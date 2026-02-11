pipeline {
  agent any
  parameters {
    booleanParam(name:'executeDeployment', defaultValue:true, description:'Should we execute the deployment step?')
  }
  stages {
    stage('Clear Workspace') {
        steps {
            script {
              deleteDir()
            }
        }
    }
    stage('Pulling project') {
        steps {
            script {
                sh '''
                    git clone https://github.com/TrishantMaharjan/jenkins-go-test.git
                '''
            }
        }
    }
    stage("Build") {
      steps {
        script {
          echo "Building Go backend with image tag ${env.BUILD_NUMBER}"
          sh """
            docker build ./jenkins-go-test/backend --no-cache -t go-backend:${env.BUILD_NUMBER} -f ./jenkins-go-test/backend/Dockerfile
          """
        }
      }
    }
    stage("Deploying Application") {
      when {
        expression {
          params.executeDeployment
        }
      }
      steps {
        echo 'Deploying the application...'
        sh 'docker compose -f /home/application/docker-compose.yaml down -v'
        sh 'docker compose -f ./jenkins-go-test/docker-compose.yaml down -v'
        
        script {
          sh """
            sed -i 's/\\(go-backend:\\)[^ ]*/\\1${env.BUILD_NUMBER}/' ./jenkins-go-test/docker-compose.yaml
          """
        }
      }
    }
    stage("Copy required files") {
      when {
        expression {
          params.executeDeployment
        }
      }
      steps {
        echo "Checking docker-compose file"
        sh 'cat ./jenkins-go-test/docker-compose.yaml'
        
        sh 'docker compose -f ./jenkins-go-test/docker-compose.yaml up -d'
      }
    }
  }
  post {
    always {
      script {
        deleteDir()
        try {
          sh 'docker rmi $(docker images -q)'
        } catch (Exception e) {
          echo 'Deleted previous image'
        }
      }
    }
  }
}

