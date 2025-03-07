pipeline {

agent any

environment {

DOCKER_IMAGE = "app-image"

DOCKER_TAG = "latest"

DOCKER_REPO = "ankitamohanty1509/app-image"

DOCKER_CREDENTIALS_ID =""

CONTAINER_NAME = "local-container"

CONTAINER_NAME1 = "server-container"

}

stages {

stage('Clone Repository') {

steps {

git 'https://github.com/default-user/repo.git'

}

}

stage('Docker Login') {

steps {

script {

docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS_ID) {

echo "Logged into Docker Hub"

}

}

}

}

stage('Build Docker Image') {

steps {

script {

sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."

}

}

}

stage('Run Container Locally') {

steps {

script {

sh """

docker ps -a -q --filter name=${CONTAINER_NAME} | xargs -r docker stop || true

docker ps -a -q --filter name=${CONTAINER_NAME} | xargs -r docker rm || true

docker run -d -p 8093:80 --name ${CONTAINER_NAME} ${DOCKER_IMAGE}:${DOCKER_TAG}

"""

}

}

}

stage('Push to Docker Hub') {

steps {

script {

docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS_ID) {

sh "docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_REPO}:${DOCKER_TAG}"

sh "docker push ${DOCKER_REPO}:${DOCKER_TAG}"

}

}

}

}

stage('Deploy to Server') {

steps {

script {

sh """

sshpass -p "password" ssh -o StrictHostKeyChecking=no user@server-ip '

docker pull ${DOCKER_REPO}:${DOCKER_TAG} &&

docker ps -a -q --filter name=${CONTAINER_NAME1} | xargs -r docker stop || true &&

docker ps -a -q --filter name=${CONTAINER_NAME1} | xargs -r docker rm || true &&

docker run -d -p 80:80 --name ${CONTAINER_NAME1} ${DOCKER_REPO}:${DOCKER_TAG}'

"""

}

}

}

}

}---
