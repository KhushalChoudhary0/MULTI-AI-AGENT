# 🤖 Multi AI Agent

A production-ready **Multi-AI Agent** system powered by **LangChain**, **LangGraph**, and **Groq LLMs**, with an optional **Tavily** web-search capability. The project ships with a **FastAPI** backend, a **Streamlit** frontend, and a fully automated **CI/CD pipeline** using **Jenkins**, **SonarQube**, **Docker**, and **AWS ECS Fargate**.

---

## ✨ Features

- 🧠 **Multi-Model Support** — Choose between `llama-3.3-70b-versatile`, `llama3-8b-8192`, and `mixtral-8x7b-32768` via Groq. *(Note: Choose models accordingly as some may occasionally be unavailable or under maintenance)*
- 🔍 **Optional Web Search** — Toggle real-time web search powered by Tavily
- 🎯 **Custom System Prompts** — Define the AI agent's persona and behaviour on the fly
- ⚡ **FastAPI Backend** — High-performance async REST API on port `9999`
- 🖥️ **Streamlit Frontend** — Clean, interactive chat UI on port `8501`
- 🐳 **Dockerized** — Single-command containerised deployment
- 🔄 **CI/CD Pipeline** — Jenkins pipeline with SonarQube analysis, ECR push, and ECS Fargate deployment
- 📝 **Structured Logging** — Daily rotating log files with custom exception handling

---

## 📁 Project Structure

```
MULTI-AI-AGENT/
├── app/
│   ├── __init__.py
│   ├── main.py                  # Entrypoint — launches backend & frontend
│   ├── backend/
│   │   ├── __init__.py
│   │   └── api.py               # FastAPI REST API (/chat endpoint)
│   ├── frontend/
│   │   ├── __init__.py
│   │   └── ui.py                # Streamlit chat interface
│   ├── core/
│   │   ├── __init__.py
│   │   └── ai_agent.py          # LangGraph ReAct agent logic
│   ├── config/
│   │   ├── __init__.py
│   │   └── settings.py          # Environment & model configuration
│   └── common/
│       ├── __init__.py
│       ├── logger.py            # Logging setup
│       └── custom_exception.py  # Custom exception with traceback details
├── custom_jenkins/
│   └── Dockerfile               # Jenkins Docker-in-Docker image
├── Dockerfile                   # Application Docker image
├── Jenkinsfile                  # CI/CD pipeline definition
├── setup.py                     # Package setup
├── requirements.txt             # Python dependencies
├── .env                         # Environment variables (git-ignored)
├── .gitignore
└── README.md
```

---

## 🏗️ Architecture

```
┌────────────────────┐         ┌────────────────────┐
│   Streamlit UI     │  HTTP   │   FastAPI Backend   │
│   (port 8501)      │───────▶│   (port 9999)       │
└────────────────────┘         └─────────┬──────────┘
                                         │
                               ┌─────────▼──────────┐
                               │  LangGraph ReAct    │
                               │  Agent (Groq LLM)   │
                               └─────────┬──────────┘
                                         │
                              ┌──────────▼──────────┐
                              │  Tavily Web Search   │
                              │  (optional)          │
                              └─────────────────────┘
```

**How it works:**

1. The user enters a query and selects a model in the **Streamlit** frontend
2. The frontend sends a `POST` request to the **FastAPI** backend at `/chat`
3. The backend creates a **LangGraph ReAct agent** using the selected **Groq LLM**
4. If web search is enabled, the agent can use **Tavily** to fetch real-time information
5. The agent processes the query and returns the response to the frontend

---

## 🚀 Quick Start

### Prerequisites

