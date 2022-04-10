pipeline{
    agent any
    tools {
        maven 'maven3'
    }
    environment {
        DOCKER_TAG = getVersion()
    }
    stages{
        stage('SCM CHECKOUT'){
            steps{
            git branch: 'main', 
                url: 'https://github.com/dileep208/dileepdockeransible.git'
            }
        }
        stage('MAVEN BUILD'){
            steps{
            sh 'mvn clean package'
            }
        }
        stage('DOCKER BUILD'){
            steps{
                sh "docker build . -t dileep208/dileepapp:${DOCKER_TAG}"

            }
        }
        stage('DOCKER PUSH'){
            steps{
                withCredentials([string(credentialsId: 'dockerHubPwd', variable: 'dockerHubPwd')]) {
                    sh "dokcer login -u dileep208 -p ${dockerHubPwd}"
            }
            sh "docker push dileep208/dileepapp:${DOCKER_TAG}"
                
            }
        }
        stage('DOCKER DEPLOY'){
            steps{
                ansiblePlaybook credentialsId: 'devserver', disableHostKeyChecking: true, extras: "-e DOCKER_TAG=${DOCKER_TAG}", installation: 'ansible', inventory: 'dev.inv', playbook: 'ansibledocker.yaml'
            }
        }
    }
def getVersion(){
    def commitHash = sh label: '', returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
}