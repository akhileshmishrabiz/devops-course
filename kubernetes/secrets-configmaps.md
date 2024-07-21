# Kubernetes Tutorial: Using Secrets and ConfigMaps with Real-World Examples

In this tutorial, you will learn how to use Secrets and ConfigMaps in Kubernetes to manage configuration data and sensitive information. We'll provide real-world examples to help you understand how to use these features effectively.

## Prerequisites

- Minikube installed and running.
- kubectl installed and configured to interact with your Minikube cluster.

## ConfigMaps

### What is a ConfigMap?

A ConfigMap is a Kubernetes object that lets you store configuration data as key-value pairs. It can be used to configure your applications without having to rebuild them.

### Creating a ConfigMap

1. Create a file named `configmap.yaml`:

    ```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
      name: example-config
    data:
      APP_NAME: "MyApp"
      APP_ENV: "development"
      LOG_LEVEL: "info"
    ```

2. Apply the configuration:

    ```bash
    kubectl apply -f configmap.yaml
    ```

3. Verify that the ConfigMap is created:

    ```bash
    kubectl get configmaps
    ```

### Using ConfigMap in a Pod

1. Create a file named `configmap-pod.yaml`:

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: configmap-pod
    spec:
      containers:
      - name: myapp-container
        image: busybox
        command: [ "/bin/sh", "-c", "env && sleep 3600" ]
        env:
        - name: APP_NAME
          valueFrom:
            configMapKeyRef:
              name: example-config
              key: APP_NAME
        - name: APP_ENV
          valueFrom:
            configMapKeyRef:
              name: example-config
              key: APP_ENV
        - name: LOG_LEVEL
          valueFrom:
            configMapKeyRef:
              name: example-config
              key: LOG_LEVEL
    ```

2. Apply the configuration:

    ```bash
    kubectl apply -f configmap-pod.yaml
    ```

3. Verify that the Pod is running and check the environment variables:

    ```bash
    kubectl exec -it configmap-pod -- env
    ```

## Secrets

### What is a Secret?

A Secret is a Kubernetes object that stores sensitive information, such as passwords, OAuth tokens, and SSH keys. Secrets are encoded in base64 to ensure the data is not easily readable.

### Creating a Secret

1. Create a file named `secret.yaml`:

    ```yaml
    apiVersion: v1
    kind: Secret
    metadata:
      name: example-secret
    type: Opaque
    data:
      USERNAME: YWRtaW4=
      PASSWORD: MWYyZDFlMmU2N2Rm
    ```

    Note: The values for `USERNAME` and `PASSWORD` are base64-encoded. You can encode your own values using the `echo -n 'your-value' | base64` command.

2. Apply the configuration:

    ```bash
    kubectl apply -f secret.yaml
    ```

3. Verify that the Secret is created:

    ```bash
    kubectl get secrets
    ```

### Using Secret in a Pod

1. Create a file named `secret-pod.yaml`:

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: secret-pod
    spec:
      containers:
      - name: myapp-container
        image: busybox
        command: [ "/bin/sh", "-c", "env && sleep 3600" ]
        env:
        - name: USERNAME
          valueFrom:
            secretKeyRef:
              name: example-secret
              key: USERNAME
        - name: PASSWORD
          valueFrom:
            secretKeyRef:
              name: example-secret
              key: PASSWORD
    ```

2. Apply the configuration:

    ```bash
    kubectl apply -f secret-pod.yaml
    ```

3. Verify that the Pod is running and check the environment variables:

    ```bash
    kubectl exec -it secret-pod -- env
    ```

## Real-World Example: Using ConfigMap and Secret Together

### Step 1: Create the ConfigMap and Secret

1. Create a file named `configmap-secret.yaml`:

    ```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
      name: example-config
    data:
      APP_NAME: "MyApp"
      APP_ENV: "production"
      LOG_LEVEL: "debug"
    
    ---
    
    apiVersion: v1
    kind: Secret
    metadata:
      name: example-secret
    type: Opaque
    data:
      DB_USERNAME: YWRtaW4=
      DB_PASSWORD: c2VjcmV0
    ```

2. Apply the configuration:

    ```bash
    kubectl apply -f configmap-secret.yaml
    ```

### Step 2: Use the ConfigMap and Secret in a Deployment

1. Create a file named `deployment.yaml`:

    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: myapp-deployment
    spec:
      replicas: 2
      selector:
        matchLabels:
          app: myapp
      template:
        metadata:
          labels:
            app: myapp
        spec:
          containers:
          - name: myapp-container
            image: busybox
            command: [ "/bin/sh", "-c", "env && sleep 3600" ]
            env:
            - name: APP_NAME
              valueFrom:
                configMapKeyRef:
                  name: example-config
                  key: APP_NAME
            - name: APP_ENV
              valueFrom:
                configMapKeyRef:
                  name: example-config
                  key: APP_ENV
            - name: LOG_LEVEL
              valueFrom:
                configMapKeyRef:
                  name: example-config
                  key: LOG_LEVEL
            - name: DB_USERNAME
              valueFrom:
                secretKeyRef:
                  name: example-secret
                  key: DB_USERNAME
            - name: DB_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: example-secret
                  key: DB_PASSWORD
    ```

2. Apply the configuration:

    ```bash
    kubectl apply -f deployment.yaml
    ```

3. Verify that the Deployment is running and check the environment variables in one of the Pods:

    ```bash
    kubectl get pods
    POD_NAME=$(kubectl get pods -l app=myapp -o jsonpath="{.items[0].metadata.name}")
    kubectl exec -it $POD_NAME -- env
    ```

## Summary

- **ConfigMap**: Used to store non-sensitive configuration data as key-value pairs.
- **Secret**: Used to store sensitive information, such as passwords and tokens, in a base64-encoded format.
- **Using together**: Both ConfigMap and Secret can be used to configure applications and provide sensitive data securely.

By following these steps, you should be able to use ConfigMaps and Secrets in your Kubernetes cluster to manage configuration data and sensitive information. Feel free to modify the YAML files and explore further!

## Additional Resources

- [Kubernetes ConfigMaps Documentation](https://kubernetes.io/docs/concepts/configuration/configmap/)
- [Kubernetes Secrets Documentation](https://kubernetes.io/docs/concepts/configuration/secret/)
- [Minikube Documentation](https://minikube.sigs.k8s.io/docs/)

Happy Kubernetes-ing!