- Python 3.10+
- [Groq API Key](https://console.groq.com/)
- [Tavily API Key](https://tavily.com/) *(optional — required only for web search)*

### 1. Clone the Repository

```bash
git clone https://github.com/KhushalChoudhary0/MULTI-AI-AGENT.git
cd MULTI-AI-AGENT
```

### 2. Create and Activate a Virtual Environment

```bash
python -m venv venv

# Windows
venv\Scripts\activate

# Linux / macOS
source venv/bin/activate
```

### 3. Install Dependencies

```bash
pip install -e .
```

### 4. Configure Environment Variables

Create a `.env` file in the project root:

```env
GROQ_API_KEY=gsk_your_groq_api_key_here
TAVILY_API_KEY=tvly-your_tavily_api_key_here
```

### 5. Run the Application

```bash
python app/main.py
```

This launches both services concurrently:
- **Backend API** → `http://127.0.0.1:9999`  
- **Streamlit UI** → `http://localhost:8501`

---

## 🐳 Docker

### Build and Run Locally

```bash
docker build -t multi-ai-agent .
docker run -p 8501:8501 -p 9999:9999 --env-file .env multi-ai-agent
```

Open `http://localhost:8501` to access the app.

---

## 🔧 API Reference

### `POST /chat`

Send a query to the AI agent.

**Request Body:**

```json
{
  "model_name": "llama-3.3-70b-versatile",
  "system_prompt": "You are a helpful assistant",
  "messages": ["What is LangGraph?"],
  "allow_search": true
}
```

**Response:**

```json
{
  "response": "LangGraph is a library for building stateful, multi-actor applications with LLMs..."
}
```

**Supported Models:**

| Model | Description |
|---|---|
| `llama-3.3-70b-versatile` | Llama 3.3 70B — high capability, versatile |
| `llama3-8b-8192` | Llama 3 8B — fast, 8K context |
| `mixtral-8x7b-32768` | Mixtral 8x7B MoE — 32K context |

> [!TIP]
> **Model Availability:** Please choose your model accordingly. AI models can sometimes be unavailable or "not in use" due to provider maintenance or rate limits. If you encounter an error, try switching to a different model from the list above.

---

## 🔄 CI/CD Pipeline

The project uses a **Jenkins** pipeline with four stages, fully automated from code push to production deployment.

```
┌──────────┐    ┌─────────────┐    ┌──────────────┐    ┌──────────────────┐
│ Checkout │───▶│  SonarQube  │───▶│ Build & Push │───▶│ Deploy to ECS    │
│ (GitHub) │    │  Analysis   │    │  (AWS ECR)   │    │  (Fargate)       │
└──────────┘    └─────────────┘    └──────────────┘    └──────────────────┘
```

### Pipeline Stages

| Stage | Description |
|---|---|
| **Checkout** | Clones the repository from GitHub |
| **SonarQube Analysis** | Runs static code analysis for quality & security |
| **Build & Push to ECR** | Builds Docker image and pushes to Amazon ECR |
| **Deploy to ECS Fargate** | Forces a new deployment on the ECS service |

---

## ☁️ AWS Deployment Guide

### Prerequisites

- AWS Account with IAM user having `AmazonEC2ContainerRegistryFullAccess` and `AmazonECS_FullAccess` policies
- AWS CLI installed
- Docker installed

### Step 1 — Jenkins Setup (Docker-in-Docker)

1. **Build the custom Jenkins image** from `custom_jenkins/`:

    ```bash
    cd custom_jenkins
    docker build -t jenkins-dind .
    ```

2. **Run the Jenkins container:**

    ```bash
    docker run -d --name jenkins-dind \
      --privileged \
      -p 8080:8080 -p 50000:50000 \
      -v /var/run/docker.sock:/var/run/docker.sock \
      -v jenkins_home:/var/jenkins_home \
      jenkins-dind
    ```

3. **Get the initial admin password:**

    ```bash
    docker logs jenkins-dind
    ```

4. **Install Python inside the container:**

    ```bash
    docker exec -u root -it jenkins-dind bash
    apt update -y && apt install -y python3 python3-pip
    ln -s /usr/bin/python3 /usr/bin/python
    exit
    ```

5. Access Jenkins at `http://<YOUR_IP>:8080` and complete the setup wizard.

---

### Step 2 — GitHub Integration

1. Generate a **Personal Access Token** on GitHub (`Settings → Developer Settings → Personal Access Tokens → Classic`) with `repo` and `repo_hook` scopes
2. In Jenkins, go to **Manage Jenkins → Credentials → Global** and add the token as a **Username/Password** credential (ID: `github-token`)
3. Create a new **Pipeline** job pointing to your repository

---

### Step 3 — SonarQube Integration

1. **Run SonarQube container:**

    ```bash
    docker run -d --name sonarqube-dind -p 9000:9000 sonarqube
    ```

2. **Create a Docker network** and connect both containers:

    ```bash
    docker network create dind-network
    docker network connect dind-network jenkins-dind
    docker network connect dind-network sonarqube-dind
    ```

3. Access SonarQube at `http://<YOUR_IP>:9000` (default credentials: `admin` / `admin`)

4. **Install Jenkins plugins:** `SonarScanner` and `SonarQualityGates`

5. **Configure in Jenkins:**
   - Create a SonarQube project and generate an analysis token
   - Add the token as a **Secret Text** credential in Jenkins (ID: `sonarqube-token`)
   - Configure the SonarQube server URL under **Manage Jenkins → System → SonarQube Servers**
   - Set up **SonarQube Scanner** under **Manage Jenkins → Tools** with auto-install enabled

---

### Step 4 — AWS ECR Setup (Build & Push)

1. **Install Jenkins plugins:** `AWS SDK` and `AWS Credentials`

2. **Create IAM user** with `AmazonEC2ContainerRegistryFullAccess` policy and generate access keys

3. **Add AWS credentials** to Jenkins (ID: `aws-credentials`, type: **AWS Credentials**)

4. **Install AWS CLI** inside the Jenkins container:

    ```bash
    docker exec -u root -it jenkins-dind bash
    apt update && apt install -y unzip curl
    curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
    unzip awscliv2.zip && ./aws/install
    aws --version
    exit
    ```

5. **Create an ECR repository** in the AWS Console (`ECR → Create Repository`)

---

### Step 5 — AWS ECS Fargate Deployment

1. **Create an ECS Cluster** (`ECS → Clusters → Create Cluster` → select **Fargate**)

2. **Create a Task Definition:**
   - Launch type: **Fargate**
   - Container image: your ECR repository URI
   - Port mapping: `8501` (TCP)
   - Add environment variables: `GROQ_API_KEY` and `TAVILY_API_KEY`

3. **Create an ECS Service:**
   - Select your task definition
   - Enable **Public IP** assignment

4. **Configure Security Group:**
   - Add inbound rule: **Custom TCP**, port `8501`, source `0.0.0.0/0`

5. **Attach additional IAM policy:** `AmazonECS_FullAccess` to your IAM user

6. **Trigger the Jenkins pipeline** — it will build, push, and deploy automatically

7. **Access your app** at `http://<ECS_PUBLIC_IP>:8501`

---

## 🛠️ Tech Stack

| Category | Technology |
|---|---|
| **LLM Provider** | [Groq](https://groq.com/) (Llama 3.3, Llama 3, Mixtral) |
| **AI Framework** | [LangChain](https://langchain.com/) + [LangGraph](https://langchain-ai.github.io/langgraph/) |
| **Web Search** | [Tavily](https://tavily.com/) |
| **Backend** | [FastAPI](https://fastapi.tiangolo.com/) |
| **Frontend** | [Streamlit](https://streamlit.io/) |
| **Containerization** | [Docker](https://www.docker.com/) |
| **CI/CD** | [Jenkins](https://www.jenkins.io/) (Docker-in-Docker) |
| **Code Quality** | [SonarQube](https://www.sonarqube.org/) |
| **Container Registry** | [Amazon ECR](https://aws.amazon.com/ecr/) |
| **Deployment** | [AWS ECS Fargate](https://aws.amazon.com/fargate/) |

---

## 🐧 WSL Setup (Windows Users)

If you're on Windows and need Docker + Jenkins, set up WSL first:

1. **Enable WSL:**

    ```powershell
    wsl --install
    ```

2. **Install Ubuntu** from the Microsoft Store (e.g., Ubuntu 22.04 LTS)

3. **Install Docker Engine** inside Ubuntu WSL:

    ```bash
    sudo apt update
    sudo apt install ca-certificates curl gnupg lsb-release -y
    sudo mkdir -p /etc/apt/keyrings
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
      https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | \
      sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    sudo apt update
    sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
    sudo usermod -aG docker $USER
    ```

4. **Restart the terminal** and verify: `docker --version`

> All Jenkins and SonarQube commands in this guide should be run inside the WSL terminal.

---

## 📄 Environment Variables

| Variable | Required | Description |
|---|---|---|
| `GROQ_API_KEY` | ✅ | API key from [Groq Console](https://console.groq.com/) |
| `TAVILY_API_KEY` | ⚠️ | API key from [Tavily](https://tavily.com/) — required only if web search is enabled |

---

## 📜 License

This project is open source and available under the [MIT License](LICENSE).

---

<p align="center">
  Built with ❤️ by <a href="https://github.com/KhushalChoudhary0">KhushalChoudhary0</a>
</p>
