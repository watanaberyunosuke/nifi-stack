
# NiFi Stack Setup with Docker Compose

This project contains a complete setup for running **Apache NiFi**, **Zookeeper**, **Airflow**, **NiFi Registry**, **MinIO**, **PostgreSQL**, and **Portainer** using **Docker Compose**. The stack is designed for orchestrating data workflows and can be used in various data engineering and ETL scenarios.

## Table of Contents

- [NiFi Stack Setup with Docker Compose](#nifi-stack-setup-with-docker-compose)
  - [Table of Contents](#table-of-contents)
  - [Architecture Overview](#architecture-overview)
  - [Prerequisites](#prerequisites)
  - [Installation](#installation)
  - [Services](#services)
  - [Volumes](#volumes)
  - [Networks](#networks)
  - [Configuration](#configuration)
    - [NiFi Configuration](#nifi-configuration)
    - [NiFi Registry](#nifi-registry)
    - [PostgreSQL](#postgresql)
    - [Airflow](#airflow)
  - [Accessing Services](#accessing-services)
  - [Troubleshooting](#troubleshooting)
    - [Common Issues](#common-issues)
      - [`ERR_SSL_PROTOCOL_ERROR` or HTTPS Issues](#err_ssl_protocol_error-or-https-issues)
      - [Unable to Connect to NiFi](#unable-to-connect-to-nifi)
      - [Network Issues](#network-issues)
    - [Logs](#logs)
  - [Contributing](#contributing)

## Architecture Overview

This project provides a full stack of services required for orchestrating data pipelines:

- **NiFi**: Data flow automation and orchestration.
- **Zookeeper**: A centralized service for maintaining configuration information and providing distributed synchronization.
- **Airflow**: Workflow automation and scheduling.
- **NiFi Registry**: Provides version control for NiFi flows.
- **PostgreSQL**: A relational database for data storage.
- **MinIO**: Object storage for scalable data storage.
- **Portainer**: Docker container management through a web interface.

## Prerequisites

Before you begin, ensure you have the following installed:

- Docker: [Installation Guide](https://docs.docker.com/get-docker/)
- Docker Compose: [Installation Guide](https://docs.docker.com/compose/install/)

## Installation

To set up and run the stack:

1. Clone the repository:

   ```bash
   git clone https://github.com/watanaberyunosuke/nifi-stack.git
   cd nifi-stack
   ```

2. Build and start the Docker services:

   ```bash
   docker-compose -f nifi.yml up --build -d
   ```

3. Verify that all services are running:

   ```bash
   docker ps
   ```

## Services

| Service         | Image                         | Port Mapping  | Description                                      |
|-----------------|-------------------------------|---------------|--------------------------------------------------|
| **NiFi**        | apache/nifi:latest             | 8091:8080     | NiFi web interface                               |
| **Zookeeper**   | bitnami/zookeeper:3.8.1        | N/A           | Coordination service for NiFi                    |
| **Airflow**     | apache/airflow:2.10.0          | 8085:8080     | Airflow for orchestrating workflows              |
| **NiFi Registry**| apache/nifi-registry:latest   | 18080:18080   | NiFi Registry for version controlling flows      |
| **PostgreSQL**  | postgres:16                    | 5433:5432     | PostgreSQL database                              |
| **MinIO**       | bitnami/minio:2023             | 9000, 9001    | Scalable object storage for NiFi                 |
| **Portainer**   | portainer/portainer-ce:latest  | 9009:9000     | Docker container management UI                   |

## Volumes

The following volumes are used for persistent storage:

| Volume Name          | Path Inside Container                      | Description                                      |
|----------------------|---------------------------------------------|--------------------------------------------------|
| `airflow-data`        | `/usr/local/airflow/data`                   | Airflow data                                    |
| `postgres`            | `/data/postgres`                            | PostgreSQL data                                  |
| `portainer-data`      | `/data`                                     | Portainer container management data              |
| `nifi-registry`       | `/opt/nifi-registry/database`               | NiFi Registry database                           |

## Networks

The project uses a custom bridge network named `nifi-network` to allow communication between all the containers.

## Configuration

The project configuration files for each service are mounted as volumes from the host system. Below are some key configurations:

### NiFi Configuration

The NiFi service uses the `nifi.properties` and other configuration files from the `./nifi/conf` directory. Notable settings include:

- HTTPS and HTTP configuration for the NiFi web interface.
- Keystore settings for SSL.
- Zookeeper coordination settings for clustered NiFi nodes.
  
### NiFi Registry

The NiFi Registry uses the following files for its configuration:

- `./nifi_registry/database`: Persistent storage for NiFi registry database.
- `./nifi_registry/flow_storage`: Version-controlled flows are stored here.

### PostgreSQL

The PostgreSQL service stores its data in the `postgres` volume mapped to `/data/postgres` inside the container.

### Airflow

Airflow configuration is set via the `airflow.cfg` file, which is mounted from `./airflow/airflow.cfg`.

## Accessing Services

- **NiFi Web Interface**: `http://localhost:8091/nifi`
- **NiFi Registry**: `http://localhost:18080/nifi-registry`
- **Airflow**: `http://localhost:8085`
- **Portainer**: `http://localhost:9009`

## Troubleshooting

### Common Issues

#### `ERR_SSL_PROTOCOL_ERROR` or HTTPS Issues

Ensure that:
- The keystore is correctly mounted in the NiFi container.
- The keystore password and key password match what was used during certificate creation.

#### Unable to Connect to NiFi

- Check that the correct ports are exposed in your `docker-compose.yml`.
- Verify that all services are running with `docker ps`.
  
#### Network Issues

Run `docker network inspect nifi-stack_nifi-network` to inspect the Docker network and ensure containers are attached.

### Logs

You can access container logs using:

\`\`\`bash
docker logs <container_name>
\`\`\`

For example, to see NiFi logs:

\`\`\`bash
docker logs nifi_container
\`\`\`

## Contributing

If you find any issues or have suggestions, feel free to open an issue or submit a pull request. Contributions are always welcome!
