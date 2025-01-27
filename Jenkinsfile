pipeline{
    agent {label 'webserver'}
    tools {
      maven 'maven'
       }
    environment {
      DOCKER_TAG = getVersion()
    }
    stages{
        stage('SCM'){
            steps{
                git credentialsId: 'github', 
                    url: 'https://github.com/javahometech/dockeransiblejenkins'
            }
        }  
        stage('Maven Build'){
            steps{
                sh "mvn clean package"
            }
        }
        
        stage('Docker Build'){
            steps{
                sh "docker build . -t anilkumblepuli/hariapp:${DOCKER_TAG} "
            }
        }
        stage('DockerHub Push'){
            steps { 
                 withCredentials([string(credentialsId: 'dockerHUB', variable: 'docker-HUB')]) 
                     {           
                       sh "docker login -u anilkumblepuli -p ${docker-HUB}"
                       sh "docker push anilkumblepuli/hariapp:${DOCKER_TAG}"
                    }
               }
          }
        stage('Docker Deploy'){
            steps{
            ansiblePlaybook credentialsId: 'private-key', disableHostKeyChecking: true, extras: 'DOCKER_TAG', inventory: 'dev.inv', playbook: 'deploy-docker.yml' 
            }
        }
}
}
def getVersion(){
    def commitHash = sh label: '', returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
    }





