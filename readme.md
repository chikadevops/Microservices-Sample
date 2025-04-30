I created a new GitHub repository name 'Microservice-sample' and created several directories with four backend directories and one frontend directory.

Then I created a '.github/workflows' directory in my repository for my GitHub actions file. That was after saving my secrets.

Next thing I did was create the workflow that built the image and sent it to my DockerHub repository.

Below is a copy of my CI workflow

```name: Build and Push
on:
  push:
    branches:
      - main

jobs:
    build:
        runs-on: ubuntu-latest
        
        steps:
            - name: Checkout code
              uses: actions/checkout@v3

            - name: Login to DockerHub
              run: |
                echo "${{ secrets.DOCKERHUB_PASSWORD }}" | docker login -u "${{ secrets.DOCKERHUB_USERNAME }}" --password-stdin

            - name: Build Docker Images
              run: |
                docker build -t chikadev/auth-api ./auth-api
                docker build -t chikadev/todos-api ./todos-api
                docker build -t chikadev/users-api ./users-api
                docker build -t chikadev/log-message-processor ./log-message-processor
                docker build -t chikadev/frontend ./frontend

            - name: Push Docker Images
              run: |
                docker push chikadev/auth-api
                docker push chikadev/todos-api
                docker push chikadev/users-api
                docker push chikadev/log-message-processor
                docker push chikadev/frontend
```

Next, was to deploy to cloud. So I worte a CD workflow that ssh into my aws, pull the images and ran a 'docker-compse up' command so the microservices and run successfully.

Below is a copy of my CI file

```
name: Deploy
on:
  workflow_run:
    workflows: ["Build and Push"]
    types:
      - completed

jobs:
    deploy:
        if: ${{ github.event.workflow_run.conclusion == 'success' }}
        runs-on: ubuntu-latest

        env:
            EC2_HOST: 54.205.245.221
            EC2_USER: ubuntu

        steps:
        - name: Install SSH Client
          run: sudo apt-get install -y openssh-client

        - name: Setup SSH Key
          run: |
            mkdir -p ~/.ssh
            echo "${{ secrets.MY_EC2_KEYS }}" > ~/.ssh/id_rsa
            chmod 600 ~/.ssh/id_rsa
    

        - name: Add EC2 Host to Known Hosts
          run: |
            ssh-keyscan -H "$EC2_HOST" >> ~/.ssh/known_hosts


        - name: Pull Docker Images
          run: |
            ssh $EC2_USER@$EC2_HOST << 'EOF'
                echo "${{ secrets.DOCKERHUB_PASSWORD }}" | docker login -u "${{ secrets.DOCKERHUB_USERNAME }}" --password-stdin
                
                # Download docker-compose.yml from GitHub
                curl -fsSL -o docker-compose.yml https://raw.githubusercontent.com/chikadevops/Microservices-Sample/main/docker-compose.yml

                # Pull the latest Docker images
                docker pull chikadev/auth-api:latest
                docker pull chikadev/todos-api:latest
                docker pull chikadev/users-api:latest
                docker pull chikadev/log-message-processor:latest
                docker pull chikadev/frontend:latest
            
                # Run Containers
                docker-compose up -d --remove-orphans
                
            EOF
```

It is worthy of note that I implemented caching to optimize build times, secured all sensitive data in our GitHub secret. And configured  my workflows to deploy updates authomatically.

#Thanks