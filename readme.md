# Setup End-to-End Web Application with Docker

## Table of Contents
- [Introduction](#introduction)
- [Prerequisites](#prerequisites)
- [Setup on Local Machine](#setup-on-local-machine)
- [Setup on EC2 Instance](#setup-on-ec2-instance)
- [MariaDB Setup (Back-End)](#mariadb-setup-back-end)
- [WordPress Setup (Front-End)](#wordpress-setup-front-end)
- [Verify and Test](#verify-and-test)

---

## Introduction
This guide outlines the steps to set up a web application with Docker. The application consists of:
- **MariaDB** (Back-End): A high-performing, open-source relational database server.
- **WordPress** (Front-End): A free and open-source content management system (CMS).

---

## Prerequisites
1. An Ubuntu machine (Local or EC2 instance).
2. Docker and optionally Docker Compose installed.
3. Open ports: `22`, `80`, and `443` for EC2.

---

## Setup on Local Machine

### 1. Install Docker
Run the following commands:
```bash
sudo apt-get update
sudo apt-get install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker $USER
```

### 2. Verify Installation
```bash
docker --version
```

---

## Setup on EC2 Instance

### 1. Launch an EC2 Instance
- Use an **Ubuntu 20.04/22.04** AMI.
- Configure the Security Group to allow **ports 22, 80, and 443**.

### 2. Install Docker
Run the following commands:
```bash
sudo apt-get update
sudo apt-get install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker ubuntu
```

### 3. Install Docker Compose (Optional)
```bash
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version
```

---

## MariaDB Setup (Back-End)

### 1. Create a Docker Network
(Optional but recommended for communication between containers):
```bash
docker network create somenetwork
```

### 2. Run the MariaDB Container
```bash
docker container run -d \
  -e MARIADB_USER=db_user \
  -e MARIADB_PASSWORD=db_pass \
  -e MARIADB_DATABASE=frontend_app \
  -e MARIADB_ROOT_PASSWORD=root_pass \
  --network somenetwork \
  --name my_mariadb_container \
  mariadb:latest
```

### 3. Verify Container
Check that the MariaDB container is running:
```bash
docker ps
docker network inspect somenetwork
```

---

## WordPress Setup (Front-End)

### 1. Run the WordPress Container
```bash
docker container run -d \
  -e WORDPRESS_DB_HOST=my_mariadb_container \
  -e WORDPRESS_DB_NAME=frontend_app \
  -e WORDPRESS_DB_USER=db_user \
  -e WORDPRESS_DB_PASSWORD=db_pass \
  -p 8080:80 \
  --network somenetwork \
  --name wordpress_container \
  wordpress:latest
```

### 2. Verify Container
Check that the WordPress container is running:
```bash
docker ps
```

---

## Verify and Test
1. **Find the Public IP**:
   - For EC2:
     ```bash
     curl http://checkip.amazonaws.com
     ```
   - For Local Machine:
     ```bash
     hostname -I
     ```

2. **Access the Web Application**:
   Open a browser and navigate to:
   ```
   http://<YOUR-IP>:8080
   ```

3. **Set Up WordPress**:
   - Configure the site title, admin username, and password.
   - WordPress will connect to the MariaDB container.

4. **Check Logs (If Needed)**:
   ```bash
   docker logs my_mariadb_container
docker logs wordpress_container
   ```

---

### **Congratulations!**
Your end-to-end web application with MariaDB and WordPress is now up and running!

