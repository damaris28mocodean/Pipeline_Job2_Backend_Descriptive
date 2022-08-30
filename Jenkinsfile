pipeline{
  
  agent {

      label 'master'
  }
  
  environment {

		    DOCKERHUB_CREDENTIALS=credentials('access-token-docker')
	}

  stages{
    
     stage ('Setup parameters'){
       
        steps{
          
          script{
            
            properties([
                      parameters([  
                        string(defaultValue: 'damy28/backend_repo', name: 'IMAGE_NAME', description: ''),
                        string(defaultValue: 'backend_cont', name: 'CONTAINER_NAME', description: ''),
                        [$class: 'ChoiceParameter', 
                                                choiceType: 'PT_SINGLE_SELECT', 
                                                description: '', 
                                                filterLength: 1, 
                                                filterable: false, 
                                                name: 'ARTIFACT', 
                                                script: [
                                                    $class: 'GroovyScript', 
                                                    fallbackScript: [
                                                        classpath: [], 
                                                        sandbox: false, 
                                                        script: ""
                                                    ], 
                                                    script: [
                                                        classpath: [], 
                                                        sandbox: false, 
                                                        script: ''' import groovy.io.FileType
                                                                 def list = []
                                                                 def dir = new File("/var/lib/jenkins/workspace/Pipeline_Job2_Backend_Declarative/Artifacts")
                                                                 dir.eachFileRecurse (FileType.FILES) {
                                                                   if(it.getName().equals("Dockerfile") == false){
                                                                     list.add(it.getName())
                                                                   }
                                                                 }
                                                                return list '''
                                                    ]
                                                ]
                                            ],
                        choice(choices: ['8083', '8084'], name: 'PORT')
                      ])
                  ])
            
            
          }
          
        }
       
     }
    
     /*stage('Preparation'){
        
      steps{
        
          sh '''docker stop ${CONTAINER_NAME}
          docker rm ${CONTAINER_NAME}
          '''
          //sh "a=1 && val=`expr $BUILD_NUMBER - $a` && docker image rm ${IMAGE_NAME}:${val}"
        
      }
      
    }*/
    

    stage('Build'){
      
      steps{
        
        dir('Artifacts'){
            
            sh "docker build --build-arg ARTIFACT_NAME=${ARTIFACT} --build-arg PORT_NUMBER=8080 --tag ${IMAGE_NAME}:${BUILD_NUMBER} ."
          
        }
        
      }
      
    }

    stage('Login to DockerHub') {

        steps {
          sh "echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin"
        }

		}

    stage('Push') {

        steps {
          sh "docker push ${IMAGE_NAME}:${BUILD_NUMBER}"
        }
		}
    
    stage('Deploy'){
      steps{
        
          sh "ssh damy2@192.168.152.131 'docker run --name ${CONTAINER_NAME} -d -p ${PORT}:8080 ${IMAGE_NAME}:${BUILD_NUMBER}'"
        
      }
      
    }
    
  }

  post {

      always {

          sh "docker logout"

      }

	}
  
}
