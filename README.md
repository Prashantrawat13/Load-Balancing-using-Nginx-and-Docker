# Cloud and DevOps Project Documentation

## PROJECT TITLE : **Containerized Web Application Deployment with NGINX Load Balancing Using Docker**

---

## 1. Project Overview

This project demonstrates the implementation of a scalable and highly available web application environment using Docker containers and NGINX as a load balancer. The primary objective was to distribute incoming client requests across multiple application containers to improve performance, reliability, and fault tolerance.

The solution follows modern Cloud and DevOps practices, including containerization, service orchestration, and traffic management.

---

## 2. Objectives

* Deploy multiple instances of a web application using Docker containers.
* Configure NGINX as a reverse proxy and load balancer.
* Distribute traffic evenly among application containers.
* Improve application availability and scalability.
* Demonstrate containerized infrastructure management using DevOps principles.

---

## 3. Technologies Used

| Technology                     | Purpose                         |
| ------------------------------ | ------------------------------- |
| Docker                         | Containerization Platform       |
| NGINX                          | Load Balancer and Reverse Proxy |
| Linux                          | Operating System Environment    |
| Git                            | Version Control                 |
| Cloud Platform (AWS/Azure/GCP) | Infrastructure Hosting          |

---

## 4. System Architecture

### Architecture Flow

User Request
↓
NGINX Load Balancer
↓
─────────────────────────
| Application Container 1 |
| Application Container 2 |
| Application Container 3 |
─────────────────────────
↓
Response to User

### Components

#### NGINX Load Balancer

* Receives all incoming client requests.
* Uses round-robin load balancing to distribute traffic.
* Acts as a reverse proxy between users and containers.

#### Docker Containers

* Multiple identical application instances.
* Isolated execution environments.
* Easy deployment and scaling.

#### Docker Network

* Enables communication between NGINX and application containers.
* Provides secure container-to-container connectivity.

---

## 5. Implementation Steps

### Step 1: Containerization

Firstly, let's create a folder for our file in the root (/) directory, let's name it app. Inside the app folder, we will create an index.html file and a Dockerfile.

You can manually create the index.html file and Dockerfile or you can just pull it from the GitHub repository using the following command:

```bash
sudo yum update
sudo yum upgrade -y
cd /
```

````bash
sudo yum install git -y
sudo git clone https://github.com/Prashantrawat13/Load-Balancing-using-Nginx-and-Docker.git
````

```bash
sudo mv Load-Balancing-using-Nginx-and-Docker /app
```

```bash
cd /app
```

**OR YOU CAN MANUALLY CREATE THE FILES**.

Let's download Docker first:

* You can install Docker using the following command: (Source: <https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/install-docker.html>)

```bash
sudo yum update -y
sudo yum install -y docker
sudo service docker start
sudo usermod -a -G docker ec2-user
```

**MANUAL CREATION OF DOCKERFILES**.

Created Docker images for the application using a Dockerfile.

Example:

```Dockerfile
FROM nginx:alpine
COPY ./index.html /usr/share/nginx/html/index.html
```

In this Dockerfile, we are using the official NGINX image as our base image. We then copy our custom index.html file into the appropriate directory within the NGINX container. This allows us to serve our custom web page when the container is run.

### Step 2: Creating Multiple Images from the Dockerfile

```bash
sudo docker build -t myapp1 .
```

* Now when you create your first image, it will be named "myapp1". Then you can change the content of the index.html file and create another image with the same name "myapp2". This way you can create multiple images from the same Dockerfile, each with different content in the index.html file.

```bash
sudo docker build -t myapp2 .
```

* Do some changes in the index.html file to see some changes that'll show the changes in containers when we access them through the load balancer.

```bash
sudo docker build -t myapp3 .
```

* Do some changes in the index.html file to see some changes that'll show the changes in containers when we access them through the load balancer.

```bash
sudo docker build -t myapp4 .
```

* Now you have four different images named "myapp1", "myapp2", "myapp3", and "myapp4". Each image can be used to create a container with Slightly different content in the index.html file.

In my case, I changed the heading in the index.html file

You can check the images using the following command:

```bash
sudo docker images
```

### Step 3: Creating Multiple Containers

Started multiple instances of the application container.

```bash
sudo docker run -d --name app1 -p 8081:80 myapp1
sudo docker run -d --name app2 -p 8082:80 myapp2
sudo docker run -d --name app3 -p 8083:80 myapp3
sudo docker run -d --name app4 -p 8084:80 myapp4
```

* Now let's check if the containers are running on their respective ports.

