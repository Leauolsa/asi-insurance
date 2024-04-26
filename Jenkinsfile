pipeline {
    agent any

    env {
        PATH = "/usr/bin:$PATH"
        tag = "1.0"
        dockerHubUser = "leauolsa"
        containerName = "insure-me"
        httpPort = "8081"
    }

    stages {
        stage('Code Clone') {
            steps {
                // Assuming git plugin is configured in Jenkins
                git branch: 'main', url: 'https://github.com/Leauolsa/asi-insurance.git'
            }
        }
        stage('Maven Build') {
            steps {
                sh 'mvn clean install -DskipTests'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${env.dockerHubUser}/insure-me:${env.tag} ."
            }
        }
        stage('Push Image to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerHubAccount', passwordVariable: 'dockerPassword', usernameVariable: 'dockerUser')]) {
                    sh "docker login -u $dockerUser -p $dockerPassword"
                    sh "docker push ${env.dockerHubUser}/${env.containerName}:${env.tag}"
                }
            }
        }
        stage('Docker Container Deployment') {
            steps {
                sh "docker rm -f ${env.containerName}"
                sh "docker pull ${env.dockerHubUser}/${env.containerName}:${env.tag}"
                sh """
                    docker run -d --rm -p ${env.httpPort}:${env.httpPort} --name ${env.containerName} ${env.dockerHubUser}/${env.containerName}:${env.tag}
                   """
                echo "Application started on port: ${env.httpPort} (http)"
            }
        }
    }
}
