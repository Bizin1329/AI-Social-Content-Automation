# AI Social Content Automation

AI-powered n8n workflow that analyzes company information, creates a structured 5-day social media content plan, generates post copy and images, and publishes the content to a Telegram channel.

<img width="1677" height="465" alt="2026-09-26_16-51-25" src="https://github.com/user-attachments/assets/64d11f6f-cbf7-46a0-80aa-d8ace1446a05" />


## Overview

This project automates the end-to-end content creation workflow for social media.

The workflow takes company information from Google Sheets, analyzes the business with an AI agent, creates a structured content plan, generates the copy and visual prompt for each post, creates an image, publishes the content to Telegram, and tracks publication status in Google Sheets.

## Workflow

Google Sheets
     ↓
Company Analysis
     ↓
Structured Company Profile
     ↓
5-Day Content Plan
     ↓
Google Sheets
     ↓
Content Loop
     ├── Text Generation
     ├── Image Generation
     ├── Telegram Publishing
     └── Publication Tracking

## What It Does

The workflow:

1. Reads company information from Google Sheets.
2. Analyzes the company using an AI agent.
3. Creates a structured company profile.
4. Generates exactly five content ideas for five days.
5. Assigns each post a topic, content pillar, goal, text task, and image task.
6. Stores the generated content plan in Google Sheets.
7. Processes each content item sequentially.
8. Generates a ready-to-publish social media post using an LLM.
9. Generates a single image for each post using a multimodal image model.
10. Converts the generated image into a binary file.
11. Publishes the image and generated text to a Telegram channel.
12. Updates the corresponding Google Sheets row as published.

## AI Content Pipeline

The workflow uses multiple AI stages instead of generating the final content in a single request.

### 1. Company Analyst

The first AI agent converts raw company information into a structured profile containing:

* Company name
* Industry
* Description
* Target audience
* Products
* Services
* Benefits
* Differentiators
* Tone of voice
* Key facts
* Content restrictions

The prompt explicitly limits the model to information provided in the source data and instructs it not to invent unsupported facts.

### 2. Content Plan

The second AI agent creates exactly five content items.

Each item contains:

* Day
* Topic
* Content pillar
* Goal
* Text task
* Image task

The workflow uses a Structured Output Parser to enforce the expected JSON structure.

### 3. Text Generation

A dedicated LLM generates the final social media copy from the structured text task.

The generation prompt instructs the model to:

* use only verified information from the content brief
* follow the company's tone of voice
* avoid unsupported claims
* return only the final post text

### 4. Image Generation

A separate image-generation step receives the image task created by the content planning agent.

The workflow requests one image per post and then extracts the generated image from the API response.

## Tech Stack

* **n8n** — workflow orchestration and automation
* **OpenAI GPT-5 Nano** — company analysis and content planning
* **OpenAI-compatible LLM API** — text and image API integration
* **Gemini 3.1 Flash-Lite Image** — image generation
* **Google Sheets** — company input, content calendar, and publication tracking
* **Telegram Bot API** — automated publishing
* **JavaScript** — image response processing and Base64-to-binary conversion
* **Structured Output Parser** — structured AI responses

## Google Sheets

Google Sheets is used as the workflow's content management layer.

The workflow uses the spreadsheet to:

* provide company information
* store the generated 5-day content plan
* store text and image tasks
* track whether a post has been published

Each content row contains fields such as:

Day
Topic
Content pillar
Goal
Text task
Image task
Published

The `Published` field is updated after the content has been successfully sent to Telegram.

## Telegram Publishing

The workflow publishes generated content to a Telegram channel using the Telegram Bot API.

For each content item it:

1. Generates the post text.
2. Generates the image.
3. Sends the image to Telegram.
4. Sends the generated text.
5. Marks the corresponding row as published in Google Sheets.

The current implementation sends the image and post text as separate Telegram messages.

## Content Safety and Consistency

The AI prompts contain explicit restrictions against inventing:

* products
* services
* prices
* statistics
* locations
* product characteristics
* company facts
* unsupported advantages

The content planner also requires exactly five posts and validates that all days from 1 to 5 are present.

## Error Prevention

The workflow uses structured AI output schemas for:

* Company Analysis
* Content Plan

This makes it easier to pass predictable data between AI nodes and downstream automation steps.

The image-generation response is also validated before converting the image from Base64 data into a binary file.

## Workflow Structure

### Company Analysis

Google Sheets
      ↓
Company Analyst
      ↓
Structured Output Parser

### Content Planning

Company Profile
      ↓
Content Plan
      ↓
Structured Output Parser
      ↓
Split Content Plan
      ↓
Google Sheets

### Post Generation

Loop Over Items
      ↓
Text Generator
      ↓
Edit Fields
      ↓
Image Generator
      ↓
Image Processing
      ↓
Telegram
      ↓
Google Sheets Update
      ↓
Next Content Item

## Setup

### 1. Import the workflow

Import:

workflow/ai-social-content-automation.json

into n8n.

### 2. Configure Google Sheets

Connect your own Google Sheets credential.

Create a spreadsheet containing the company information and a sheet for the content calendar.

Update the spreadsheet URL and sheet names in the Google Sheets nodes.

### 3. Configure AI credentials

Connect your own OpenAI-compatible API credentials to the AI nodes.

The repository must not contain API keys or authentication secrets.

### 4. Configure Telegram

Create or use a Telegram bot and add it to the target channel with permission to publish messages.

Connect your own Telegram credentials in n8n and set the target chat ID.

### 5. Run the workflow

Start the workflow using the Manual Trigger.

The workflow will:

* analyze the company
* generate a 5-day content plan
* create post copy
* generate images
* publish the content
* update publication status

## Purpose

This project demonstrates how n8n can orchestrate multiple AI stages, structured data processing, image generation, Google Sheets, and Telegram into an automated social media content pipeline.
