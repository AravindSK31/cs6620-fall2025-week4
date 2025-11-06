# CS6620 Fundamentals of Cloud Computing
## Comprehensive Course Summary: Weeks 1-10

**Course:** CS6620 18670 Fundamentals Cloud Computing SEC 04 Fall 2025
**Prepared:** November 2025

---

## Table of Contents

1. [Week 1: Introduction to Cloud Computing and Virtualization](#week-1)
2. [Week 2: SSH and Remote Access](#week-2)
3. [Week 3: Networking and DNS](#week-3)
4. [Week 4: Automation with GitHub Actions and AWS Lambda](#week-4)
5. [Week 5: Dependency Management and Containerization](#week-5)
6. [Week 6: Microservices Architecture](#week-6)
7. [Week 7: IAM Roles and Automated Deployment](#week-7)
8. [Week 8: Virtual Private Clouds (VPCs)](#week-8)
9. [Week 9: Advanced Networking and Infrastructure as Code](#week-9)
10. [Week 10: Production Environments and Observability](#week-10)
11. [Key Technologies Reference](#technologies-reference)
12. [Common Commands and Tools](#commands-tools)

---

## <a name="week-1"></a>Week 1: Introduction to Cloud Computing and Virtualization

### Main Topics
- Introduction to cloud computing concepts
- Virtualization fundamentals
- Type 1 vs Type 2 hypervisors
- Virtual machines and containers
- AWS EC2 instances

### Key Concepts

**Virtualization:**
- **Type 1 Hypervisors:** Run directly on hardware (e.g., Xen, VMware ESXi, used by AWS)
- **Type 2 Hypervisors:** Run on host OS (e.g., VirtualBox, UTM)
- **Virtual Machines:** Full OS instances with isolated resources
- **Containers:** Lightweight, share kernel, more portable than VMs

**EC2 (Elastic Compute Cloud):**
- Virtual servers in the cloud
- Created using Type 1 hypervisors on AWS hardware
- Pay-as-you-go pricing model
- Scalable compute capacity

### Practical Activities

**Post-Class Lab: Setting Up Virtual Machine**
- Install virtualization platform:
  - Mac: UTM
  - Windows: VirtualBox
- Download and install Ubuntu 20.04/22.04
- Install Docker on the VM
- Run containerized applications (2048 game)
- Commands learned:
  ```bash
  sudo apt-get update
  sudo apt-get install docker-ce docker-ce-cli containerd.io
  sudo docker run hello-world
  sudo docker run -d -p 80:80 amigoscode/2048
  ```

**In-Class Activity: AWS CLI and EC2**
- Launch AWS Learner Lab session
- Use AWS CLI to:
  - Query key pairs
  - Get AMI IDs
  - Launch EC2 instances
  - Describe running instances
  - SSH into EC2
- Understand security groups and SSH access
- Commands:
  ```bash
  aws ec2 describe-key-pairs
  aws ec2 run-instances --image-id $AMI_ID --count 1 --instance-type t2.micro --key-name vockey
  aws ec2 describe-instances
  ssh -i ~/.ssh/labsuser.pem ec2-user@$EC2_IP_ADDRESS
  ```

### Resources
- Learn2Cloud guide
- Course GitHub repo: https://github.com/aanchan/cs6620-fall2025-public
- Videos on virtualization, EC2, containers vs VMs

---

## <a name="week-2"></a>Week 2: SSH and Remote Access

### Main Topics
- SSH (Secure Shell) protocol
- Public key cryptography
- Security groups and firewalls
- CIDR notation and IP addressing
- OSI networking model

### Key Concepts

**SSH Security:**
- Public/private key pairs for authentication
- Default SSH port: 22 (security risk, change it)
- Permission requirements: `chmod 400` for private keys
- Using PEM files for AWS access

**Networking Fundamentals:**
- **CIDR Notation:** 192.168.10.0/24 defines network range
- **Subnet Mask:** Determines network and host portions of IP
- **Security Groups:** Virtual firewalls (stateful, instance-level)
- **0.0.0.0/0:** Represents all IPv4 addresses (open to internet)

**OSI Model Layers:**
1. Physical
2. Data Link
3. Network
4. Transport (TCP/UDP, security groups operate here)
5. Session
6. Presentation
7. Application (HTTP, SSH)

### Practical Activities

**Pre-Class: OverTheWire Bandit Wargame (Levels 0-9)**
- Essential Linux commands learned:
  - `cat`, `ls`, `cd`, `find`, `grep`, `sort`, `uniq`, `strings`
  - Working with files containing spaces
  - Finding hidden files (files starting with `.`)
  - Using pipes `|` to combine commands
  - Searching by file size, permissions
- SSH with non-default ports: `ssh -p 2220 user@host`

**In-Class Activity: Running Applications**
- Clone course repository with SSH keys
- Set up Python virtual environment:
  ```bash
  python -m venv env
  source env/bin/activate
  pip install -r requirements.txt
  python app.py
  ```
- SSH into EC2 from local machine
- Configure security groups for SSH access

**Post-Class: Networking Questions**
- Why security groups reference themselves (allow inter-instance communication)
- Security groups as Layer 4 (Transport) firewalls
- Inbound vs outbound rules
- `scp` vs `rsync` for file transfer
- CIDR notation conversions

---

## <a name="week-3"></a>Week 3: Networking and DNS

### Main Topics
- DNS (Domain Name System)
- Deploying applications to EC2
- HTTPS vs HTTP
- Server configuration and setup
- Key pair management

### Key Concepts

**DNS and Domain Names:**
- DNS translates domain names to IP addresses
- DuckDNS: Free dynamic DNS service
- Format: `speech-NUID.duckdns.org`
- Propagation time: ~15 minutes

**HTTP vs HTTPS:**
- HTTP: Plain text, port 80
- HTTPS: Encrypted with TLS/SSL, port 443
- Requires certificate (e.g., from AWS Certificate Manager, Let's Encrypt)

**Application Deployment:**
- **Gunicorn:** Production WSGI server for Python apps
- Alternative to Flask development server
- Handles multiple concurrent requests
- Production-ready configuration

### Practical Activities

**Pre-Class: SSH Keys on EC2**
- Generate SSH keys on EC2 for GitHub access:
  ```bash
  ssh-keygen -t ed25519 -C "ec2-user@$(hostname)" -f ~/.ssh/github_key -N ""
  chmod 600 ~/.ssh/github_key
  ```
- Configure SSH config for GitHub
- Install git on EC2: `sudo yum install -y git`
- Clone repository via SSH

**In-Class: Full Application Deployment**
1. Create and import SSH key pair in AWS
2. Launch EC2 with custom key pair
3. Clone repository on EC2
4. Run `./server-setup.sh` to configure environment
5. Run `start.sh` to launch application with Gunicorn
6. Configure security group (port 80)
7. Register domain with DuckDNS
8. Access application via custom domain

**Post-Class: Understanding Deployment**
- Analyze `setup-server.sh` script
- Understand Gunicorn's role
- Research HTTPS implementation requirements
- Certificate acquisition process

### Commands & Configuration
```bash
# Import key pair to AWS
aws ec2 import-key-pair --key-name mykey --public-key-material fileb://~/.ssh/id_rsa.pub

# Security group modification (allow HTTP)
aws ec2 authorize-security-group-ingress --group-id sg-xxx --protocol tcp --port 80 --cidr 0.0.0.0/0
```

---

## <a name="week-4"></a>Week 4: Automation with GitHub Actions and AWS Lambda

### Main Topics
- Continuous Integration/Continuous Deployment (CI/CD)
- GitHub Actions workflows
- AWS Lambda serverless functions
- Event-driven automation with EventBridge
- Infrastructure automation and cost optimization

### Key Concepts

**CI/CD Pipeline:**
- Automated testing on code changes
- Linting with flake8
- Testing with pytest
- Workflow triggers (push, pull_request)
- `continue-on-error: true` for non-blocking failures

**AWS Lambda:**
- Serverless compute service
- Event-driven execution
- Pay only for compute time used
- Common use cases: API health checks, automation tasks
- Timeout configuration (default 3s, max 15 minutes)

**EventBridge (CloudWatch Events):**
- Scheduled events with cron expressions
- Lambda function triggers
- Format: `cron(minutes hours day month day-of-week year)`
- Example: `0/10 * * * ? *` = every 10 minutes

### Practical Activities

**Pre-Class: Team Formation**
- Submit team member names
- Schedule project consultation meetings

**In-Class Part 1: GitHub CI Pipeline**
- Fork course repository
- Create `.github/workflows/ci.yml`
- Configure workflow:
  ```yaml
  name: CI Pipeline
  on:
    push:
      branches: [ week02 ]
    pull_request:
      branches: [ week02 ]
  jobs:
    test:
      runs-on: ubuntu-latest
      steps:
        - uses: actions/checkout@v3
        - uses: actions/setup-python@v4
          with:
            python-version: '3.9'
        - run: pip install -r requirements.txt
        - run: flake8 . --count --select=E9,F63,F7,F82 --show-source --statistics
          continue-on-error: true
        - run: pytest
  ```

**In-Class Part 2: Lambda Health Checker**
- Create Lambda function `api-health-checker`
- Python code to check multiple APIs:
  ```python
  import urllib3
  import json

  http = urllib3.PoolManager()

  def lambda_handler(event, context):
      endpoints = [
          'https://httpbin.org/get',
          'https://jsonplaceholder.typicode.com/posts/1',
          'https://reqres.in/api/users/2'
      ]

      results = []
      for url in endpoints:
          try:
              response = http.request('GET', url)
              results.append({
                  'url': url,
                  'status': response.status,
                  'healthy': response.status == 200
              })
          except Exception as e:
              results.append({
                  'url': url,
                  'error': str(e),
                  'healthy': False
              })

      return {
          'statusCode': 200,
          'body': json.dumps(results)
      }
  ```
- EventBridge rule: Run every 1 minute
- Monitor via CloudWatch Logs

**Post-Class Lab 1: Fix CI Linting**
- Fork week4 branch
- Fix linting errors
- Create pull request with passing CI

**Post-Class Lab 2: EC2 Automation**
- Create Lambda function for EC2 start/stop
- Use boto3 for EC2 control:
  ```python
  import boto3

  ec2 = boto3.client('ec2')

  def lambda_handler(event, context):
      instance_id = 'i-xxxxx'
      action = event.get('action', 'start')

      if action == 'start':
          ec2.start_instances(InstanceIds=[instance_id])
      elif action == 'stop':
          ec2.stop_instances(InstanceIds=[instance_id])

      return {'statusCode': 200, 'body': f'EC2 {action} initiated'}
  ```
- EventBridge cron schedules:
  - Start: `cron(0/10 * * * ? *)` (every 10 min)
  - Stop: `cron(5/10 * * * ? *)` (offset by 5 min)
- Understand cost optimization patterns

### Key Technologies
- GitHub Actions
- YAML configuration
- AWS Lambda
- Python boto3 library
- EventBridge/CloudWatch Events
- CloudWatch Logs
- Cron expressions
- IAM roles (LabRole)

---

## <a name="week-5"></a>Week 5: Dependency Management and Containerization

### Main Topics
- Docker containerization
- Dockerfile creation
- Docker Compose for microservices
- Container registries (Docker Hub)
- CI/CD with Docker
- Microservices architecture

### Key Concepts

**Containers vs VMs:**
- Containers share host OS kernel
- VMs include full OS
- Containers are more lightweight
- "Infinite portability" across environments
- Isolation through namespaces and cgroups

**Docker Components:**
- **Dockerfile:** Blueprint for building images
- **Image:** Read-only template
- **Container:** Running instance of an image
- **Docker Hub:** Public container registry
- **Docker Compose:** Multi-container orchestration

**OCI Labels:**
- Open Container Initiative standards
- Metadata for images
- Examples:
  - `org.opencontainers.image.source`
  - `org.opencontainers.image.revision`
  - `build.source=github-actions`

### Practical Activities

**Pre-Class: Concepts Review**
- Containers vs Type 2 hypervisors
- Levels of virtualization
- Portability advantages
- Docker architecture diagrams

**In-Class Activity 1: Docker Basics**
- Use Play with Docker (https://www.docker.com/play-with-docker/)
- Create Dockerfile:
  ```dockerfile
  FROM python:3.9-slim
  WORKDIR /app
  COPY requirements.txt .
  RUN pip install -r requirements.txt
  COPY . .
  EXPOSE 5000
  CMD ["python", "app.py"]
  ```
- Build and run:
  ```bash
  docker build -t myapp:v1.0 .
  docker run -p 5000:5000 myapp:v1.0
  ```

**In-Class Activity 2: Docker Compose Microservices**
- Clone dockersamples/example-voting-app
- Run multi-container application:
  ```bash
  docker compose up
  ```
- Access services:
  - Voting interface: http://localhost:8080
  - Results interface: http://localhost:8081
- Understand service communication

**Post-Class: Complete Docker CI/CD Pipeline**

**Part 1: Create Dockerfile**
```dockerfile
FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 5000

CMD ["python", "app.py"]
```

Create `.dockerignore`:
```
.git
.env
__pycache__
*.pyc
env/
venv/
```

**Part 2: Test Locally**
```bash
docker build -t username/cs6620-app:v1.0 .
docker run -p 5000:5000 username/cs6620-app:v1.0
curl http://localhost:5000
```

**Part 3: CI/CD to Docker Hub**
- Create Docker Hub access token (Read & Write permissions)
- Add GitHub secrets:
  - `DOCKERHUB_USERNAME`
  - `DOCKERHUB_TOKEN`

Create `.github/workflows/docker-build-push.yml`:
```yaml
name: Build and Push Docker Image

on:
  push:
    branches: [ week02 ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Login to Docker Hub
        uses: docker/login-action@v2
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2

      - name: Build and push
        uses: docker/build-push-action@v4
        with:
          context: .
          push: true
          tags: ${{ secrets.DOCKERHUB_USERNAME }}/cs6620-app:latest
          labels: |
            org.opencontainers.image.source=${{ github.server_url }}/${{ github.repository }}
            org.opencontainers.image.revision=${{ github.sha }}
            build.source=github-actions
            build.workflow=${{ github.workflow }}
            build.run_id=${{ github.run_id }}
            build.run_number=${{ github.run_number }}
```

**Part 4: Deploy to EC2**
```bash
# On EC2: Install Docker
sudo yum update -y
sudo yum install -y docker
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker ec2-user

# Pull and run
docker pull username/cs6620-app:latest
docker run -d -p 80:5000 --name cs6620-app username/cs6620-app:latest

# Verify
docker ps
docker logs cs6620-app
docker image inspect username/cs6620-app:latest | grep -A 10 Labels
```

### Key Commands
```bash
# Docker basics
docker build -t image:tag .
docker images
docker run -d -p host:container image:tag
docker ps
docker stop container_id
docker rm container_id
docker logs container_id
docker inspect image:tag

# Docker Compose
docker compose up
docker compose down
docker compose ps
```

---

## <a name="week-6"></a>Week 6: Microservices Architecture

### Main Topics
- Monolithic vs microservices architecture
- System design principles
- Scalability patterns
- Service communication
- Distributed systems challenges

### Key Concepts

**Monolithic Architecture:**
- Single, unified codebase
- All components deployed together
- Challenges:
  - Difficult to scale individual components
  - Deployment risk (one failure affects all)
  - Technology lock-in
  - Slower development cycles

**Microservices Architecture:**
- Independent, loosely coupled services
- Each service has specific business capability
- Benefits:
  - Independent scaling
  - Technology flexibility
  - Faster deployment
  - Fault isolation
- Challenges:
  - Network latency
  - Data consistency
  - Increased complexity
  - Distributed debugging

**System Design Concepts:**

1. **Scaling Strategies:**
   - **Vertical Scaling:** Increase server resources (CPU, RAM)
   - **Horizontal Scaling:** Add more servers

2. **Load Balancing:**
   - Distribute traffic across servers
   - Algorithms: Round Robin, Least Connections, IP Hash
   - Health checks for availability

3. **Database Strategies:**
   - **Sharding:** Partition data across databases
   - **Replication:** Copy data for redundancy
   - Master-slave configurations

4. **Caching:**
   - **Redis:** In-memory data store
   - **TTL (Time To Live):** Cache expiration
   - Reduces database load
   - Example from voting app

5. **CDN (Content Delivery Network):**
   - Distribute static content globally
   - Reduce latency for users
   - Cache at edge locations

6. **Communication Patterns:**
   - **REST APIs:** Synchronous HTTP
   - **WebSockets:** Real-time bidirectional
   - **Message Queues:** Asynchronous communication
   - Polling vs WebSockets

7. **API Gateway:**
   - Single entry point for services
   - Rate limiting
   - Authentication/authorization
   - Request routing

### Practical Activities

**Pre-Class: Architecture Comparison**
- Watch microservices vs monoliths video
- Ticketing application example
- True/False questions:
  - Deploying monoliths on Friday: Generally bad idea
  - Scaling individual services in monoliths: Not possible
  - Microservices using APIs: True
  - Independent languages/scaling: True

**Post-Class: System Design Deep Dive**
- Comprehensive video on system design
- Topics covered:
  - Reverse proxy (NGINX, Apache)
  - HTTP structure and headers
  - Vertical vs horizontal scaling
  - Load balancing algorithms
  - Database sharding strategies
  - Caching with Redis (seen in Week 5 voting app)
  - CDN architecture
  - WebSockets for real-time features
  - Message queues in microservices
  - Rate limiting strategies
  - API Gateway patterns

### Key Principles
- **Single Responsibility:** Each service does one thing well
- **Loose Coupling:** Services are independent
- **High Cohesion:** Related functionality grouped together
- **API Contracts:** Well-defined interfaces
- **Fault Tolerance:** Graceful degradation
- **Observability:** Monitoring and logging

---

## <a name="week-7"></a>Week 7: IAM Roles and Automated Deployment

### Main Topics
- Identity and Access Management (IAM)
- AWS Systems Manager (SSM)
- Secure deployment practices
- S3 storage service
- Automated CI/CD deployment

### Key Concepts

**IAM (Identity and Access Management):**

**Core Components:**
- **Users:** Individual accounts with credentials
- **Groups:** Collection of users with shared permissions
- **Roles:** Set of permissions without credentials (can be "assumed")
- **Policies:** JSON documents defining permissions

**Key Principles:**
- "Who can do what to which resources and when"
- **Roles vs Users:** Roles have no credentials, must be assumed
- **Least Privilege:** Grant minimum required permissions
- **Group-based Permissions:** Attach policies to groups, not individual users

**IAM Policy Structure:**
```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Action": "*",
    "Resource": "*"
  }]
}
```

**Example IAM Use Cases:**
- EC2 accessing S3 without credentials (via IAM role)
- Lambda accessing CloudWatch Logs
- GitHub Actions deploying to AWS (temporary credentials)

**AWS Systems Manager (SSM):**
- Secure way to manage EC2 instances
- No need to expose SSH port 22
- Uses IAM roles for authentication
- Run commands remotely via SSM Agent
- Better than SSH keys over public internet

**S3 (Simple Storage Service):**
- Object storage service
- Inexpensive file storage
- Use cases: Static websites, backups, data lakes
- **Public Access:** Generally not recommended
- **Signed URLs:** Temporary access without public exposure
- **Object Lock:** Regulatory compliance (WORM)

**EBS (Elastic Block Store):**
- Block storage for EC2
- Like a virtual hard drive
- Different from S3 (object storage)

### Practical Activities

**Pre-Class: IAM Concepts**
Watch tutorial with example users (Dwight, Pam, Michael)

**Key Learnings:**
- Creating user groups and assigning users
- Roles as "hats" that can be assumed
- EC2 using roles to access S3/CloudWatch
- Policy attachment at group vs user level
- AdministratorAccess policy analysis
- S3 security best practices

**In-Class: Deployment Strategy with Claude AI**
- Use Claude AI (https://claude.northeastern.edu/)
- Upload previous Docker assignment and workflow
- Ask about deployment options from GitHub to EC2
- Explore SSM as deployment method
- Understand IAM role requirements
- Why SSM preferred over SSH keys:
  - Private SSH keys over public internet = risky
  - SSM uses IAM authentication
  - SSH acceptable in private networks (Jenkins, OpenShift)

**Post-Class: Complete Automated Deployment**

**Part 1: Configure EC2 for SSM**
```bash
# Attach LabInstanceProfile IAM role to EC2 in AWS console

# Verify SSM agent (pre-installed on Amazon Linux 2)
sudo systemctl status amazon-ssm-agent

# If not running
sudo systemctl start amazon-ssm-agent
sudo systemctl enable amazon-ssm-agent
```

**Part 2: Setup GitHub Secrets**
From AWS Learner Lab, get temporary credentials and add to GitHub:
- `AWS_ACCESS_KEY_ID`
- `AWS_SECRET_ACCESS_KEY`
- `AWS_SESSION_TOKEN` (required for Learner Lab!)
- `AWS_REGION` (e.g., us-east-1)
- `EC2_INSTANCE_ID` (e.g., i-xxxxx)

**Important:** Credentials expire after 4 hours. Refresh when needed.

**Part 3: Extend GitHub Actions Workflow**

Create deployment job in `.github/workflows/docker-build-push.yml`:
```yaml
deploy:
  needs: build
  runs-on: ubuntu-latest
  steps:
    - name: Configure AWS credentials
      uses: aws-actions/configure-aws-credentials@v4
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws-session-token: ${{ secrets.AWS_SESSION_TOKEN }}
        aws-region: ${{ secrets.AWS_REGION }}

    - name: Deploy to EC2 via SSM
      run: |
        aws ssm send-command \
          --instance-ids ${{ secrets.EC2_INSTANCE_ID }} \
          --document-name "AWS-RunShellScript" \
          --parameters 'commands=[
            "docker pull ${{ secrets.DOCKERHUB_USERNAME }}/cs6620-app:latest",
            "docker stop cs6620-app || true",
            "docker rm cs6620-app || true",
            "docker run -d -p 80:5000 --name cs6620-app --restart unless-stopped ${{ secrets.DOCKERHUB_USERNAME }}/cs6620-app:latest"
          ]' \
          --comment "Automated deployment from GitHub Actions"
```

Update Flask app to show version 2.0:
```python
@app.route('/')
def home():
    return f"""
    <h1>Version 2.0 - Deployed via GitHub Actions!</h1>
    <p>Deployed at: {datetime.now()}</p>
    """
```

**Part 4: Security Analysis**

**Security Violation Identified:**
Using LabInstanceProfile for both:
- GitHub Actions (to run SSM commands)
- EC2 instance (to run SSM agent)

**Proper IAM Design:**

GitHub User/Role (for CI/CD):
```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Action": [
      "ssm:SendCommand",
      "ssm:GetCommandInvocation",
      "ec2:DescribeInstances"
    ],
    "Resource": [
      "arn:aws:ec2:*:*:instance/i-specific-instance",
      "arn:aws:ssm:*:*:*"
    ]
  }]
}
```

EC2 Instance Role:
```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Action": [
      "ssm:UpdateInstanceInformation",
      "ssmmessages:*",
      "ec2messages:*"
    ],
    "Resource": "*"
  }]
}
```

**Principle of Least Privilege:** Each role has only the minimum permissions needed.

### Key Commands
```bash
# SSM send command
aws ssm send-command \
  --instance-ids i-xxxxx \
  --document-name "AWS-RunShellScript" \
  --parameters 'commands=["echo hello"]'

# Check command status
aws ssm get-command-invocation \
  --command-id xxxxx \
  --instance-id i-xxxxx

# View SSM agent status
sudo systemctl status amazon-ssm-agent
```

### Important Notes
- SSM requires IAM role attached to EC2
- AWS Learner Lab credentials expire every 4 hours
- Session tokens required for Learner Lab (different from regular AWS)
- Monitor deployments via Systems Manager console or CloudWatch

---

## <a name="week-8"></a>Week 8: Virtual Private Clouds (VPCs)

### Main Topics
- Virtual Private Cloud architecture
- Network segmentation
- Public and private subnets
- Internet Gateway and NAT Gateway
- Security Groups vs Network ACLs
- Project presentations

### Key Concepts

**VPC (Virtual Private Cloud):**
- Isolated network environment in AWS
- Complete control over networking
- Define IP address range (CIDR block)
- Example: 10.0.0.0/16 provides 65,536 IP addresses

**VPC Components:**

1. **Subnets:**
   - Subdivisions of VPC CIDR block
   - **Public Subnet:** Has route to Internet Gateway
   - **Private Subnet:** No direct internet access
   - Example:
     - Public: 10.0.1.0/24 (256 IPs)
     - Private: 10.0.2.0/24 (256 IPs)

2. **Internet Gateway (IGW):**
   - Connects VPC to internet
   - Required for public subnets
   - Attached to VPC
   - Handles NAT for public IPs

3. **NAT Gateway:**
   - Allows private subnets to access internet
   - Unidirectional: outbound only
   - Placed in public subnet
   - Used for software updates, API calls
   - Stateful: Tracks connections

4. **Route Tables:**
   - Define traffic routing
   - Public subnet route table:
     - 10.0.0.0/16 → local
     - 0.0.0.0/0 → IGW
   - Private subnet route table:
     - 10.0.0.0/16 → local
     - 0.0.0.0/0 → NAT Gateway

**Security Layers:**

**Network ACLs (Access Control Lists):**
- Subnet-level firewall
- **Stateless:** Separate inbound/outbound rules
- Rule numbers (lowest first)
- Can ALLOW or DENY
- Example rules:
  - Rule 100: Allow HTTP (80) from 0.0.0.0/0
  - Rule 200: Allow HTTPS (443) from 0.0.0.0/0
  - Rule *: Deny all

**Security Groups:**
- Instance-level firewall
- **Stateful:** Return traffic automatically allowed
- Only ALLOW rules (implicit deny)
- Can reference other security groups
- Example rules:
  - Allow port 22 (SSH) from 203.0.113.0/24
  - Allow port 80 (HTTP) from 0.0.0.0/0
  - Allow all traffic from same security group

**Comparison:**
| Feature | Security Groups | Network ACLs |
|---------|----------------|--------------|
| Level | Instance | Subnet |
| State | Stateful | Stateless |
| Rules | Allow only | Allow & Deny |
| Rule Processing | All rules evaluated | Ordered rules |
| Applies to | Instance ENI | All instances in subnet |

### Practical Activities

**Pre-Class: VPC Concepts**
Watch Tiny Tech Tutorials VPC video

**Key Questions:**
1. How do private subnets access internet?
   - **Answer:** Through NAT Gateway in public subnet

2. What connects VPC to internet?
   - **Answer:** Internet Gateway (IGW)

3. Difference between ACL and Security Group?
   - **ACL:** Stateless, subnet-level, can deny
   - **Security Group:** Stateful, instance-level, allow only

4. Submit 3 concept questions for review from entire course

**In-Class Part 1: Project Presentations**
- 10 groups present projects
- For each of 9 other groups:
  - **Individual:** What you understand about their project
  - **Team:** One question to ask the group
- Questions compiled for post-class responses

**In-Class Part 2: MedReminder Architecture**
- Architecture diagramming exercise
- Use MedReminder Student Handout
- Color coding:
  - **Purple:** Services (EC2, Lambda, RDS)
  - **Blue:** Network components (VPC, subnets, IGW, NAT)
  - **Red:** Security components (Security Groups, ACLs, IAM)

**Example Architecture:**
```
┌─────────────────── VPC (10.0.0.0/16) ───────────────────┐
│                                                          │
│  ┌──── Public Subnet (10.0.1.0/24) ────┐               │
│  │                                       │               │
│  │  [Internet Gateway]                  │               │
│  │        ↓                              │               │
│  │  [NAT Gateway]                        │               │
│  │  [Bastion Host]                       │               │
│  │                                       │               │
│  └───────────────────────────────────────┘               │
│                                                          │
│  ┌──── Private Subnet (10.0.2.0/24) ────┐               │
│  │                                       │               │
│  │  [Application Servers]                │               │
│  │  [RDS Database]                       │               │
│  │                                       │               │
│  └───────────────────────────────────────┘               │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

### Key Design Patterns

**Multi-Tier Architecture:**
1. **Public Tier:**
   - Load balancers
   - NAT Gateways
   - Bastion hosts

2. **Application Tier:**
   - Application servers in private subnet
   - Scale horizontally
   - Access via load balancer

3. **Database Tier:**
   - RDS in private subnet
   - Multi-AZ for high availability
   - Backup retention

**Security Best Practices:**
- Use private subnets for databases and application servers
- Bastion host for administrative access
- Security groups with least privilege
- Network ACLs for additional subnet-level protection
- VPC Flow Logs for traffic monitoring

---

## <a name="week-9"></a>Week 9: Advanced Networking and Infrastructure as Code

### Main Topics
- NAT Gateways and stateful connections
- Elastic Load Balancing (ELB)
- OSI model in cloud context
- Frontend deployment (S3, CloudFront)
- Infrastructure as Code with Terraform
- Multi-cloud deployments

### Key Concepts

**NAT Gateway Deep Dive:**
- Enables private subnet instances to initiate outbound connections
- **Stateful Connection Management:**
  - Tracks TCP connection state
  - Remembers outbound request
  - Allows corresponding inbound response
  - Example flow:
    1. Private instance sends request to API (source: private IP)
    2. NAT Gateway translates source to its public IP
    3. API responds to NAT Gateway's public IP
    4. NAT Gateway remembers connection, forwards to private instance

- **OSI Layer:** Operates at Layer 3 (Network) and Layer 4 (Transport)
- **HTTP Context:**
  - HTTP request goes out through NAT
  - HTTP response comes back through same connection
  - NAT maintains connection table

**Elastic Load Balancer (ELB):**

**Types:**
1. **Application Load Balancer (ALB):**
   - Layer 7 (Application)
   - HTTP/HTTPS traffic
   - Path-based routing (/api → backend, /images → static)
   - Host-based routing (api.example.com, www.example.com)
   - TLS termination

2. **Network Load Balancer (NLB):**
   - Layer 4 (Transport)
   - TCP/UDP traffic
   - Ultra-high performance
   - Static IP support

3. **Gateway Load Balancer:**
   - Layer 3 (Network)
   - Third-party virtual appliances

**ALB Features:**
- **TLS Termination:** Decrypt HTTPS at load balancer
  - EC2 instances only handle HTTP
  - Reduces compute load on instances
  - Centralized certificate management

- **Health Checks:** Monitor instance availability
- **Cross-Zone Load Balancing:** Distribute across availability zones
- **Connection Draining:** Graceful shutdown

**Architecture Pattern:**
```
Internet → [IGW] → [ALB in Public Subnet] → [EC2 in Private Subnet] → [RDS in Private Subnet]
```

**Stateful Connection Management:**
- ALB tracks client connections
- Maintains session to backend instance
- Can use sticky sessions (session affinity)
- Operates at Layer 7 (understands HTTP)

**Frontend Deployment Architecture:**

**S3 Static Website Hosting:**
- Host React/Vue/Angular builds
- Enable static website hosting
- Bucket policy for public read
- Cost-effective for static content

**CloudFront CDN:**
- Global content delivery
- Cache static assets at edge locations
- HTTPS support with ACM certificates
- Custom domain with Route53
- Invalidation for cache refresh

**Complete Frontend Pipeline:**
```
GitHub → GitHub Actions → Build (npm run build) → S3 Upload → CloudFront Invalidation
```

**Sample CI/CD for React:**
```yaml
name: Deploy Frontend

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Setup Node
        uses: actions/setup-node@v3
        with:
          node-version: '18'

      - name: Build
        run: |
          npm install
          npm run build

      - name: Deploy to S3
        uses: jakejarvis/s3-sync-action@v0.5.1
        with:
          args: --delete
        env:
          AWS_S3_BUCKET: 'my-frontend-bucket'
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          SOURCE_DIR: 'dist'

      - name: Invalidate CloudFront
        run: |
          aws cloudfront create-invalidation \
            --distribution-id ${{ secrets.CLOUDFRONT_ID }} \
            --paths "/*"
```

**Route53 DNS:**
- Highly available DNS service
- Create A record pointing to CloudFront
- Alias record (no additional charge)

### Infrastructure as Code (Terraform)

**Benefits:**
- Version controlled infrastructure
- Reproducible environments
- Multi-cloud support (AWS, Azure, GCP)
- Declarative syntax
- State management

**Terraform Basics:**
```hcl
# Provider configuration
provider "aws" {
  region = "us-east-1"
}

# VPC resource
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"

  tags = {
    Name = "main-vpc"
  }
}

# Subnet resource
resource "aws_subnet" "public" {
  vpc_id     = aws_vpc.main.id
  cidr_block = "10.0.1.0/24"

  tags = {
    Name = "public-subnet"
  }
}

# Internet Gateway
resource "aws_internet_gateway" "main" {
  vpc_id = aws_vpc.main.id
}
```

**Terraform Commands:**
```bash
terraform init      # Initialize and download providers
terraform plan      # Preview changes
terraform apply     # Apply changes
terraform destroy   # Destroy infrastructure
terraform show      # Show current state
```

### Practical Activities

**Pre-Class Part 1: NAT and Load Balancer Videos**

**Key Questions:**

1. **Prior Knowledge Summary:**
   - What did you know before watching videos?

2. **NAT Gateway Request Management:**
   - How does NAT manage requests within VPC?
   - Relation to HTTP request/response
   - Stateful connection tracking
   - Which OSI layer?
   - **Answer:** Layer 3/4, maintains connection state table, allows response traffic

3. **Elastic Load Balancer Request Management:**
   - How does ELB manage requests within and outside VPC?
   - HTTP request/response in context
   - Stateful connection management
   - Which OSI layer?
   - **Answer:** ALB at Layer 7, NLB at Layer 4, tracks client connections, routes to healthy targets

4. **Web Application Architecture:**
   - **Backend API:** EC2 in private subnet (compute intensive, database access)
   - **Frontend React:** S3 + CloudFront (static files, global distribution)
   - **CI/CD Pipeline:** GitHub Actions builds React → uploads to S3 → invalidates CloudFront
   - **Communication:** Frontend makes API calls to backend ALB endpoint

**Pre-Class Part 2: React Deployment**
- Read blog post on S3, Route53, CloudFront deployment
- Understand static website hosting
- CI/CD for frontend applications

**In-Class: Terraform Multi-Cloud Analysis**

**Repository:** https://github.com/learntocloud/linux-ctfs

**Activity:**
1. Review Terraform code in `aws` folder
2. Partner review without AI:
   - What does the code do? (2-3 sentences)
   - Identify 2 specific components and explain

3. Cross-cloud comparison:
   - Review AWS, Google Cloud, Azure folders
   - Find 2 functionally equivalent components
   - Example:
     - AWS: `aws_vpc`
     - GCP: `google_compute_network`
     - Azure: `azurerm_virtual_network`
   - Observations on syntax differences (2-3 sentences)

**Example Terraform Comparison:**

AWS VPC:
```hcl
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
}
```

GCP Network:
```hcl
resource "google_compute_network" "main" {
  name                    = "main"
  auto_create_subnetworks = false
}
```

Azure Virtual Network:
```hcl
resource "azurerm_virtual_network" "main" {
  name                = "main"
  address_space       = ["10.0.0.0/16"]
  location            = azurerm_resource_group.main.location
  resource_group_name = azurerm_resource_group.main.name
}
```

### Architecture Decision Points

**When to Use What:**

| Use Case | Solution | Reason |
|----------|----------|--------|
| Static website | S3 + CloudFront | Cost-effective, fast, scalable |
| API backend | EC2/ECS in private subnet | Compute power, database access |
| Database | RDS in private subnet | Managed, secure, backed up |
| File storage | S3 | Durable, cheap, scalable |
| CDN | CloudFront | Global distribution, low latency |
| Load balancing | ALB (HTTP) or NLB (TCP) | High availability, auto-scaling |
| Private outbound | NAT Gateway | Secure, stateful, managed |

### Key Technologies
- NAT Gateway
- Elastic Load Balancer (ALB, NLB)
- S3 static website hosting
- CloudFront CDN
- Route53 DNS
- Terraform
- Infrastructure as Code (IaC)
- Multi-cloud deployments
- OSI model application

---

## <a name="week-10"></a>Week 10: Production Environments and Observability

### Main Topics
- Production deployment strategies
- Multiple environments (dev, staging, production)
- Monitoring vs Observability
- CloudWatch
- Site Reliability Engineering (SRE)
- The Four Golden Signals

### Key Concepts

**Environment Strategy:**

**Three Core Environments:**
1. **Development (Dev):**
   - Developer workstations
   - Rapid iteration
   - Feature branches
   - May have mock services
   - Database: Dev data or seeded data

2. **Staging:**
   - **Production-like environment**
   - Same configuration as production
   - Same instance types, database versions
   - Testing ground before production
   - Automated tests run here
   - **Should be identical to production** (using IaC like Terraform)

3. **Production:**
   - Live environment serving real users
   - Highest security and monitoring
   - Backed up regularly
   - High availability configured
   - Performance optimized

**Environment Parity:**
- Staging and production should be identical
- Use Terraform/IaC to ensure consistency
- Prevents "works in staging but not in production" issues
- Test configurations before production deployment

**Deployment Flow:**
```
Developer → Git Push → CI Pipeline → Deploy to Dev → Integration Tests
    ↓
Deploy to Staging → Full Test Suite → Manual QA → Approval
    ↓
Deploy to Production → Monitoring → Rollback if needed
```

**Monitoring vs Observability:**

**Monitoring:**
- **Definition:** Collecting and displaying known metrics
- **Approach:** Predefined dashboards and alerts
- **Use Case:** "Is the system working?"
- **Components:**
  - Metrics (CPU, memory, disk)
  - Logs (application logs)
  - Alerts (threshold-based)
- **Example Tools:** CloudWatch, Prometheus, Grafana

**Observability:**
- **Definition:** Understanding system behavior from external outputs
- **Approach:** Exploratory, ad-hoc queries
- **Use Case:** "Why is the system behaving this way?"
- **Three Pillars:**
  1. **Metrics:** Numerical measurements over time
  2. **Logs:** Detailed event records
  3. **Traces:** Request flow through distributed system
- **Example Tools:** Honeycomb, Datadog, New Relic

**Key Difference:**
- **Monitoring:** Known knowns (what we expect to break)
- **Observability:** Unknown unknowns (what we didn't anticipate)

**Example Scenario:**
- **Monitoring:** Alert when response time > 500ms
- **Observability:** Investigate why a specific user's request takes 2 seconds by tracing through all services

### Site Reliability Engineering (SRE)

**SRE Principles:**
- Originated at Google
- Treat operations as software engineering problems
- Balance reliability with feature velocity
- Error budgets
- Automation over manual work

**Four Golden Signals:**

1. **Latency:**
   - **What:** Time to serve a request
   - **Why Important:** User experience, performance issues
   - **Metrics:**
     - P50 (median): 50th percentile response time
     - P95: 95th percentile (outliers)
     - P99: 99th percentile (worst experiences)
   - **Example:** API endpoint should respond in < 200ms at P95
   - **Alert:** P95 latency > 500ms for 5 minutes

2. **Traffic:**
   - **What:** Demand on the system
   - **Why Important:** Capacity planning, scaling decisions
   - **Metrics:**
     - Requests per second (RPS)
     - Concurrent users
     - Network I/O
   - **Example:** System handles 1000 RPS normally
   - **Alert:** Traffic drops by 50% (potential outage) or increases 300% (DDoS)

3. **Errors:**
   - **What:** Rate of failed requests
   - **Why Important:** Service reliability, user impact
   - **Metrics:**
     - Error rate (%)
     - Error count by type (4xx, 5xx)
     - Failed transactions
   - **Example:** 1% error rate acceptable, 5% critical
   - **Alert:** Error rate > 1% for 5 minutes

4. **Saturation:**
   - **What:** How "full" the service is
   - **Why Important:** Predict when to scale before outage
   - **Metrics:**
     - CPU utilization (%)
     - Memory usage (%)
     - Disk I/O
     - Network bandwidth
     - Database connections
   - **Example:** Database at 80% connection pool
   - **Alert:** CPU > 80% for 10 minutes

**Golden Signals in Practice:**

```python
# Example CloudWatch metrics for Flask app
import boto3
from datetime import datetime

cloudwatch = boto3.client('cloudwatch')

def track_request(endpoint, duration, status_code):
    # Latency
    cloudwatch.put_metric_data(
        Namespace='MyApp',
        MetricData=[{
            'MetricName': 'ResponseTime',
            'Value': duration,
            'Unit': 'Milliseconds',
            'Dimensions': [{'Name': 'Endpoint', 'Value': endpoint}]
        }]
    )

    # Traffic
    cloudwatch.put_metric_data(
        Namespace='MyApp',
        MetricData=[{
            'MetricName': 'RequestCount',
            'Value': 1,
            'Unit': 'Count'
        }]
    )

    # Errors
    if status_code >= 500:
        cloudwatch.put_metric_data(
            Namespace='MyApp',
            MetricData=[{
                'MetricName': 'ErrorCount',
                'Value': 1,
                'Unit': 'Count',
                'Dimensions': [{'Name': 'StatusCode', 'Value': str(status_code)}]
            }]
        )
```

### AWS CloudWatch

**CloudWatch Components:**

1. **CloudWatch Metrics:**
   - Time-series data points
   - Examples:
     - EC2: CPUUtilization, NetworkIn/Out
     - Lambda: Invocations, Errors, Duration
     - RDS: DatabaseConnections, ReadLatency
   - Custom metrics via PutMetricData API

2. **CloudWatch Logs:**
   - Centralized log management
   - Log Groups and Log Streams
   - Filter patterns for searching
   - Insights for querying (SQL-like)
   - Example query:
     ```
     fields @timestamp, @message
     | filter @message like /ERROR/
     | sort @timestamp desc
     | limit 20
     ```

3. **CloudWatch Alarms:**
   - Threshold-based alerts
   - Actions: SNS notification, Auto Scaling, Lambda
   - Example: CPU > 80% for 2 consecutive periods
   - Alarm states: OK, ALARM, INSUFFICIENT_DATA

4. **CloudWatch Dashboards:**
   - Visual representation of metrics
   - Multiple widgets (line graphs, numbers, logs)
   - Shareable dashboards
   - Auto-refresh

**CloudWatch in Monitoring vs Observability:**

**Monitoring Use:**
- Track predefined metrics
- Set up alarms for known issues
- Dashboard for operations team
- Example: "CPU is high, add more instances"

**Observability Use:**
- CloudWatch Logs Insights for ad-hoc queries
- Trace request IDs through logs
- Correlate metrics with logs
- Example: "Why did this specific user request fail?"

**CloudWatch Limitations for Observability:**
- Not designed for distributed tracing
- Limited cross-service correlation
- Use with X-Ray for better observability

### Practical Activities

**Pre-Class Part 1: Project Q&A**
- Answer questions from classmates (compiled by TA)
- Respond to feedback on project progress slides

**Pre-Class Part 2: Shipping Code to Production**
- Watch video on how big companies deploy code

**Key Learnings:**
1. **Environments Mentioned:**
   - Development
   - Staging
   - Production
   - (Sometimes: QA, UAT, Pre-production)

2. **Which Should Be Identical?**
   - **Staging and Production must be identical**
   - Same instance types, configurations, database versions
   - Use Terraform to ensure consistency

**Pre-Class Part 3: Observability and Monitoring**

**Key Questions:**

1. **Monitoring vs Observability Difference:**
   - **Monitoring:** Known metrics, predefined dashboards, "What is happening?"
   - **Observability:** Understanding system behavior, exploratory, "Why is it happening?"
   - **Example:**
     - Monitoring: "Error rate is 5%"
     - Observability: "Error rate is 5% because database connections are exhausted due to a memory leak in the user service introduced in last deployment"

2. **Why Need Observability in Addition to Monitoring?**
   - Complex distributed systems have emergent behaviors
   - Can't predict all failure modes
   - Need to investigate unknown issues
   - Modern microservices require correlation across services
   - **Problem Solved:** Finding root cause of novel issues

3. **Google SRE Main Concerns:**
   - Reliability as feature
   - Error budgets (balance reliability vs velocity)
   - Toil reduction (automate repetitive tasks)
   - Monitoring and alerting
   - Capacity planning
   - Change management

4. **Four Golden Signals and Importance:**
   - **Latency:** User experience, performance degradation early warning
   - **Traffic:** Capacity planning, detect anomalies
   - **Errors:** Service health, customer impact
   - **Saturation:** Predict resource exhaustion before failure
   - **Why Important:** Cover all aspects of service health with minimal metrics

5. **CloudWatch in Monitoring vs Observability:**
   - **Monitoring Framework:**
     - Metrics: Pre-defined, efficient storage
     - Alarms: Threshold-based alerting
     - Dashboards: Real-time status
   - **Observability Aspects:**
     - Logs Insights: Ad-hoc querying
     - Can correlate metrics with logs
   - **Limitation:** Best for monitoring, needs X-Ray for full observability

### Production Readiness Checklist

**Before Going to Production:**

1. **Monitoring:**
   - [ ] CloudWatch alarms for Four Golden Signals
   - [ ] Dashboard for operations team
   - [ ] Log aggregation configured
   - [ ] Alert escalation defined

2. **Security:**
   - [ ] All resources in private subnets (except load balancer)
   - [ ] Security groups follow least privilege
   - [ ] IAM roles with minimal permissions
   - [ ] Secrets in AWS Secrets Manager (not hardcoded)
   - [ ] SSL/TLS certificates configured

3. **High Availability:**
   - [ ] Multi-AZ deployment
   - [ ] Auto Scaling configured
   - [ ] Load balancer health checks
   - [ ] Database replication

4. **Backup and Recovery:**
   - [ ] Automated backups enabled
   - [ ] Tested restore procedures
   - [ ] Disaster recovery plan
   - [ ] RTO/RPO defined

5. **Performance:**
   - [ ] Load testing completed
   - [ ] Capacity planning done
   - [ ] Caching configured
   - [ ] Database query optimization

6. **Deployment:**
   - [ ] CI/CD pipeline tested
   - [ ] Rollback procedure defined
   - [ ] Blue-green or canary deployment
   - [ ] Feature flags for risky changes

### Key Technologies
- CloudWatch (Metrics, Logs, Alarms, Dashboards)
- CloudWatch Logs Insights
- AWS X-Ray (distributed tracing)
- Terraform (environment parity)
- SNS (alerting)
- Auto Scaling
- SRE practices
- Monitoring tools (Prometheus, Grafana)
- Observability platforms (Datadog, Honeycomb)

---

## <a name="technologies-reference"></a>Key Technologies Reference

### AWS Services Covered

| Service | Category | Purpose | Week |
|---------|----------|---------|------|
| EC2 | Compute | Virtual servers | 1, 2, 3, 4, 5, 7 |
| Lambda | Compute | Serverless functions | 4 |
| S3 | Storage | Object storage, static websites | 7, 9 |
| EBS | Storage | Block storage for EC2 | 7 |
| RDS | Database | Managed relational databases | 8 |
| VPC | Networking | Isolated cloud network | 8, 9 |
| Internet Gateway | Networking | VPC to internet connection | 8 |
| NAT Gateway | Networking | Private subnet internet access | 8, 9 |
| ALB/NLB | Networking | Load balancing | 9 |
| CloudFront | Networking | Content Delivery Network | 9 |
| Route53 | Networking | DNS service | 9 |
| IAM | Security | Identity and access management | 7 |
| Security Groups | Security | Instance-level firewall | 2, 8 |
| Network ACLs | Security | Subnet-level firewall | 8 |
| SSM | Management | Systems Manager | 7 |
| CloudWatch | Monitoring | Metrics, logs, alarms | 4, 10 |
| EventBridge | Integration | Event-driven automation | 4 |
| ACM | Security | Certificate management | 7, 9 |

### Development Tools

| Tool | Purpose | Week |
|------|---------|------|
| GitHub Actions | CI/CD automation | 4, 5, 7 |
| Docker | Containerization | 1, 5 |
| Docker Compose | Multi-container orchestration | 5, 6 |
| Terraform | Infrastructure as Code | 9 |
| Git | Version control | 2, 3 |
| Python/Flask | Web applications | 2-7 |
| boto3 | AWS SDK for Python | 4, 7 |
| Gunicorn | Python WSGI server | 3 |

### Concepts and Practices

| Concept | Description | Week |
|---------|-------------|------|
| CI/CD | Continuous Integration/Deployment | 4, 5, 7, 9 |
| IaC | Infrastructure as Code | 9, 10 |
| Microservices | Distributed architecture | 5, 6 |
| SRE | Site Reliability Engineering | 10 |
| Monitoring | Known metrics tracking | 10 |
| Observability | System behavior understanding | 10 |
| Least Privilege | Minimal permissions | 7 |
| Environment Parity | Consistent staging/production | 10 |

---

## <a name="commands-tools"></a>Common Commands and Tools

### AWS CLI Commands

```bash
# EC2 Commands
aws ec2 describe-instances
aws ec2 run-instances --image-id ami-xxx --instance-type t2.micro
aws ec2 start-instances --instance-ids i-xxx
aws ec2 stop-instances --instance-ids i-xxx
aws ec2 describe-key-pairs
aws ec2 import-key-pair --key-name mykey --public-key-material fileb://~/.ssh/id_rsa.pub

# Lambda Commands
aws lambda invoke --function-name myfunction output.json
aws lambda list-functions

# SSM Commands
aws ssm send-command --instance-ids i-xxx --document-name "AWS-RunShellScript"
aws ssm get-command-invocation --command-id xxx --instance-id i-xxx

# S3 Commands
aws s3 ls
aws s3 cp file.txt s3://bucket/
aws s3 sync ./dist s3://bucket/ --delete

# CloudWatch Commands
aws cloudwatch put-metric-data --namespace MyApp --metric-name ResponseTime --value 200
aws logs tail /aws/lambda/myfunction --follow

# IAM Commands
aws iam list-users
aws iam list-roles
aws iam get-role --role-name MyRole
```

### Docker Commands

```bash
# Build and Run
docker build -t image:tag .
docker run -d -p 80:5000 image:tag
docker run -it image:tag /bin/bash

# Management
docker ps                    # List running containers
docker ps -a                 # List all containers
docker images                # List images
docker logs container_id     # View logs
docker exec -it container_id bash  # Enter container
docker stop container_id     # Stop container
docker rm container_id       # Remove container
docker rmi image:tag         # Remove image

# Cleanup
docker system prune -a       # Remove all unused images/containers
docker volume prune          # Remove unused volumes

# Docker Compose
docker compose up            # Start services
docker compose up -d         # Start in background
docker compose down          # Stop and remove
docker compose ps            # List services
docker compose logs -f       # Follow logs
```

### Git Commands

```bash
# Basic Operations
git clone git@github.com:user/repo.git
git checkout branch-name
git branch -a                # List all branches
git add .
git commit -m "message"
git push origin branch

# SSH Key Setup
ssh-keygen -t ed25519 -C "email@example.com"
ssh-add ~/.ssh/id_ed25519
cat ~/.ssh/id_ed25519.pub    # Copy to GitHub
```

### Linux Commands (from Week 2 Bandit)

```bash
# File Operations
cat file.txt                 # Display file
cat -                        # Read from stdin
cat "file with spaces"       # Handle spaces
cat ./- or cat ./-           # Files starting with -

# Directory Operations
ls -la                       # List with hidden files, long format
cd directory                 # Change directory
pwd                          # Print working directory

# Search Commands
find / -name filename        # Find by name
find / -size 1033c           # Find by size
find / -user user -group grp # Find by owner
grep "pattern" file          # Search in file
grep -r "pattern" .          # Recursive search

# Combining Commands
sort file.txt | uniq         # Remove duplicates
cat file.txt | grep "word"   # Filter output
strings binary_file          # Extract readable strings

# File Permissions
chmod 400 file               # Read-only for owner
chmod 600 file               # Read-write for owner
ls -l                        # View permissions
```

### Python/Flask Development

```bash
# Virtual Environment
python -m venv env
source env/bin/activate      # Linux/Mac
env\Scripts\activate         # Windows
deactivate                   # Exit venv

# Package Management
pip install -r requirements.txt
pip freeze > requirements.txt
pip list

# Running Application
python app.py                # Development
gunicorn -w 4 -b 0.0.0.0:80 app:app  # Production
```

### SSH Commands

```bash
# Basic SSH
ssh user@host
ssh -i keyfile user@host
ssh -p 2220 user@host        # Non-default port

# SSH Key Generation
ssh-keygen -t ed25519 -C "comment"
ssh-keygen -t rsa -b 4096

# SSH Config (~/.ssh/config)
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/github_key

# File Transfer
scp file.txt user@host:/path/
rsync -avz source/ user@host:/dest/
```

### Terraform Commands

```bash
# Initialize
terraform init

# Planning and Applying
terraform plan               # Preview changes
terraform apply              # Apply changes
terraform apply -auto-approve  # Skip confirmation

# State Management
terraform show               # Show current state
terraform state list         # List resources
terraform state show aws_vpc.main

# Destroy
terraform destroy            # Remove all resources
terraform destroy -target=aws_instance.example  # Remove specific resource

# Formatting and Validation
terraform fmt                # Format code
terraform validate           # Validate syntax
```

### Useful Bash Shortcuts

```bash
# Navigation
Ctrl + A                     # Beginning of line
Ctrl + E                     # End of line
Ctrl + U                     # Delete to beginning
Ctrl + K                     # Delete to end
Ctrl + W                     # Delete word
Ctrl + R                     # Search command history

# Process Management
Ctrl + C                     # Kill current process
Ctrl + Z                     # Suspend process
bg                           # Run in background
fg                           # Bring to foreground
jobs                         # List background jobs

# Output
|                            # Pipe output
>                            # Redirect output (overwrite)
>>                           # Redirect output (append)
2>&1                         # Redirect stderr to stdout
```

---

## Study Tips and Resources

### For Midterm/Final Preparation

1. **Understand Concepts, Not Just Commands:**
   - Why use NAT Gateway vs Internet Gateway?
   - When to use Security Groups vs Network ACLs?
   - Stateful vs stateless firewalls

2. **Practice Labs:**
   - Redo all activities from scratch
   - Time yourself on deployments
   - Break things and fix them

3. **Draw Diagrams:**
   - VPC architectures
   - CI/CD pipelines
   - Microservices communication
   - Request flow through load balancers

4. **Security Best Practices:**
   - Least privilege principle
   - Private subnets for databases
   - IAM roles vs users
   - When to use SSH vs SSM

5. **Key Comparisons:**
   - Monolith vs Microservices
   - Monitoring vs Observability
   - Vertical vs Horizontal Scaling
   - Type 1 vs Type 2 Hypervisors
   - Containers vs VMs

### Additional Resources

- **Learn2Cloud:** https://learntocloud.guide/
- **Course Repo:** https://github.com/aanchan/cs6620-fall2025-public
- **AWS Documentation:** https://docs.aws.amazon.com/
- **Docker Documentation:** https://docs.docker.com/
- **GitHub Actions Docs:** https://docs.github.com/en/actions

### Common Gotchas

1. **AWS Learner Lab:**
   - Credentials expire every 4 hours
   - Must include session token
   - Resources deleted after session

2. **Docker:**
   - Port mapping: host:container (80:5000)
   - Don't forget .dockerignore
   - Remove old containers before rerunning

3. **Security Groups:**
   - Stateful - return traffic automatic
   - Only allow rules
   - Can reference other security groups

4. **IAM:**
   - Attach policies to groups, not users
   - Roles have no credentials
   - Least privilege always

5. **GitHub Actions:**
   - Use secrets for credentials
   - `needs:` for job dependencies
   - Workflows run on push/PR

---

## Project Tips

### Best Practices for Final Project

1. **Architecture:**
   - Draw complete architecture diagram
   - Show all AWS services used
   - Include security components
   - Document data flow

2. **Security:**
   - Database in private subnet
   - Use IAM roles (not access keys)
   - Security groups with least privilege
   - HTTPS for all public endpoints

3. **Automation:**
   - Complete CI/CD pipeline
   - Infrastructure as Code (Terraform)
   - Automated testing
   - Automated deployment

4. **Monitoring:**
   - CloudWatch metrics and alarms
   - Centralized logging
   - Dashboard for key metrics
   - Alert on Four Golden Signals

5. **Documentation:**
   - Clear README
   - Architecture diagram
   - Deployment instructions
   - API documentation
   - Troubleshooting guide

6. **Presentation:**
   - Demo working application
   - Explain architecture decisions
   - Show CI/CD in action
   - Discuss trade-offs made
   - Answer peer questions thoroughly

---

## Final Notes

This course covers the fundamentals of modern cloud computing practices:

- **Weeks 1-3:** Foundations (virtualization, SSH, networking, deployment)
- **Weeks 4-5:** Automation and containers (CI/CD, Docker, Lambda)
- **Weeks 6-7:** Architecture and security (microservices, IAM, automated deployment)
- **Weeks 8-9:** Advanced networking (VPC, load balancing, IaC)
- **Week 10:** Production practices (monitoring, observability, SRE)

**Key Takeaways:**
1. Automation is essential for modern cloud deployments
2. Security through least privilege and network segmentation
3. Infrastructure as Code for consistency and reproducibility
4. Monitoring and observability for production reliability
5. CI/CD pipelines for rapid, safe deployments

**Skills Developed:**
- AWS cloud service configuration and management
- Docker containerization and deployment
- GitHub Actions CI/CD pipelines
- Infrastructure as Code with Terraform
- Security best practices
- Networking in cloud environments
- System design and architecture

Good luck with your studies and projects!

---

*This summary compiled from course materials for CS6620 Fall 2025*
*For questions or clarifications, refer to course slides, videos, and AWS documentation*
