pipeline {
    agent any

    environment {
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
                sh "docker build -t ${environment.dockerHubUser}/insure-me:${environment.tag} ."
            }
        }
        stage('Push Image to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerHubAccount', passwordVariable: 'dockerPassword', usernameVariable: 'dockerUser')]) {
                    sh "docker login -u $dockerUser -p $dockerPassword"
                    sh "docker push ${environment.dockerHubUser}/${environment.containerName}:${environment.tag}"
                }
            }
        }
        stage('Docker Container Deployment') {
            steps {
                sh "docker rm -f ${environment.containerName}"
                sh "docker pull ${environment.dockerHubUser}/${environment.containerName}:${environment.tag}"
                sh """
                    docker run -d --rm -p ${environment.httpPort}:${environment.httpPort} --name ${environment.containerName} ${environment.dockerHubUser}/${environment.containerName}:${environment.tag}
                   """
                echo "Application started on port: ${environment.httpPort} (http)"
            }
        }
    }
}
