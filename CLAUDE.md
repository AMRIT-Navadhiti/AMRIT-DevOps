# CLAUDE.md - AMRIT-DevOps

## Project Overview

AMRIT-DevOps contains infrastructure scripts, Docker Compose configurations, and observability setup for running the AMRIT platform locally and managing deployments. It is not a buildable application but rather a collection of DevOps tooling.

## Repository Structure

```
amrit-local-setup/          # Docker Compose local development environment
  docker-compose.yml        # Full-stack compose (APIs + MySQL + Redis + MongoDB)
  init.sql                  # Creates the 4 AMRIT databases
  my.cnf                    # MySQL configuration
  loaddummydata.sh          # Script to load sample data (Linux/Mac)
  loaddummydata.bat         # Script to load sample data (Windows)
  README.md                 # Local setup instructions

ELK/                        # Elastic Stack observability
  SETUP.md                  # ELK setup instructions
  apm-server.yml            # APM Server configuration
  filebeat.yml              # Filebeat log shipping config
  http_ca.crt               # Elasticsearch CA certificate
  apm_agent/                # Elastic APM Java agent
    elastic-apm-agent.jar   # APM agent JAR
    elasticapm.properties   # APM agent configuration
```

## Local Development Setup

The `docker-compose.yml` orchestrates the following services:

| Service | Port | Source |
|---------|------|--------|
| Common-API | 8083 | `../../Common-API` |
| Identity-API (main) | 8094 | `../../Identity-API` |
| Identity-API (profile) | 8095 | `../../Identity-API` |
| BeneficiaryID-Generation-API | 8092 | `../../BeneficiaryID-Generation-API` |
| TM-API | 8089 | `../../TM-API` |
| Admin-API | 8082 | `../../Admin-API` |
| Scheduler-API | 8088 | `../../Scheduler-API` |
| AMRIT-DB | 8999 | `../../AMRIT-DB` |
| FHIR-API | 8093 | `../../FHIR-API` |
| ECD-API | 8084 | `../../ECD-API` |
| MySQL 8.0 | 3306 | Official image |
| Redis 7.2 | 6379 | Official image |
| MongoDB 6.0 | 27017 | Official image |

### Running Locally

```bash
cd amrit-local-setup
docker compose up --build
```

- MySQL initializes with `init.sql` creating databases: `db_iemr`, `db_reporting`, `db_identity`, `db_1097_identity`
- AMRIT-DB runs Flyway migrations after MySQL is healthy
- MySQL root password: `1234` (local dev only)
- MongoDB root credentials: `root` / `1234` (local dev only)

## ELK Observability

- **Elastic APM Agent**: Attach to Java APIs via `-javaagent:elastic-apm-agent.jar`
- **Filebeat**: Ships application logs to Elasticsearch
- **APM Server**: Receives APM data from agents
- See `ELK/SETUP.md` for detailed setup instructions

## Notes

- Docker Compose expects sibling directories (`../../<API-name>`) for building API images
- Each API has its own Dockerfile in its repository root
- No CI/CD workflows in this repo; it is purely operational tooling