![app1 container](</Screenshots/container-web-serve/Screenshot 2026-06-06 165121.png>)
![app2 container](</Screenshots/container-web-serve/Screenshot 2026-06-06 165129.png>)
![app3 container](</Screenshots/container-web-serve/Screenshot 2026-06-06 165137.png>)
![app4 container](</Screenshots/container-web-serve/Screenshot 2026-06-06 165150.png>)

---
---

### Step 4: Install and Configure NGINX Load Balancer

Let's install NGINX on our system and then we will configure it to act as a load balancer for our application containers.

```bash
sudo yum update
sudo yum upgrade -y
sudo yum install nginx -y
sudo systemctl start nginx
sudo systemctl enable nginx
```

Now that

```bash
cd /etc/nginx
sudo vim nginx.conf
```

Configured upstream servers inside NGINX.

```nginx
http {
    upstream backend_servers {
        server <app1_ip>:8081;                    (server 13.207.43.232:8081; -- This is what it'll look like)
        server <app2_ip>:8082;
        server <app3_ip>:8083;
        server <app4_ip>:8084;
    }

    server {
        listen 80;

        location / {
            proxy_pass http://backend_servers;
        }
    }
}
```

* Copy from the upstream block and paste it in the http block of the nginx.conf file. Then replace the <app1_ip>, <app2_ip>, <app3_ip>, and <app4_ip> with the actual IP addresses of your application containers.

Now see how you'll do it:

![nginx.conf](</Screenshots/copied-code/Screenshot 2026-06-06 170444.png>)

* Copy the above block of code and paste it like shown below in next image.

![nginx.conf-2](</Screenshots/copied-code/Screenshot 2026-06-06 170523.png>)

* **This is how the end result will look like:**

![Final Look](</Screenshots/copied-code/Screenshot 2026-06-06 170644.png>)

```bash
sudo systemctl restart nginx
sudo systemctl start nginx
sudo systemctl enable nginx
```

* Now NGINX is configured to load balance incoming requests across the four application containers.

## 6. Load Balancing Strategy

### Round Robin

The default NGINX load balancing method was used.

Request Distribution Example:

| Request   | Server |
| --------- | ------ |
| Request 1 | App1   |
| Request 2 | App2   |
| Request 3 | App3   |
| Request 4 | App1   |
| Request 5 | App2   |

This ensures balanced traffic distribution across all running containers.

---

## 7. Key Features

* Containerized deployment using Docker.
* Traffic distribution using NGINX.
* High availability architecture.
* Easy horizontal scaling.
* Improved resource utilization.
* Reduced downtime during peak traffic.

---

## 8. Benefits

### Scalability

Additional containers can be added without affecting existing services.

### Reliability

If one container becomes unavailable, NGINX can continue routing traffic to healthy containers.

### Portability

Docker containers can run consistently across different environments.

### Faster Deployment

Infrastructure setup and application deployment are automated and repeatable.

---

## 9. Challenges Faced

* Configuring inter-container networking.
* Managing NGINX reverse proxy settings.
* Ensuring proper health and connectivity between containers.
* Handling port mappings and service discovery.

---

## 10. Results

The project successfully achieved:

* Load balancing across multiple Docker containers.
* Reduced server workload on individual containers.
* Improved application availability.
* Demonstration of cloud-native deployment practices.
* Efficient traffic management using NGINX.

* **End result**

![result-1](</Screenshots/end-result/Screenshot 2026-06-06 170844.png>)
![result-2](</Screenshots/end-result/Screenshot 2026-06-06 170859.png>)
![result-3](</Screenshots/end-result/Screenshot 2026-06-06 170910.png>)
![result-4](</Screenshots/end-result/Screenshot 2026-06-06 170920.png>)

* **AS YOU CAN SEE WE CAN SEE THE LOAD BALANCING IN ACTION**

---

## 11. Future Enhancements

* Implement Kubernetes for container orchestration.
* Add auto-scaling capabilities.
* Configure SSL/TLS using HTTPS.
* Integrate CI/CD pipelines using Jenkins or GitHub Actions.
* Implement monitoring with Prometheus and Grafana.
* Add health checks and automatic failover mechanisms.

---

## 12. Conclusion

This Cloud and DevOps project successfully demonstrated the deployment of a containerized application architecture using Docker and NGINX. By implementing load balancing across multiple application containers, the system achieved improved scalability, availability, and performance. The project showcases fundamental DevOps concepts and provides a strong foundation for building enterprise-grade cloud-native applications.
