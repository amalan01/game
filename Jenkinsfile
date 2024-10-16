pipeline {
    agent none
    
    stages {
        stage('CLONE GIT REPOSITORY') {
            agent {
                label 'ubuntu-Appserver-3120'
            }
            steps {
                checkout scm
            }
        }  

        stage('SCA-SAST-SNYK-TEST') {
            agent any
            steps {
                script {
                    snykSecurity(
                        snykInstallation: 'Snyk',
                        snykTokenId: 'Synkid',
                        severity: 'critical'
                    )
                }
            }
        }


        stage('BUILD-AND-TAG') {
            agent {
                label 'ubuntu-Appserver-3120'
            }
            steps {
                script {
                    def app = docker.build("amalan06/snake_game_2024")
                    app.tag("latest")
                }
            }
        }

        stage('POST-TO-DOCKERHUB') {    
            agent {
                label 'ubuntu-Appserver-3120'
            }
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub_credentials') {
                        def app = docker.image("amalan06/snake_game_2024")
                        app.push("latest")
                    }
                }
            }
        }

        stage('DEPLOYMENT') {    
            agent {
                label 'ubuntu-Appserver-3120'
            }
            steps {
                sh "docker-compose down"
                sh "docker-compose up -d"   
            }
        }
    }
}