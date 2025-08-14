# Deploying VProfile Multi-Tier Web Application on a Local Machine

## 📖 Project Overview
This project demonstrates the **manual deployment** of the **VProfile multi-tier Java web application** on a local environment using **Vagrant-managed Virtual Machines**.  
Each core service runs in its own VM, mimicking a production-like multi-tier architecture while remaining fully local.  

The setup includes:
- **Nginx** as a reverse proxy web server  
- **Tomcat** as the Java application server  
- **RabbitMQ** for asynchronous messaging  
- **Memcached** for database query caching  
- **Elasticsearch** for indexing and search  
- **MySQL** as the relational database


This deployment is **entirely manual** (no automation tools), providing hands-on experience with service installation, configuration, networking, and integration in a DevOps context.

---

## 🔧 Function of Each Service

| Service       | Purpose |
|---------------|---------|
| **Nginx**     | Acts as a reverse proxy, receiving client requests on port 80 and forwarding them to Tomcat. |
| **Tomcat**    | Runs the Java-based VProfile web application (`ROOT.war`) and handles core business logic. |
| **RabbitMQ**  | Manages message queues for asynchronous processing, enabling loose coupling between components. |
| **Memcached** | Stores frequently accessed data in memory to reduce MySQL query load and improve performance. |
| **Elasticsearch** | Indexes and retrieves search data quickly for the application's search functionality. |
| **MySQL**     | Stores persistent relational data, including user accounts and application records. |

---

## 🖼 Architecture Explanation

The architecture consists of **six VMs** provisioned by Vagrant:

1. **Web Tier** → Nginx (`web01`) handles HTTP requests and forwards them to Tomcat.  
2. **Application Tier** → Tomcat (`app01`) executes Java code, communicates with MySQL, Memcached, RabbitMQ, and Elasticsearch.  
3. **Database Tier** → MySQL (`db01`) stores application data.  
4. **Caching Tier** → Memcached (`mc01`) speeds up data retrieval by caching query results.  
5. **Messaging Tier** → RabbitMQ (`rmq01`) handles background message processing.  
6. **Search Tier** → Elasticsearch indexes and serves search queries.

**Request Flow**:  
User → Nginx → Tomcat → (MySQL / Memcached / RabbitMQ / Elasticsearch) → Response via Nginx

This design:
- Improves **scalability** by separating services  
- Enhances **performance** with caching and search indexing  
- Enables **fault isolation** — one tier failure doesn’t crash the whole app  

---

## 📋 Prerequisites
Before starting, install:
- Oracle VM VirtualBox
- Vagrant
- Vagrant Hostmanager plugin  
  ```bash
  vagrant plugin install vagrant-hostmanager
 ## 📖 🚀 Tomcat Deployment Script
  #!/bin/bash
# deploy.sh - Deploy WAR file to Tomcat

# Stop Tomcat
systemctl stop tomcat

# Remove old deployment
rm -rf /usr/local/tomcat/webapps/ROOT*

# Copy new WAR file
cp target/vprofile-v2.war /usr/local/tomcat/webapps/ROOT.war

# Set ownership
chown tomcat.tomcat /usr/local/tomcat/webapps -R

# Start Tomcat
systemctl start tomcat

# Restart for clean state
systemctl restart tomcat

echo "Deployment completed successfully."
