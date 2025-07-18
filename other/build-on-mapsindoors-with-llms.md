---
icon: robot
cover: ../.gitbook/assets/llm.jpg
coverY: 0
layout:
  width: default
  cover:
    visible: true
    size: hero
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# Build on MapsIndoors with LLMs

Large Language Models (LLMs) process plain text formats more effectively than web-based formats like HTML and CSS. To optimize our documentation for LLMs, you can append specific suffixes that transform the content into LLM-friendly formats such as plain text or markdown.

LLM-optimized documentation ensures that AI systems like ChatGPT, Claude, Cursor, and Copilot can retrieve and provide accurate, contextual responses about your product or API.

## Markdown

Add `.md` to the URL to serve the page as a markdown file. The markdown file will be much easier for your chosen LLM to work with, than directly linking to the URL of our documentation.\
\
Example: [https://docs.mapsindoors.com/sdks-and-frameworks/web.md](https://docs.mapsindoors.com/sdks-and-frameworks/web.md)

<figure><img src="../.gitbook/assets/Clipboard-20250716-090636-415.gif" alt=""><figcaption><p>You can also quickly access both the markdown file and open it directly in ChatGPT or Claude from this dropdown.</p></figcaption></figure>

## llms.txt

`llms.txt` is a proposed standard for making web content available in text-based formats that are easier for LLMs to process. The `llms.txt` file contains an index of all the page URLs and titles of our documentation. You can find that here: [https://docs.mapsindoors.com/llms.txt](https://docs.mapsindoors.com/llms.txt).&#x20;

You can also fetch the full content of our documentation in one file that can be passed to LLMs as context. That is available here: [https://docs.mapsindoors.com/llms-full.txt](https://docs.mapsindoors.com/llms-full.txt).
