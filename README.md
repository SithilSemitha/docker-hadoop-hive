

# 🐳 Kerberized Apache Hadoop & Apache Hive Cluster

A fully containerized, secure big data infrastructure leveraging Docker and Docker Compose. This project provisions a Kerberized Apache Hadoop and Apache Hive cluster, designed for backend engineers who need to quickly deploy, test, and build upon foundational data systems without the overhead of manual, bare-metal configuration.

## 🏗️ Architecture & Features

This repository acts as the main engine room for local big data development, offering:
* **Containerized Deployment:** Entire Hadoop ecosystem isolated in Docker containers.
* **Robust Security:** Fully integrated Kerberos (KDC) authentication out-of-the-box.
* **Modular Structure:** Clean separation of concerns between Hadoop, Hive, and Kerberos services.
* **Automated Operations:** A `Makefile` is included to streamline image building, cluster orchestration, and teardown.
* **Environment Configuration:** Managed entirely via a `.env` file for easy overrides.

## 📂 Project Structure

```text
.
├── hadoop/               # Dockerfiles and configuration scripts for HDFS and YARN components
├── hive/                 # Dockerfiles and configuration scripts for Hive Metastore & Server2
├── kerberos/             # KDC (Key Distribution Center) server setup and keytab generation
├── .env                  # Environment variables for cluster configuration (ports, versions)
├── docker-compose.yml    # Service orchestration and networking
├── Makefile              # Automation commands for fast cluster management
└── README.md

```

## ⚙️ Prerequisites

To run this cluster, ensure you have the following installed on your host machine:

* [Docker](https://docs.docker.com/get-docker/)
* [Docker Compose](https://docs.docker.com/compose/install/)
* `make` utility (optional, but highly recommended for streamlined commands)

## 🚀 Getting Started

### 1. Clone the Repository

```bash
git clone [https://github.com/SithilSemitha/docker-hadoop-hive.git](https://github.com/SithilSemitha/docker-hadoop-hive.git)
cd docker-hadoop-hive

```

### 2. Configure the Environment

Check the `.env` file to ensure the default variables (like network subnets, container names, and Hadoop/Hive versions) match your requirements. Modify them if necessary.

### 3. Build the Images

Use the included `Makefile` to compile the Docker images. This process will build the KDC server, Hadoop base images, and Hive components.

```bash
make build
# Alternatively: docker-compose build

```

### 4. Start up the Cluster

Spin up the foundational infrastructure in detached mode:

```bash
make up
# Alternatively: docker-compose up -d

```

*Note: Wait a few moments for the KDC server to initialize and distribute the keytabs before the Hadoop and Hive services become fully healthy.*

## 🔐 Authentication (Kerberos)

Because this cluster is secured with Kerberos, you must obtain a Ticket-Granting Ticket (TGT) before interacting with HDFS or Hive.

To authenticate within a client container (e.g., the NameNode or an Edge node):

```bash
# Exec into the container
docker exec -it <namenode_container_name> bash

# Initialize your Kerberos ticket (using the configured admin principal)
kinit -kt /etc/security/keytabs/admin.keytab admin/admin@YOUR.REALM.COM

# Verify the ticket
klist

```

## 🌐 Standard Web Interfaces & Ports

Once the cluster is running, you can monitor the backend infrastructure via the following default UIs (assuming localhost mapping in `docker-compose.yml`):

* **HDFS NameNode UI:** `http://localhost:9870`
* **YARN Resource Manager UI:** `http://localhost:8088`
* **HiveServer2:** `jdbc:hive2://localhost:10000/default`

*(If you adjusted the exposed ports in your `.env` or `docker-compose.yml` file, use those instead).*

## 🧹 Teardown

To shut down the cluster and remove the containers, networks, and optionally volumes:

```bash
# Stop and remove containers
make down
# Alternatively: docker-compose down

# To completely wipe the data and start fresh:
docker-compose down -v

```

## 📄 License

This project is licensed under the [Apache-2.0 License](https://www.google.com/search?q=LICENSE).
