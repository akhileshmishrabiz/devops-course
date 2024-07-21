Sure, here's a guide on how to use environment variables with Docker. This includes creating a Dockerfile, building an image, and running a container with environment variables.

# Using Environment Variables with Docker

Environment variables in Docker can be set in different ways, including using the Dockerfile, the `docker run` command, and Docker Compose. Below are examples demonstrating how to set environment variables using each method.

## Method 1: Using a Dockerfile

### Step 1: Create a Dockerfile

1. Create a directory for your project and navigate into it:

    ```bash
    mkdir my-docker-app
    cd my-docker-app
    ```

2. Create a file named `Dockerfile` with the following content:

    ```Dockerfile
    # Use an official Python runtime as a parent image
    FROM python:3.9-slim

    # Set the working directory in the container
    WORKDIR /app

    # Copy the current directory contents into the container at /app
    COPY . /app

    # Install any needed packages specified in requirements.txt
    RUN pip install --no-cache-dir -r requirements.txt

    # Set environment variables
    ENV MY_VARIABLE=my_value

    # Make port 80 available to the world outside this container
    EXPOSE 80

    # Define environment variable
    ENV NAME World

    # Run app.py when the container launches
    CMD ["python", "app.py"]
    ```

3. Create a simple Python application. For example, create a file named `app.py` with the following content:

    ```python
    import os
    from flask import Flask

    app = Flask(__name__)

    @app.route('/')
    def hello():
        name = os.getenv('NAME', 'World')
        return f'Hello, {name}!'

    if __name__ == '__main__':
        app.run(host='0.0.0.0', port=80)
    ```

4. Create a `requirements.txt` file with the necessary dependencies:

    ```txt
    Flask
    ```

### Step 2: Build and Run the Docker Image

1. Build the Docker image:

    ```bash
    docker build -t my-docker-app .
    ```

2. Run the Docker container:

    ```bash
    docker run -p 4000:80 my-docker-app
    ```

3. Open your browser and navigate to `http://localhost:4000`. You should see `Hello, World!`.

## Method 2: Using the `docker run` Command

You can also set environment variables directly in the `docker run` command using the `-e` flag:

```bash
docker run -p 4000:80 -e NAME=Developer my-docker-app
```

This overrides the `NAME` environment variable set in the Dockerfile. When you visit `http://localhost:4000`, you should see `Hello, Developer!`.

## Method 3: Using Docker Compose

Docker Compose allows you to define and manage multi-container Docker applications. Here’s how you can set environment variables using Docker Compose.

### Step 1: Create a `docker-compose.yml` File

1. Create a file named `docker-compose.yml` with the following content:

    ```yaml
    version: '3'
    services:
      web:
        build: .
        ports:
          - "4000:80"
        environment:
          - NAME=ComposeUser
    ```

### Step 2: Use Docker Compose to Build and Run the Application

1. Build and run the application using Docker Compose:

    ```bash
    docker-compose up --build
    ```

2. Open your browser and navigate to `http://localhost:4000`. You should see `Hello, ComposeUser!`.

## Conclusion

In this guide, you learned how to use environment variables with Docker in three different ways:

1. Setting environment variables in a Dockerfile.
2. Setting environment variables using the `docker run` command.
3. Setting environment variables using Docker Compose.

Each method has its use cases, and you can choose the one that best fits your workflow and requirements. Environment variables are a powerful way to manage configuration settings for your Dockerized applications.
