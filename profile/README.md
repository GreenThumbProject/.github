# GreenThumb

**A low-cost automated hydroponic grow system.**

## About

GreenThumb is an automated hydroponic grow system. A Raspberry Pi 5 at each grow unit reads the sensors, runs the pumps and other actuators, and keeps working without an internet connection. Its data syncs to a cloud service when a connection is available.

GreenThumb is in its research phase: we are building a low-cost prototype for growing cherry tomatoes.

## Repositories

- [docs](https://github.com/GreenThumbProject/docs): project documentation ([read it online](https://docs.greenthumbsystems.com))

The rest of the code is private: the edge software for the Raspberry Pi, the cloud services, the database schema and our research material.

## Technology

- **Edge:** Raspberry Pi 5, Python 3.11, PostgreSQL 17, Docker Compose
- **Cloud:** Python (FastAPI), Java (Spring Boot), React, PostgreSQL 17 with TimescaleDB
- **CI/CD:** GitHub Actions builds the Docker images

## Contact

- Henrique Bucci R. Netto ([@henriquebrnetto](https://github.com/henriquebrnetto))
