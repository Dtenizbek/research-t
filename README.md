# research-t
Here are a couple of options on the infrastructure level
 Using AWS Secrets Manager with EKS
1. Store a Secret in AWS Secrets Manager:
```
### Store a secret in AWS Secrets Manager
aws secretsmanager create-secret --name my-app-db-secret \
    --secret-string '{"username":"db_user","password":"super_secure_password"}'
```
2. Grant EKS Worker Nodes IAM Role Access:
Ensure that the worker nodes in your EKS cluster have the necessary IAM role permissions to access secrets from AWS Secrets Manager.

3. Modify Kubernetes Deployment Configuration:
Adjust the Kubernetes Deployment configuration to fetch the secret from AWS Secrets Manager at runtime.

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app-container
        image: my-app-image:latest
        envFrom:
        - secretRef:
            name: my-app-db-secret
```
4. Deploy the Updated Configuration:
```
kubectl apply -f my-app-deployment.yaml
```
now, the application running in your EKS cluster fetches the database username and password from AWS Secrets Manager at runtime.


### Using AWS Systems Manager Parameter Store with EKS
 #Store a parameter in AWS Systems Manager Parameter Store
```
aws ssm put-parameter --name /my-app/db-password \
    --value super_secure_password \
    --type SecureString
```
2. Grant EKS Worker Nodes IAM Role Access:
Ensure that the worker nodes in your EKS cluster have the necessary IAM role permissions to access parameters from AWS Systems Manager Parameter Store.

3. Modify Kubernetes Deployment Configuration:
Adjust the Kubernetes Deployment configuration to fetch the parameter from AWS Systems Manager Parameter Store at runtime.
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app-container
        image: my-app-image:latest
        envFrom:
        - secretRef:
            name: my-app-db-parameter
            key: value
```
4.Deploy the Updated Configuration:
```
kubectl apply -f my-app-deployment.yaml
```

### Using HashiCorp Vault with EKS
1. Deploy Vault on EKS:
HashiCorp provides Helm charts for deploying Vault on Kubernetes. Install Helm and deploy Vault:
```
helm repo add hashicorp https://helm.releases.hashicorp.com
helm install vault hashicorp/vault --set "server.dev=false"
```
2. Initialize and Unseal Vault:
Follow the Vault documentation to initialize and unseal Vault. This typically involves running commands like vault operator init and vault operator unseal.

3. Store Secrets in Vault:
```
# Store a database password in Vault
vault kv put secret/my-app/db-credentials username=db_user password=super_secure_password
```
4. Integrate Vault with Kubernetes:
Vault provides a Kubernetes auth method. Configure Vault to authenticate with Kubernetes and create a Vault policy and role for your application.

5. Modify Kubernetes Deployment Configuration:
Adjust the Kubernetes Deployment configuration to fetch the secret from Vault at runtime.
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app-container
        image: my-app-image:latest
        envFrom:
        - secretRef:
            name: my-app-db-secret
```
6. Deploy the Updated Configuration:
```
kubectl apply -f my-app-deployment.yaml
```
Now, your application running in EKS fetches the database credentials from HashiCorp Vault at runtime.

