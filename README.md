# MERN-Application-Using-AKS
Deploy a MERN application using AKS

## Prerequisites:
1. Azure Subscription
2. Azure CLI
3. kubectl
4. Docker
5. Git

## Implementation

### 1. Code setup
1. Fork the repository https://github.com/UnpredictablePrashant/SampleMERNwithMicroservices to https://github.com/SyamalaKadmi/SampleMERNwithMicroservices.git
2. Clone the repository
    ```bash
     git clone https://github.com/SyamalaKadmi/SampleMERNwithMicroservices.git
    ```
---

### 2. Containerize the MERN Application
1. Create .env files for helloService & ProfileService in backend
    - helloService
        ```
            PORT=3001
        ```
    - profileService
        ```
            PORT=3002
            MONGO_URL="specifyYourMongoURLHereWithDatabaseNameInTheEnd"
        ```
2. Login to Azure
   ```
          az login
   ```
3. Create Azure Resource Group
   ```
          az group create --name skMernResourceGroups --location eastus
   ```
   ![Images/resourceGroups.png](Images/resourceGroups.png)
4. Create AKS Cluster
   ```
          az aks create --resource-group skMernResourceGroups --name skMernCluster --node-count 2 --generate-ssh-keys
   ```
   ![Images/aksCluster.png](Images/aksCluster.png)
5. Connect kubectl to the cluster
   ```
          az aks get-credentials --resource-group skMernResourceGroups --name skMernCluster
          kubectl get nodes
   ```
   ![Images/kubectl.png](Images/kubectl.png)
6. Create Azure Container Registry
   ```
         az acr create --resource-group skMernResourceGroups --name skmernacr --sku Basic
         az acr login --name skmernacr --expose-token
    ```
7. Create docker files for frontend and backend (helloService & profileService) respectively
    - helloservice [HelloServiceDockerfile](backend/helloService/Dockerfile)
    - profileservice [profileServiceDockerfile](backend/profileService/Dockerfile)
    - frontendservice [frontendDockerfile](frontend/Dockerfile)
8. Build & push the docker images to ACR
    ```bash
    cd ./SampleMERNwithMicroservices

    docker build -t skmernacr.azurecr.io/helloservice ./backend/helloService
    docker push skmernacr.azurecr.io/helloservice

    docker build -t skmernacr.azurecr.io/profileservice ./backend/profileService
    docker push skmernacr.azurecr.io/profileservice

    docker build -t skmernacr.azurecr.io/frontend ./frontend
    docker push skmernacr.azurecr.io/frontend
    ```
    ![Images/acr.png](Images/acr.png)
 ### 3. Deployment in AKS
 1. Create k8s deployment files in below structure
 k8s/
 - mongo-deployment.yaml
 - frontend-deployment.yaml
 - helloservice-deployment.yaml
 - profileservice-deployment.yaml
 - ingress.yaml

2. Apply kubernetes files
   ```
   kubectl apply -f k8s/
   ```
   ![Images/k8deployment.png](Images/k8deployment.png)
