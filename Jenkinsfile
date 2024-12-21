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
                withDockerRegistry([credentialsId: 'dockerlogin', url: 'https://index.docker.io/v1/']) { // Perbaiki URL ini
                    script {
                        app = docker.build('gerrysereal')
                    }
                }
            }
        }

        stage('Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerlogin', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    script {
                        // Login ke Docker Hub
                        sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin'
                        
                        // Push ke Docker Hub
                        app.push('latest') // Pastikan `app` sudah terdefinisi
                    }
                }
            }
        }
    }
}