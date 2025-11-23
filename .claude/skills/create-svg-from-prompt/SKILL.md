---
name: create-svg-from-prompt
description: Generate an SVG of a user-requested image or scene
---

## Core Workflow

When the user prompts the model to generate an SVG of and image or scene:

### User wants an SVG of an image or scene

To generate an SVG of an image or scene use Google Gemini and autotrace:

```bash
curl -s -X POST "https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-image:generateContent" -H "x-goog-api-key: <GEMINI_API_KEY>" -H "Content-Type: application/json" -d '{ "contents": [{ "parts": [ {"text": "<IMAGE_PROMPT>"}]}]}' | grep -o '"data": "[^"]*"' | cut -d'"' -f4 | base64 --decode | autotrace -output-format svg -color-count 10 -corner-threshold 60 -line-threshold 0.5 -filter-iterations 4 -despeckle-level 2 -despeckle-tightness 2.0 -error-threshold 2.0 -output-file <OUTPUT_FILE>.svg
```

**Arguments from prompt:**
- `<GEMINI_API_KEY>`: The Gemini API key gotten from the environment variable "GEMINI_API_KEY"
- `<PROMPT>`: A Gemini image prompt generated from the user's requested scene
- `<OUTPUT_FILE>`: An 8-30 character filename based on the image the user wants

