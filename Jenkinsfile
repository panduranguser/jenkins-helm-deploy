pipeline {
    agent any
       stages {
        stage('build maven') {
          steps {
           sh 'pwd'
           sh 'mvn clean install package'
          }
       }
        stage('copy artifact') {
          steps {
            sh 'pwd'
            sh 'cp -r target/*.jar docker'
        }
      }
        stage('run test') {
          steps {
            sh 'mvn test'
          }
        }
        stage ('build and push image') {
           steps {
            script {
                def customImage = docker.build("paddy6078/petclinic:${env.BUILD_NUMBER}", "./docker")
                docker.withRegistry('https://registry.hub.docker.com', 'dockerhub'){
                    customImage.push()

                }
            }
          }
        }

        stage ('deploye on kubernetes') {
           steps {
            withKubeConfig([credentialsId: 'kubeconfig']) {
                sh 'pwd'
                sh 'cp -R helm/* .'
                sh 'ls -ltrh'
                sh 'pwd'
                sh '/usr/local/bin/helm upgrade --install petclinic-app petclinic --set image.repository=paddy6078/petclinic --set image.tag=${BUILD_NUMBER}'
            }
        }
       }

    }
}


pipeline {
  agent any	
    stages {      
        stage('Build maven ') {
            steps { 
              sh 'pwd'      
              sh 'mvn  clean install package'
            }
        }
        
        stage('Copy Artifact') {
           steps { 
             sh 'pwd'
		     sh 'cp -r target/*.jar docker'
           }
        }
	 stage('Run Tests') {
            steps {
              sh 'mvn test'
        }
      }    
         
        stage('Build docker image') {
           steps {
               script {         
                 def customImage = docker.build("ashaik65/petclinic:${env.BUILD_NUMBER}", "./docker")
                 docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                 customImage.push()
                 }                     
           }
        }
	  }

      stage('Build on k8 ') {
            steps {
	      withKubeConfig([credentialsId: 'kubeconfig']) {
              sh 'pwd'
              sh 'cp -R helm/* .'
		      sh 'ls -ltr'
              sh 'pwd'
              sh '/usr/local/bin/helm upgrade --install petclinic-app petclinic  --set image.repository=ashaik65/petclinic --set image.tag=${BUILD_NUMBER}'
              			
            }           
        }
      }
    }	    
}
