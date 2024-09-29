
# Example Voting App

A simple distributed application running across multiple Docker containers.

## Table of Contents

- [Getting Started](#getting-started)
- [Architecture](#architecture)
- [Running the App](#running-the-app)
  - [Using Docker Compose](#using-docker-compose)
  - [Running on Docker Swarm](#running-on-docker-swarm)
  - [Running in Kubernetes](#running-in-kubernetes)
- [Environment Variables](#environment-variables)
- [Additional Docker Commands](#additional-docker-commands)
- [Notes](#notes)
- [Contributing](#contributing)

## Getting Started

Before starting, ensure you have [Docker Desktop](https://www.docker.com/products/docker-desktop) installed for Mac or Windows, or the latest version of [Docker Compose](https://docs.docker.com/compose/install/) for Linux.

This solution uses Python, Node.js, and .NET, with Redis for messaging and Postgres for storage.

## Architecture

![Architecture diagram](architecture.excalidraw.png)

The system consists of the following components:

- **Front-end (Python)**: A web app allowing users to vote between two options.
- **Redis**: A message broker collecting new votes.
- **Worker (.NET)**: Processes votes from Redis and stores them in the database.
- **Postgres**: A database for persistent storage of votes.
- **Results App (Node.js)**: Displays voting results in real time.

## Running the App

### Using Docker Compose

Run the following command to build and start the application using Docker Compose:

```bash
docker compose up
```

- The `vote` app will be available at [http://localhost:5000](http://localhost:5000).
- The `results` app will be available at [http://localhost:5001](http://localhost:5001).

You can stop the application with:

```bash
docker compose down
```

### Running on Docker Swarm

To run this application on Docker Swarm, first initialize a swarm (if not already done):

```bash
docker swarm init
```

Then, deploy the stack:

```bash
docker stack deploy --compose-file docker-stack.yml vote
```

This will run the services in swarm mode.

### Running in Kubernetes

In the `k8s-specifications` folder, you'll find the YAML specifications for deploying the Voting App on Kubernetes.

Deploy the app using:

```bash
kubectl apply -f k8s-specifications/
```

- The `vote` web app will be available on port 31000.
- The `results` web app will be available on port 31001.

To remove the app, run:

```bash
kubectl delete -f k8s-specifications/
```

## Environment Variables

You can pass environment variables to Docker containers for configuration. Example:

```bash
docker run -e APP_COLOR=blue simple-webapp-color
```

To inspect environment variables in a running container:

```bash
docker inspect <container_id_or_name>
```

Example for starting a service with an environment variable:

```bash
docker run -p 38282:8080 --name blue-app -e APP_COLOR=blue -d kodekloud/simple-webapp
```

## Additional Docker Commands

Below are some useful Docker commands that can help in managing and inspecting containers:

### Running a Simple Web App

```bash
docker run -d --name=redis redis
docker run -d --name=db postgres
docker run -d --name=vote -p 5000:80 --link redis:redis voting-app
docker run -d --name=result -p 5001:80 --link db:db result-app
```

### Building and Running Custom Docker Images

To build a custom image and run a container:

```bash
docker build -t ubuntu-sleeper .
docker run ubuntu-sleeper
```

You can also pass additional arguments at runtime:

```bash
docker run ubuntu-sleeper sleep 10
```

For entrypoints and CMD usage:

```Dockerfile
FROM ubuntu
ENTRYPOINT ["sleep"]
CMD ["5"]
```

## Notes

- The voting application allows **one vote per client**. Additional votes from the same client/browser will not be registered.
- This is a basic example of a distributed application and not an optimized production-level system.
- The purpose is to demonstrate different components and how they interact within Docker.

## Contributing

We welcome contributions! Please follow these steps to contribute:

1. Fork the repository.
2. Create a feature branch (`git checkout -b feature-branch`).
3. Commit your changes (`git commit -m 'Add new feature'`).
4. Push to the branch (`git push origin feature-branch`).
5. Open a Pull Request.

---

This README is intended to provide clear instructions on running the application and understanding its architecture. Feel free to modify and enhance it to suit your specific project needs.
