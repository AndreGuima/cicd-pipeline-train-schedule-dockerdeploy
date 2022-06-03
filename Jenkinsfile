pipeline {
    agent any
    stages {
        stage('Initialize'){
            ENV DOCKERVERSION=18.03.1-ce
            RUN curl -fsSLO https://download.docker.com/linux/static/stable/x86_64/docker-${DOCKERVERSION}.tgz \
            && tar xzvf docker-${DOCKERVERSION}.tgz --strip 1 \
            -C /usr/local/bin docker/docker \
            && rm docker-${DOCKERVERSION}.tgz
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
