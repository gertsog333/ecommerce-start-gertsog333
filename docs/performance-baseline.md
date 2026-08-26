# Performance Baseline — Module 01 (chat-agent)

Measured on: VirtualBox VM (8GB RAM / 4 vCPU), Docker 29.7.2, Docker Compose v5.5.0.

| Metric | Value |
|---|---|
| Docker image build time (cold, `--build`) | ~142s |
| Container (re)start time (image cached, no rebuild) | 0.67s |
| First HTTP response (`curl` to `/`) | 3ms, HTTP 200 |
| Memory usage at idle | 35.9 MiB (0.45% of 7.75GiB) |
| CPU usage at idle | 0.10% |
| Docker image size | ~189MB (compressed layer) |

## Notes

- These numbers reflect the current Module 01 scope: a single lightweight Streamlit container doing in-memory pandas keyword search over a 100-row CSV. Startup and memory footprint are expected to grow substantially once ML/RAG/Agent services, MLflow, and a vector DB are added in later modules.
- Search response time wasn't separately measured via HTTP, since the app is a stateful Streamlit session (WebSocket-based UI interaction, not a REST endpoint) — functional correctness was verified manually instead (see screenshots).
- The VM comfortably ran this service alongside the Module 01 Jupyter container (telco-churn project) at the same time, with combined memory well under 200 MiB — confirming the 8GB/4-core VM has ample headroom for this stage of the course.
