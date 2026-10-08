# Privacy Policy

_Last updated: 2026_

This policy describes how **Native AI Assistant** handles your data.

## 1. Principles
We believe that personal productivity tools should prioritize user privacy. Native AI Assistant is designed from the ground up to keep your sensitive conversation and screen data on your device.

- **Audio, screen content, transcripts, notes, and meeting history** are stored locally on your device in a local SQLite database. They are **never** uploaded to any proprietary central server.
- **On-Device Speech-to-Text:** When Apple Speech or local Whisper/Moonshine models are used, audio transcription happens entirely on your machine.
- **Third-Party AI Services (BYOK):** If you provide API keys for third-party cloud AI providers (e.g., Google Gemini, OpenAI, Anthropic, Groq, Deepgram), only the necessary audio or prompt context is sent directly to those APIs to generate responses.
- **Local AI (Ollama):** You can use local LLM inference engines (such as Ollama or LocalAI) for a 100% offline and private workflow with no network requests.

## 2. Source Code & Openness
The application source code is available on GitHub at <https://github.com/jeevanjames2000/native-ai-cluely-assistant> under the [MIT License](LICENSE). You are welcome to audit the source code to verify all privacy assertions.

## 3. Contact
For any questions regarding privacy, please open an issue on the GitHub repository.
