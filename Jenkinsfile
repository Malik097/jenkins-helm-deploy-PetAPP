pipeline {
  agent any
    stages {
      stage('Build maven'){
        steps {
          sh 'pwd'
          sh 'mvn clean install package'
        }
      }
      stage('Copy Artifact'){
        steps {
          sh 'pwd'
          sh 'cp -r target/*.jar docker'
        }
      }
      stage('Run Tests'){
        steps {
          sh 'mvn test'
        }
      }
      stage('Build docker image'){
        steps{
          script {
            def customImage = docker.build("malik097/veterinaryapp:${env.BUILD_NUMBER}", "./docker")
            docker.withRegistry('https://registry.hub.docker.com', 'dockerhub'){
            customImage.push()  
            }
          }

        }

      }
      stage('Build on kubernetes'){
        steps {
          withKubeConfig([credentialsId: 'kubeconfig']){
            sh 'pwd'
            sh 'cp -R helm/* .'
            sh 'ls -ltrh'
            sh 'pwd'
            sh '/usr/local/bin/helm upgrade --install veterinaryapp veterinaryapp  --set image.repository=malik097/veterinaryapp --set image.tag=${BUILD_NUMBER}'
          }
        }

      }
    }  
}

