# Local AI Verification, Privacy, and Safety

## What runs on-device

- **Chat inference:** every chat completion (`/api/chat`) is served by a
  locally-running Ollama process on your own machine. No prompt, response,
  or chat history is sent to any cloud AI API.
- **Hardware profiling:** `/api/metrics` and `/api/benchmark` read your
  system's own RAM (`psutil`) and VRAM (`GPUtil`, with an `nvidia-smi`
  fallback on Linux) directly from the host machine.
- **Code execution:** `/api/execute` is disabled by default. If you explicitly set
  `ADAPT_ENABLE_CODE_EXECUTION=true`, submitted Python runs in a local subprocess;
  however, this is not a security sandbox and must only be used with trusted code.

## What requires internet

- **Model downloads:** The first time you select a model that isn't already
  pulled, Ollama downloads its weights from its own model library. This is
  the only outbound network call the app makes as part of its core
  functionality. Once a model is on disk, chatting with it works fully
  offline.

## Does any user data leave the device?

Chat messages, attached files, and hardware telemetry stay local. Code execution is
not available unless you explicitly opt in with `ADAPT_ENABLE_CODE_EXECUTION=true`;
when enabled, the submitted code and output stay on the host but should be treated
as untrusted host-level execution. The app does not call any third-party analytics, logging, or AI
API service.

## Data handling and storage

- Chat session history is kept in the browser's in-memory JS state for the
  current session; it is not written to disk by the backend and is not
  persisted anywhere once the browser tab is closed, beyond what the browser
  itself may cache.
- `/api/execute` is disabled by default. When explicitly enabled, submitted code is
  passed directly to a subprocess and is not intentionally persisted by the app.
- No user accounts, authentication, or telemetry collection exist in this
  project.

## Permissions

The app itself doesn't request any OS-level permissions beyond what a normal
local web server and subprocess launcher need (opening a port, spawning
`ollama` and `python` subprocesses). The API binds to `127.0.0.1` by default. File attachments in chat are handled
client-side in the browser; see `js/app.js` for exactly how attached files
are read and sent to `/api/chat`.

## Known risks

See the "Known limitation(s)" section in [README.md](README.md). The short
version is that code execution is disabled by default. If enabled, it is only
a subprocess with a timeout; it is not a container/seccomp sandbox, so it must
never be used for untrusted code or exposed on a shared/public network.
