### `Setting up the Project`
Here we set up the project, dockerize it and run it locally to make sure everything works as expected.

### `Basic Node.js project`
Create a basic node project by npm init

### `Containerizing the project`
To containerize the project, we would make use of Docker. First, we create a Dockerfile for the application.

In the root folder, create a file named Dockerfile and add the following content within it:
please reeference Dockerfile present in the repo.

### `Create a Github Repo and Add Secrets`
Goto repo settings > secret and variables > actions > new repository secret
Add AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY

### `Push your code to the Repo`
git init
git remote add origin https://your-git-repo.git
git add -A
git commit -m "FC"
git push -u -f origin master

### `Login to the AWS Console and Create an ECR repository`
Search ECR
Click on create repository and under general settings tab give repo name and click on create with default settings

### `Push and Build code`
Refer to the push_and_build.yml file in .gituhb/workflows directory present in the repo.
it will automatically trigger when any chanage detected in the master branch and push the image to the AWS ECR.

### `Provisioning an EKS Cluster`
We make use of the eksctl CLI to create the cluster in EKS, eksctl which is a simple command-line tool for creating and managing Kubernetes clusters on Amazon EKS.

eksctl version

eksctl create cluster --name node-app-cluster --region ap-south-1

aws eks update-kubeconfig --name node-app-cluster --region ap-south-1

### `Deploying to EKS Cluster`
To deploy the application, create a Deployment.yml file containing the deployment details. The deployment creates a Pod, where the application runs in a single container using the Docker image.

----------------------------------------------------------------------------------------------------------------------------------------------
apiVersion: apps/v1
kind: Deployment
metadata:
  name: node-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: node-app
  template:
    metadata:
      labels:
        app: node-app
    spec:
      containers:
      - name: node-app
        image: 058264231631.dkr.ecr.ap-south-1.amazonaws.com/deploy_to_eks:latest
        ports:
          - containerPort: 80

----------------------------------------------------------------------------------------------------------------------------------------------
Replace the image “058264231631.dkr.ecr.ap-south-1.amazonaws.com/deploy_to_eks:latest” with your ECR URI

Next, apply the deployment to create it:

kubectl apply -f Deployment.yml

To verify if it works, run the command below:

kubectl get deployments

kubectl expose deployment node-app --port=80 --target-port=80 --type=LoadBalancer

To verify if it works, run the command below:

kubectl get services

### `Testing`
Finally, to confirm everything works as expected, copy the exposed EXTERNAL-IP address shown in the previous command and visit a browser.
