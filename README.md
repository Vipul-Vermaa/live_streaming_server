# Live Streaming Server 

This project sets up a live streaming server with authentication, Prometheus monitoring, and a chat feature using Docker. It includes the backend setup using Express.js, WebSocket, and Socket.io for live interactions, and integrates Prometheus for monitoring and logging.

## Prerequisites

- Docker and Docker Compose
- Node.js and npm
- OBS Studio (for streaming)

```bash
pip install foobar
```

## Setup Instructions
## 1. Clone the Repository 

```bash
git clone https://github.com/Vipul-Vermaa/live_streaming_server.git
cd live_streaming_server
```

## 2. Backend Setup
Navigate to the backend directory and install dependencies.

```bash
cd backend
npm install
```

## 3. Frontend Setup
Navigate to the frontend directory and install dependencies.
```bash
cd ../frontend
npm install
```
## Environment Variables
Create a config.env file in the backend/config directory and add the following variables:

```bash
PORT=4000
MONGO_URI=your_mongodb_connection_string
JWT_SECRET=your_jwt_secret
VITE_SERVER=http://localhost:4000
```
## Docker Configuration
### Dockerfile for Backend
Create a Dockerfile in the backend directory:

```bash
FROM node:12
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install
COPY . .
CMD ["npm", "start"]
```
### Docker Compose File
Create a docker-compose.yml in the root directory:
```bash
version: "3.9"
services:
  rtmp:
    build: ./rtmp
    ports:
      - "1935:1935"
      - "8080:8080"
    container_name: rtmp_server
    volumes:
      - ./data:/tmp/hls
  
  backend:
    build: ./backend
    container_name: backend_server
    environment:
      - PORT=4000
      - MONGO_URI=${MONGO_URI}
      - JWT_SECRET=${JWT_SECRET}
    ports:
      - "4000:4000"
    depends_on:
      - rtmp

  frontend:
    build: ./frontend
    container_name: frontend_server
    ports:
      - "3000:3000"
    depends_on:
      - backend

  prometheus:
    image: prom/prometheus
    ports:
      - "9090:9090"
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
```
### Prometheus Configuration
Create a prometheus.yml file in the root directory:
```bash
global:
  scrape_interval: 5s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['backend_server:4000']
```
## Run the Application
1. Start all services using Docker Compose:
```bash 
docker-compose up --build
```