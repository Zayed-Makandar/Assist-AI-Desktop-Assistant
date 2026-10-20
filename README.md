# Assist: AI Desktop Assistant

A voice-activated AI assistant for Windows. Say the wake word, speak a request, and Assist either answers with GPT-4o or acts on your PC: opening apps, playing music and videos, fetching weather and news, or typing into Word.

![Python](https://img.shields.io/badge/Python-3.10-3776AB?logo=python&logoColor=white)
![PyQt](https://img.shields.io/badge/PyQt-desktop_UI-41CD52?logo=qt&logoColor=white)
![OpenAI](https://img.shields.io/badge/OpenAI-GPT--4o-412991?logo=openai&logoColor=white)
![Porcupine](https://img.shields.io/badge/Porcupine-wake_word-1f2937)
![Spotify](https://img.shields.io/badge/Spotify_Web_API-1DB954?logo=spotify&logoColor=white)
![PyInstaller](https://img.shields.io/badge/PyInstaller-Windows_app-1f2937)

> **Note:** The source code is private. This repository documents the features and design. I'm happy to walk through the implementation in an interview.

**Built:** May–June 2025.

<img alt="Assist main window: a chat between the user and the assistant, with the assistant introducing itself" src="assets/screenshots/01-main-window.png" width="380">

---

## Overview

Everyday computer tasks still mean clicking through menus: opening an app, finding a song, checking the weather, starting a document. Assist puts these behind a single wake word. A spoken or typed request is first matched against known commands, which run instantly on the PC; anything else goes to GPT-4o, and the answer appears in a chat window and is read aloud.

## Key Features

- **Hands-free activation:** a Porcupine wake-word detector starts listening, speech recognition turns the request into text, and replies are spoken with on-device text-to-speech. Typed messages work too.
- **AI conversation:** open questions go to GPT-4o through the OpenAI API, behind a provider layer that keeps the model swappable.
- **Desktop actions:** open and close applications by voice (for example, "open calculator").
- **Media:** play a requested song on Spotify, or find a YouTube video with the YouTube Data API and start it automatically.
- **Live information:** the time, current weather, the latest news headlines and a joke on request.
- **Office automation:** on command, Assist opens Microsoft Word and types into a new document through COM automation.
- **Polished desktop UI:** chat bubbles with a typing indicator, toast notifications, chat history, a profile page and settings for light or dark theme and font size, packaged as a standalone Windows app.

## Screenshots

| | |
|---|---|
| ![Opening an app by voice](assets/screenshots/02-open-app-command.png) | ![The assistant introducing itself](assets/screenshots/03-ai-reply.png) |
| *"Open calculator": a desktop action with a confirmation toast* | *An AI reply, spoken and shown in the chat* |
| ![Menu](assets/screenshots/04-menu.png) | ![Settings](assets/screenshots/05-settings.png) |
| *Menu: history, profile and settings* | *Theme and font-size settings* |

![Chat history](assets/screenshots/06-chat-history.png)
*Chat history panel*

## Architecture

```mermaid
flowchart TB
    Mic["Microphone"] --> Wake["Wake-word detection<br/>Porcupine"]
    Wake --> STT["Speech recognition"]
    Typed["Typed message"] --> Parser
    STT --> Parser{"Command parser<br/>and router"}
    Parser -->|"system command"| Exec["Action executor<br/>open / close apps · Word"]
    Parser -->|"media request"| Media["Spotify playback<br/>YouTube search + autoplay"]
    Parser -->|"live info"| Info["Weather · news · jokes"]
    Parser -->|"anything else"| AI["AI provider layer<br/>GPT-4o"]
    Exec --> Reply["Chat window<br/>bubbles · toasts · history"]
    Media --> Reply
    Info --> Reply
    AI --> Reply
    Reply --> TTS["Spoken reply<br/>pyttsx3, listening paused while speaking"]
```

## How It Works

1. The wake-word detector listens in the background; once triggered, speech recognition captures the request.
2. A command parser checks whether the request is a known action, such as opening an app, playing media or fetching live information.
3. Known actions run directly: apps launch through the operating system, songs play through the Spotify Web API and videos open from a YouTube search.
4. Everything else is sent to GPT-4o with an assistant persona.
5. The response appears in the chat window and is read aloud. Listening pauses while Assist speaks, so it doesn't hear itself.

## Tech Stack

| Category | Tools |
|---|---|
| Language | Python |
| Desktop UI | PyQt (chat window, dialogs, themes, animations) |
| Voice | Porcupine wake word, SpeechRecognition, PyAudio, pyttsx3 |
| AI | OpenAI API (GPT-4o) behind a pluggable provider layer |
| Integrations | Spotify Web API (spotipy, OAuth token refresh), YouTube Data API, Open-Meteo weather, GNews headlines, Microsoft Word automation |
| Packaging | PyInstaller (standalone Windows executable) |

## Engineering Decisions

- **Commands before the LLM:** recognised commands run immediately without an API call, which is faster and cheaper; only open-ended requests go to the model.
- **Swappable AI provider:** the assistant talks to a provider interface rather than to one SDK, so the model can change without touching the UI.
- **Don't listen to yourself:** speech recognition pauses while the assistant is speaking and resumes when it finishes.
- **Long-lived Spotify access:** the Spotify integration refreshes its OAuth token automatically, so playback keeps working without signing in again.

## My Role

I designed and built Assist on my own: the voice pipeline, the command parser and router, the AI provider layer, the integrations and the PyQt desktop interface.

## Contact

**Zayed Makandar** | [zayedmakandar03@gmail.com](mailto:zayedmakandar03@gmail.com) | [LinkedIn](https://www.linkedin.com/in/zayed-makandar-642536422/) | [Portfolio](https://zayedmakandar.com)
