pipeline {
    agent any
    tools {
        maven 'Maven_3_2_5'
    }
    stages {
        stage('Compile and Run Sonar Analysis') {
            steps {
                sh '''
                    mvn clean verify sonar:sonar \
                    -Dsonar.projectKey=gerrysereal \
                    -Dsonar.organization=gerrysereal \
                    -Dsonar.host.url=https://sonarcloud.io \
                    -Dsonar.token=b9ef54ebdfcd70665fcd4a9b4601996bb971ee07
                '''
            }
        }

        stage('Run SCA Analysis Using Snyk') {
            steps {
                withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
                    sh 'mvn snyk:test -fn'
                }
            }
        }

        stage('Build') {
            steps {
                withDockerRegistry([credentialsId: 'dockerlogin', url: 'https://your-docker-registry-url']) {
                    script {
                        app = docker.build('gerrysereal')
                    }
                }
            }
        }

        stage('Push') {
            steps {
                script {
                    docker.withRegistry('https://528757816359.dkr.ecr.us-west-2.amazonaws.com', 'ecr:us-west-2:aws-credentials') {
                        app.push('latest')
                    }
                }
            }
        }
    }
}