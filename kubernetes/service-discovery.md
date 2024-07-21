# Kubernetes Tutorial: Service Discovery with Env and DNS for a 3-Tier Application

In this tutorial, we will deploy a 3-tier application (frontend, backend, and database) on a Kubernetes cluster. We will demonstrate how to use service discovery with environment variables and DNS to enable communication between the different tiers.

## Prerequisites

- Minikube installed and running.
- kubectl installed and configured to interact with your Minikube cluster.

## Application Overview

- **Frontend**: A web server that serves the user interface.
- **Backend**: An application server that provides business logic.
- **Database**: A database server that stores application data.

## Step 1: Create the Database Deployment and Service

1. Create a file named `db-deployment.yaml`:

    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: db-deployment
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: db
      template:
        metadata:
          labels:
            app: db
        spec:
          containers:
          - name: db
            image: postgres:latest
            env:
            - name: POSTGRES_DB
              value: mydatabase
            - name: POSTGRES_USER
              value: myuser
            - name: POSTGRES_PASSWORD
              value: mypassword
            ports:
            - containerPort: 5432
    ```

2. Create a file named `db-service.yaml`:

    ```yaml
    apiVersion: v1
    kind: Service
    metadata:
      name: db-service
    spec:
      selector:
        app: db
      ports:
      - protocol: TCP
        port: 5432
        targetPort: 5432
      clusterIP: None
    ```

3. Apply the configurations:

    ```bash
    kubectl apply -f db-deployment.yaml
    kubectl apply -f db-service.yaml
    ```

## Step 2: Create the Backend Deployment and Service

1. Create a file named `backend-deployment.yaml`:

    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: backend-deployment
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: backend
      template:
        metadata:
          labels:
            app: backend
        spec:
          containers:
          - name: backend
            image: your-backend-image:latest
            env:
            - name: DB_HOST
              value: db-service
            - name: DB_USER
              valueFrom:
                secretKeyRef:
                  name: db-credentials
                  key: user
            - name: DB_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: db-credentials
                  key: password
            - name: DB_NAME
              valueFrom:
                configMapKeyRef:
                  name: db-config
                  key: database
            ports:
            - containerPort: 8080
    ```

2. Create a file named `backend-service.yaml`:

    ```yaml
    apiVersion: v1
    kind: Service
    metadata:
      name: backend-service
    spec:
      selector:
        app: backend
      ports:
      - protocol: TCP
        port: 8080
        targetPort: 8080
      clusterIP: None
    ```

3. Apply the configurations:

    ```bash
    kubectl apply -f backend-deployment.yaml
    kubectl apply -f backend-service.yaml
    ```

## Step 3: Create the Frontend Deployment and Service

1. Create a file named `frontend-deployment.yaml`:

    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: frontend-deployment
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: frontend
      template:
        metadata:
          labels:
            app: frontend
        spec:
          containers:
          - name: frontend
            image: your-frontend-image:latest
            env:
            - name: BACKEND_HOST
              value: backend-service
            ports:
            - containerPort: 80
    ```

2. Create a file named `frontend-service.yaml`:

    ```yaml
    apiVersion: v1
    kind: Service
    metadata:
      name: frontend-service
    spec:
      selector:
        app: frontend
      ports:
      - protocol: TCP
        port: 80
        targetPort: 80
      type: NodePort
    ```

3. Apply the configurations:

    ```bash
    kubectl apply -f frontend-deployment.yaml
    kubectl apply -f frontend-service.yaml
    ```

## Step 4: Create ConfigMap and Secret for Database Credentials

1. Create a file named `db-config.yaml`:

    ```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
      name: db-config
    data:
      database: mydatabase
    ```

2. Create a file named `db-secret.yaml`:

    ```yaml
    apiVersion: v1
    kind: Secret
    metadata:
      name: db-credentials
    type: Opaque
    data:
      user: bXl1c2Vy
      password: bXlwYXNzd29yZA==
    ```

    Note: The values for `user` and `password` are base64-encoded.

3. Apply the configurations:

    ```bash
    kubectl apply -f db-config.yaml
    kubectl apply -f db-secret.yaml
    ```

## Accessing the Application

### Accessing the Frontend Service

1. Get the NodePort assigned to the frontend service:

    ```bash
    kubectl get service frontend-service
    ```

    The output will show a `NodePort` like `30000:80/TCP`. Note the port number.

2. Access the frontend application using the Minikube IP and NodePort:

    ```bash
    minikube service frontend-service --url
    ```

    This command will open the URL of the frontend service in your default web browser.

### Verifying Service Discovery

- **Frontend to Backend**: The frontend uses the environment variable `BACKEND_HOST=backend-service` to communicate with the backend.
- **Backend to Database**: The backend uses the environment variable `DB_HOST=db-service` to communicate with the database.

The services `backend-service` and `db-service` are resolved using Kubernetes DNS.

## Conclusion

In this tutorial, we've shown how to deploy a 3-tier application on a Kubernetes cluster, using service discovery with environment variables and DNS to enable communication between the frontend, backend, and database components.

By following these steps, you can effectively manage and scale your applications on Kubernetes while ensuring seamless communication between different tiers of your application.

## Additional Resources

- [Kubernetes Services Documentation](https://kubernetes.io/docs/concepts/services-networking/service/)
- [Kubernetes ConfigMaps Documentation](https://kubernetes.io/docs/concepts/configuration/configmap/)
- [Kubernetes Secrets Documentation](https://kubernetes.io/docs/concepts/configuration/secret/)
- [Minikube Documentation](https://minikube.sigs.k8s.io/docs/)

Happy Kubernetes-ing!
