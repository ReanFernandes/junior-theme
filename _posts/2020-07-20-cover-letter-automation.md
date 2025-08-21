---
layout:     post
title:      My protest against Paid Cover letter generators. 
date:       2025-07-20
summary:    Online tools should not hide basic features behind a paywall. So I built my own.
categories: development python automation
---

If you've spent any time on the job hunt, you've probably used an online cover letter builder. I certainly did. Services like [FlowCV](https://flowcv.com) and others are great at first, offering slick templates and a simple way to create professional-looking documents. But a frustrating trend has emerged: features that were once free are now disappearing behind paywalls.

### The Motivation: A Frustrating Paywall

I hit my breaking point when I tried to create a second version of a cover letter. A pop-up appeared, asking me to upgrade to a premium plan to save and manage multiple documents. This felt wrong. The ability to create more than one cover letter is not a "premium" feature; it's a fundamental need.

All I wanted to do was take the text I had already written and place it into a consistent, well-designed template. The idea that this simple act of conversion should be a recurring subscription cost was the motivation for this project.

### Building a Better Way

Frustrated, I decided to build my own solution. As a developer, I knew I could create a simple, local, and infinitely flexible tool that did exactly what I needed, without any hidden costs. The goal was to create a script that would take a plain text file and merge it into a beautiful HTML template, then convert it to a PDF.

The result is this **Text 2 PDF Cover Letter Generator**. It’s a transparent, open-source tool that puts you in control.

### How It Works

The project is intentionally simple. There's no complex backend or database, just a few key files working together:

* **`my_info.json`**: This file holds your personal details, like your name, contact information, and social media links. You set it up once, and the script pulls from it for every cover letter.
* **`template.html`**: A clean, customizable HTML template that defines the structure and style of your final PDF. It uses Jinja2 syntax to insert your personal info and the cover letter body.
* **`content/`**: This is where you write. Each cover letter body is just a simple `.txt` file. This separates your writing from the formatting, allowing you to focus purely on the words.
* **`generate_pdf.py`**: The Python script is the core of the project. It reads your information, grabs the correct body text, renders the HTML template, and uses the WeasyPrint library to generate a polished PDF.

With a single command, you can generate a new, perfectly formatted cover letter:
```bash
python generate_pdf.py --company "A Great Company" --body body_great_company.txt
```
### What the cover letter looks Like

![Example image of what the cover letter looks like after generation]({{ 'images/cover_letter_post/CoverLetter-ReanFernandes_LoremIpsum_page-0001.jpg' | relative_url }})
## Free, Forever
This tool is my small protest against the trend of monetizing basic productivity. It's for anyone who believes that you shouldn't have to subscribe to a service just to format a document.

## Possible updates (?)
I cannot find the time to think of enhancements, but was considering making it possible to add more templates. For the timebeing, I'm happy with the way it looks, I would add those in as I feel necessary :)

Feel free to check out the [GitHub repository](https://github.com/ReanFernandes/cover_letter_text2pdf), use the tool for yourself, and adapt it to your needs.