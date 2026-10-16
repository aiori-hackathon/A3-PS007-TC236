# Privacy-Aware IPv6 IoT Security Monitoring Across Address Rotation

A hackathon-ready prototype for detecting suspicious IPv6 address rotation in IoT networks while preserving user and device privacy.

## 1. Problem Statement

Internet of Things (IoT) devices frequently rotate IPv6 addresses due to SLAAC, DHCPv6, privacy extensions, and network churn. While this behavior is often legitimate, it also creates a blind spot for security monitoring: attackers can impersonate trusted devices, change addresses rapidly, and evade policy enforcement that assumes a stable identity.

Traditional monitoring systems often record raw device IDs and full IPv6 addresses, creating privacy risks and operational concerns. There is a clear need for a system that can reliably detect suspicious rotation patterns while keeping identifiers pseudonymized, explainable, and safe for operators to consume.

## 2. Solution Overview

This project proposes a privacy-aware monitoring framework that:

- captures telemetry from IoT devices,
- pseudonymizes device identities using HMAC-based keys,
- stores and analyzes IPv6 rotation behavior without exposing raw identity details,
- flags repeated IPv6 address changes within a configurable monitoring window,
- provides redacted and explainable alert summaries for operators.

The system is designed for easy deployment in a hackathon environment, with a lightweight Python + FastAPI backend and SQLite persistence.

## 3. Why This Matters

Modern IoT deployments are increasingly distributed, dynamic, and privacy sensitive. Security teams need to detect malicious behavior without over-collecting personally identifying or operationally sensitive information. This project addresses both concerns at once:

- privacy preservation through pseudonymization and redaction,
- operational security through rotation-aware anomaly detection,
- explainability through human-readable alerts and summaries.

## 4. System Architecture

```text
IoT Devices / Telemetry Sources
           |
           v
   FastAPI Event Ingestion
           |
           +--> Privacy Layer
           |      - HMAC device key generation
           |      - IPv6 redaction
           |      - Safe alert formatting
           |
           +--> Monitoring Engine
           |      - rotation heuristics
           |      - risk scoring
           |
           +--> SQLite Database
           |      - telemetry history
           |      - alert records
           |
           +--> Summary / Alert API
                  - redacted overview
                  - suspicious activity alerts
``` 

## 5. Core Features

- Privacy-first pseudonymization for device identifiers
- IPv6 endpoint redaction for safe operational reporting
- FastAPI-based ingest and summary endpoints
- SQLite-backed data persistence for rapid demo deployment
- Anomaly detection for repeated IPv6 address changes within a 15-minute window
- Redacted alert payloads and explainable security insights
- Docker-ready runtime for simple setup and showcase

## 6. Technical Implementation

### Backend
- Python
- FastAPI
- SQLAlchemy
- SQLite

### Privacy Controls
- Device IDs are transformed into HMAC-SHA256 pseudonyms
- Full IPv6 addresses are stored internally for demo analysis but omitted from summary and alert API responses
- Alerts are designed to avoid leaking raw identifying data

### Security Logic
The current monitoring engine counts consecutive address-fingerprint changes
for a pseudonymized device within a 15-minute window. Two or more changes
generate a medium-severity alert with a fixed demo score and a human-readable
description. Reconnect and network-segment anomaly checks are potential future
work, not implemented detection signals.

## 7. Quick Start

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

If using Docker:

```bash
docker-compose up --build
```

## 8. API Examples

### Submit telemetry event

```bash
curl -X POST "http://localhost:8000/api/v1/events" \
  -H "Content-Type: application/json" \
  -d '{
    "device_id": "sensor-lab-42",
    "ipv6_address": "2001:db8:85a3::8a2e:370:7334",
    "network_segment": "edge-zone-a",
    "event_type": "join",
    "protocol": "udp",
    "port": 5683,
    "status": "normal"
  }'
```

### Fetch summary

```bash
curl http://localhost:8000/api/v1/summary
```

### Fetch alerts

```bash
curl http://localhost:8000/api/v1/alerts
```

## 9. Demo Flow

Run the complete walkthrough from the repository root:

```bash
pip install -r requirements.txt
python demo.py
```

The script starts the API server, sends normal and rotated-address events,
prints the summary and alerts, and leaves the API running for exploration.
Press `Ctrl+C` to stop it. To stop the server automatically after the
walkthrough (useful for automated checks), run `python demo.py --exit-after-demo`.

For a fresh run, use a dedicated demo database. In PowerShell:

```powershell
$env:DATABASE_URL = "sqlite:///./data/hackathon-demo.db"
$env:SECRET_KEY = "demo-only-change-before-use"
python demo.py --exit-after-demo
```

The detector raises an alert after two or more consecutive address changes
within 15 minutes. The demo uses documentation-only IPv6 addresses.

## 10. Demo Video

**YouTube demo video:** Pending recording and upload. Add the public or
unlisted YouTube URL here before submitting.

The local silent explainer is [`assets/aiori-3-demo-explainer.mp4`](assets/aiori-3-demo-explainer.mp4).
Use [`DEMO_RECORDING_SCRIPT.md`](DEMO_RECORDING_SCRIPT.md) for the live-demo
shot list, narration, and publishing steps. The AIORI-3 guideline asks teams
to upload the video to YouTube and add its link to the GitHub repository.

The editable hackathon pitch deck is
[`assets/AIORI-3-Hackathon-Presentation.pptx`](assets/AIORI-3-Hackathon-Presentation.pptx);
see [`PRESENTATION_NOTES.md`](PRESENTATION_NOTES.md) for presenter guidance.

## 11. Privacy and Security Considerations

This project is intentionally designed around minimal disclosure:

- raw device IDs are not retained in plain form,
- IPv6 addresses are not exposed in summaries,
- alert content is sanitized and operator-friendly,
- data is structured for demonstration and extension into production-grade security pipelines.

## 12. Evaluator Relevance

This submission aligns with modern security and IoT themes because it combines:

- IPv6 networking awareness,
- device monitoring and anomaly detection,
- privacy-preserving design,
- lightweight deployment suitable for research and hackathon demos.

## 13. Future Enhancements

- Deploy on edge gateways or smart-home environments
- Add a dashboard visualization for active devices and alerts
- Integrate threat intelligence / anomaly scoring models
- Expand to DHCPv6 and SLAAC behavior-based detection
- Add support for multi-device correlation and timeline analysis

## 14. Team / Mentor Information

- **Team Name:** Last Minute Coders
- **Team Members:** Akrit Goyal, Vedika Pathak
- **Faculty Mentor:** Sachin Kumar
- **Problem Statement Selected:** Privacy-Aware IPv6 IoT Security Monitoring Across Address Rotation
- **Problem Statement Code:** A3-PS007-TC236

## 15. License

MIT
