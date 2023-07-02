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
   
     stage('update version') {
        steps {
          script{
           dir('eks') {
             def buildNumber = currentBuild.number
             echo "Build Number: ${buildNumber}"
             //sh(script: "sed -i 's/VERSION_TAG/\${buildNumber}/g' weatherapp.yaml")
             sh "sed -i 's/VERSION_TAG/${buildNumber}/g' weatherapp.yaml"
             //configFile = readFile('weatherapp.yaml')
             //updatedAppFile = configFile.replaceAll('VERSION_TAG', "${VERSION_TAG}")
             //writeFile(file: 'weatherapp.yaml', text: updatedAppFile)
             sh "cat weatherapp.yaml"
             
             
             }
         }
        }
     }
      stage('push changes') {
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
      stage('Disable Jenkins pipeline') {
        when {
            branch "pre-prod"
        }
        steps{
          script{
             def parentBuild = getParentBuild()
             parentBuild.pipeline.disableResume()
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
