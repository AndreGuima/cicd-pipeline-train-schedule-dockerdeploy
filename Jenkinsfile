pipeline {
    agent any
    stages {
        stage('Initialize'){
            RUN curl -fsSLO https://get.docker.com/builds/Linux/x86_64/docker-20.10.9.tgz \
              && tar xzvf docker-20.10.9.tgz \
              && mv docker/docker /usr/local/bin \
              && rm -r docker docker-20.10.9.tgz
        }    
        
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
                    app = docker.build("andrepaivaguimaraes/trainSchedule")
                    app.inside {
                        sh 'echo $(curl localhost:8080)'    
                    }
                }
            }
        }
    }
}
