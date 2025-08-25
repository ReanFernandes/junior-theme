---
layout:     post
title:      A Database-Driven Pipeline for Managing SLURM Job Queues
date:       2025-04-14
summary:    An overview of a custom, on-cluster pipeline that uses a persistent database to automate and optimize SLURM job submissions on HPC clusters with queue limits.
categories: development python automation hpc slurm
---

## Introduction

For researchers using High-Performance Computing (HPC) clusters, user-specific limits on job submissions can be a significant bottleneck. When running large-scale experiments, it's common to hit these queue limits, leading to a tedious cycle of manually monitoring and submitting jobs. To address this, I developed an on-cluster pipeline that uses a persistent SQLite database to automate the entire job management process, ensuring the cluster's resources are used as efficiently as possible.

This post provides a brief technical overview of the system's architecture and workflow.

---

## A Self-Managing, On-Cluster Solution

The key difference in this approach is that it operates entirely **on the cluster**, rather than relying on a client-side tool on a local machine. It's a self-contained system where the SLURM jobs themselves are responsible for perpetuating the queue.

The system is built around three core components:

* **A central SQLite database (`jobs.db`)** that acts as the single source of truth for the state of all jobs.
* **A collection of Python scripts** for managing the database, generating job configurations, and handling submissions.
* **The SLURM jobs themselves**, which have a final, crucial step: triggering the next job in the queue.

### How It Works

The workflow is designed to be a "fire-and-forget" process:

1.  **Initialization**: First, a configuration script (`config_generator.py`) is used to define the hyperparameter space for the entire experiment. Then, a master script (`submit_jobs.py`) populates the SQLite database with every job permutation, each marked as `pending`. This script then submits the first batch of jobs to SLURM.

2.  **Execution and Chaining**: Each SLURM job runs its primary task (e.g., training a model). Once the task is complete, the job executes a final command: a call to the `check_queue.py` script.

3.  **Database-Driven Submission**: The `check_queue.py` script is the heart of the automation. It connects to the SQLite database, finds the next job with a `pending` status, and submits it to the SLURM queue.

This creates a self-sustaining loop. As soon as one job finishes, it ensures the next one is submitted, keeping the job queue full without any need for manual intervention.

---

## Key Advantages

This database-centric architecture offers a couple of significant benefits:

* **Fault Tolerance and Resumability**: Because the state of every job (`completed`, `failed`, `pending`) is stored persistently, the entire pipeline is robust. If there's an interruption, a dedicated `resubmit_failed_jobs.py` script can be run to intelligently find and rerun only the jobs that failed, saving considerable time and resources.

* **Efficient and Daemon-Free**: The system is elegant because it doesn't require a separate, long-running daemon to monitor the queue. This event-driven approach is a key design choice. **It eliminates the need for a separate, threaded process to constantly poll the database, which saves precious CPU resources and, more importantly, sidesteps potential race conditions or locking issues.** Database access is brief and transactional, occurring only when a job's state changes (e.g., upon completion or failure).

While this system was built for a specific machine learning workflow, its core logic—using a persistent database to manage a self-perpetuating job chain—is a flexible pattern. With some adjustments to the configuration and management scripts, this approach could be adapted for a wide range of complex, dependent job workflows in any SLURM-managed environment.

For those interested in the implementation details, the source code is available on [GitHub](https://github.com/ReanFernandes/bar-llama/tree/master/SLURM-related-scripts).
