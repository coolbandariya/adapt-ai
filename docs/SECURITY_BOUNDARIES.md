# Security boundaries

Adapt AI is designed for local inference, but local execution does not automatically make every feature safe or private.

## Trust boundaries
- The browser is an untrusted client. Validate requests again in the FastAPI backend.
- Ollama is a local model service; model output is untrusted content and must not be treated as executable instructions.
- File attachments may contain sensitive information. Keep temporary files scoped and clean them up on success and failure.
- Python execution is a high-risk capability. It should be isolated, resource-limited, and never exposed as an unrestricted remote execution endpoint.
- Hardware telemetry can reveal device characteristics; collect and display only what the feature needs.

## Review checklist
- [ ] Keep secrets out of source, browser bundles, logs, and error messages.
- [ ] Bound upload size and reject unsupported file types.
- [ ] Apply timeouts and resource limits to model and code-execution operations.
- [ ] Escape model output before inserting it into HTML.
- [ ] Ensure one user's sessions and uploaded files cannot be read by another user.
- [ ] Explain which operations require internet access, including model downloads.

Update this document whenever a feature changes a trust boundary. Run the repository CI checks before merging.