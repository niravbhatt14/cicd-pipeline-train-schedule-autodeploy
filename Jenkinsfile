pipeline {
    agent any
    environment {
        //be sure to replace "bhavukm" with your own Docker Hub username
        DOCKER_IMAGE_NAME = "niravbhatt14/train-schedule:1"
    }
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('Build Docker Image') {
            //when {
              //  branch 'master'
            //}
            steps {
                script {
                    app = docker.build(DOCKER_IMAGE_NAME)
                    app.inside {
                        sh 'echo Hello, World!'
                    }
                }
            }
        }
        stage('Push Docker Image') {
            //when {
              //  branch 'master'
            //}
            steps {
                script {
                   // docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login')
                    withDockerRegistry(credentialsId: 'DOCKER_HUB_LOGIN', url: 'https://index.docker.io/v1/'){
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
        stage('Deploy-App-QA') {
  	         steps {
              sh 'ansible-playbook --inventory /tmp/inv $WORKSPACE/deploy/train-schedule-kube-canary.yml --extra-vars "env=qa build=$BUILD_NUMBER"'
	   }
	   
            post { 
              always { 
                cleanWs() 
	      }
	   }
	}
        
    }
}
