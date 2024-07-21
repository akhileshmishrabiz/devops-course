# Kubernetes Tutorial: Deploying Nginx with Pod, Service, and Deployment

This tutorial will guide you through deploying an Nginx web server on a Kubernetes cluster using Minikube. We will cover Pods, Deployments, and Services, and show you how to access your application using different Service types.

## Prerequisites

- Minikube installed and running.
- kubectl installed and configured to interact with your Minikube cluster.

## Step 1: Create a Pod

A Pod is the smallest deployable unit in Kubernetes, representing a single instance of a running process.

### Create a Pod

1. Create a file named `nginx-pod.yaml`:

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx-pod
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
    ```

2. Apply the configuration to your cluster:

    ```bash
    kubectl apply -f nginx-pod.yaml
    ```

3. Verify that the Pod is running:

    ```bash
    kubectl get pods
    ```

### Access the Pod Directly

1. Get the Pod's IP address:

    ```bash
    kubectl get pod nginx-pod -o jsonpath='{.status.podIP}'
    ```

2. You can access the Nginx server using the Pod's IP address. Note that this is typically only accessible within the cluster.

## Step 2: Create a Deployment

A Deployment ensures that a specified number of Pods are running at all times and provides updates to Pods in a controlled way.

### Create a Deployment

1. Create a file named `nginx-deployment.yaml`:

    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx-deployment
    spec:
      replicas: 3
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:latest
            ports:
            - containerPort: 80
    ```

2. Apply the configuration to your cluster:

    ```bash
    kubectl apply -f nginx-deployment.yaml
    ```

3. Verify that the Deployment is running:

    ```bash
    kubectl get deployments
    kubectl get pods
    ```

## Step 3: Create a Service

A Service exposes your application running on a set of Pods as a network service.

### Create a ClusterIP Service

A `ClusterIP` Service exposes the application within the cluster, making it accessible only from within the cluster.

1. Create a file named `nginx-clusterip-service.yaml`:

    ```yaml
    apiVersion: v1
    kind: Service
    metadata:
      name: nginx-clusterip-service
    spec:
      selector:
        app: nginx
      ports:
        - protocol: TCP
          port: 80
          targetPort: 80
      type: ClusterIP
    ```

2. Apply the configuration to your cluster:

    ```bash
    kubectl apply -f nginx-clusterip-service.yaml
    ```

3. Verify that the Service is running:

    ```bash
    kubectl get services
    ```

### Access the ClusterIP Service

1. Get the ClusterIP:

    ```bash
    kubectl get service nginx-clusterip-service -o jsonpath='{.spec.clusterIP}'
    ```

2. You can access the Nginx server using the ClusterIP within the cluster.

### Create a NodePort Service

A `NodePort` Service exposes the application on each Node's IP at a static port. This makes the application accessible outside the cluster.

1. Create a file named `nginx-nodeport-service.yaml`:

    ```yaml
    apiVersion: v1
    kind: Service
    metadata:
      name: nginx-nodeport-service
    spec:
      selector:
        app: nginx
      ports:
        - protocol: TCP
          port: 80
          targetPort: 80
          nodePort: 30007
      type: NodePort
    ```

2. Apply the configuration to your cluster:

    ```bash
    kubectl apply -f nginx-nodeport-service.yaml
    ```

3. Verify that the Service is running:

    ```bash
    kubectl get services
    ```

### Access the NodePort Service

1. Get the Minikube IP:

    ```bash
    minikube ip
    ```

2. Access the Nginx server using the Minikube IP and NodePort:

    ```bash
    curl http://<minikube-ip>:30007
    ```

## Step 4: Access the Nginx Service via LoadBalancer

For demonstration purposes, Minikube supports LoadBalancer services by opening a tunnel.

1. Create a file named `nginx-loadbalancer-service.yaml`:

    ```yaml
    apiVersion: v1
    kind: Service
    metadata:
      name: nginx-loadbalancer-service
    spec:
      selector:
        app: nginx
      ports:
        - protocol: TCP
          port: 80
          targetPort: 80
      type: LoadBalancer
    ```

2. Apply the configuration to your cluster:

    ```bash
    kubectl apply -f nginx-loadbalancer-service.yaml
    ```

3. Start the Minikube tunnel:

    ```bash
    minikube tunnel
    ```

4. Get the LoadBalancer IP:

    ```bash
    kubectl get service nginx-loadbalancer-service -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
    ```

5. Access the Nginx server using the LoadBalancer IP:

    ```bash
    curl http://<loadbalancer-ip>
    ```

## Summary

- **Pod:** Represents a single instance of a running process in your cluster.
- **Deployment:** Manages a set of identical Pods, ensuring they are running and can update them as needed.
- **Service:** Exposes your application to the network, allowing it to be accessible inside or outside the cluster.

By following these steps, you should be able to deploy a simple Nginx web server on your Minikube cluster using Kubernetes. Feel free to modify the YAML files and explore further!

## Additional Resources

- [Kubernetes Documentation](https://kubernetes.io/docs/home/)
- [Minikube Documentation](https://minikube.sigs.k8s.io/docs/)

Happy Kubernetes-ing!
