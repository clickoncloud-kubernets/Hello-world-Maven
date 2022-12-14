pipeline{
    agent any
    tools {
      maven 'maven3'
    }
    environment {
      DOCKER_TAG = getVersion()
    }
    stages{
        stage('SCM'){
            steps{
                git credentialsId: 'github', 
                    url: 'https://github.com/clickoncloud-kubernets/Hello-world-Maven'
            }
        }
        
        stage('Maven Build'){
            steps{
                sh "mvn clean package"
            }
        }
        
        stage('Docker Build'){
            steps{
                sh "docker build . -t clickoncloudkubernets/hello-world-maven:${DOCKER_TAG} "
            }
        }
        
        stage('DockerHub Push'){
            steps{
                withCredentials([string(credentialsId: 'docker-hub', variable: 'dockerHubPwd')]) {
                    sh "docker login -u soumenbiswas212 -p ${dockerHubPwd}"
                }
                
                sh "docker push clickoncloudkubernets/hello-world-maven:${DOCKER_TAG} "
            }
        }
		
        stage('Connect Kubernets'){
            steps{
                sh "cp -i kubernets/config $HOME/.kube/config"
		sh "chown jenkins:jenkins $HOME/.kube/config"
		sh "kubectl get nodes"    
            }
        }		

	    
        // stage('maven yaml name change') {
        //     steps {
        //         sh "chmod +x script.sh"
        //         sh "./script.sh ${DOCKER_TAG}"
        //     }
        // }	    
	    
        stage('Deploy Kubernets'){
            steps{ 
                sh "kubectl apply -f kubernets/hello-world-app.yaml"  
            }
        }	    
		
		
		
    }     

}

def getVersion(){
    def commitHash = sh label: '', returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
}
