@NonCPS
def getParentBuild() {
  return currentBuild.rawBuild.getParent()
}

pipeline {
    agent {
      label 'build-agent' 
   }
   options {
      disableResume()
   }
   stages{
     
     stage('Disable pipeline from Jenkins trigger') {
        steps{
          script{
             def jenkinsCause = currentBuild.rawBuild.getCause(hudson.model.Cause$UserIdCause)
             echo "${jenkinsCause}"
             //def username = currentBuild.getExecutor(hudson.model.Cause$UserIdCause)
             def executor = env.NODE_NAME
             echo "Executor: ${executor}"
             if (jenkinsCause != null) {
                   echo 'Changes detected from Jenkins. Aborting pipeline run.'
                   error ('Pipeline run aborted due to changes from Jenkins.')
           }
         }
     }
     }
     stage('Update version') {
        steps {
          script{
           dir('eks') {
             def buildNumber = currentBuild.number
             echo "Build Number: ${buildNumber}"
             sh "sed -i 's/VERSION_TAG/${buildNumber}/g' weatherapp.yaml"
             sh "cat weatherapp.yaml"
             
             
             }
         }
        }
     }
      stage('Push changes') {
        steps {
          script{
             withCredentials([gitUsernamePassword(credentialsId: 'github-token', gitToolName: 'Default')]) {
                   //currentBuild.rawBuild.pipeline.disableResume()
                   //sh 'git stash'
                   //sh 'git checkout development'
                   //sh 'git stash pop'
                   sh 'git add .'
                   sh 'git commit -m "Commit message from jenkins"'
                   sh 'git push origin HEAD:main'
                   
                   //def parentBuild = currentBuild.rawBuild.getParent()
                   //sh 'git checkout pre-prod'
                   //sh 'git merge development'
                   //sh 'git tag "${VERSION_TAG}"'
                   //sh 'git push --tags' 
                   //sh 'git push origin pre-prod'
                   
            }
           }
        }
      }
      
       
   }
   post {        
      
        always {
            // Clean workspace here
            cleanWs()
            
        }
        
    }
    
}
