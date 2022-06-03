pipeline {
    agent any
    stages {
        
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        
        stage('Build Docker Image') {
            when {
                branch 'master'    
            }
            steps {
                script {
                    app = docker.build("andrepaivaguimaraes/trainschedule")
                    app.inside {
                        sh 'echo $(curl localhost:8080)'    
                    }
                }
            }
        }
        
        stage('Push Docker Image') {
            when {
                branch 'master'    
            }
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com','dockerhub_id') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("lastest")
                    }
                }
            }
        }
        
        stage('Deploy To Production') {
            when {
                branch 'master'
            }
            steps {
                input 'Deploy to Production?'
                milestone(1)
                withCredentials([usernamePassword(credentialsId: 'webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
                    script {
                        sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$prod_ip \"docker pull andrepaivaguimaraes/trainschedule:${env.BUILD_NUMBER}\""
                        try {
                            sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$prod_ip \"docker stop trainschedule\""
                            sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$prod_ip \"docker rm trainschedule\""
                        } catch (err) {
                            echo: 'caught error: $err'
                        }
                        sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$prod_ip \"docker run --restart always --name train-schedule -p 8080:8080 -d andrepaivaguimaraes/trainschedule:${env.BUILD_NUMBER}\""
                    }
                }
            }
        }
    }
}
