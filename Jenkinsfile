@NonCPS
def getParentBuild() {
  return currentBuild.rawBuild.getParent()
}

pipeline {
    agent {
      label 'build-agent' 
   }
   options {
      //disableResume()
   }
      stage('build & tests') {
         when {
            branch "development"
         }
         steps {
             sh 'docker build -t "mytestbuild" .'
             sh 'docker run -dit -p 5000:5000 --name weather-app "mytestbuild"'
             sh 'docker exec -dit weather-app bash python3 testApp.py'
             //sh 'python3 testSelenium.py'
             //sh "docker tag \"${ECR_URI}/${REPO_NAME}\" \"${ECR_URI}/${REPO_NAME}:${VERSION_TAG}\""

            
          
         }
      }
      
     stage('update version') {
        when {
            branch "main"
        }
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
        when {
            branch "main"
        }
        steps {
          script{
             withCredentials([gitUsernamePassword(credentialsId: 'github-token', gitToolName: 'Default')]) {
                   //currentBuild.rawBuild.pipeline.disableResume()
                   //sh 'git stash'
                   //sh 'git checkout development'
                   //sh 'git stash pop'
                   sh 'git add .'
                   sh 'git commit -m "Commit message from jenkins"'
                   sh 'git push origin'
                   
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
             //parentBuild.pipeline.disableResume()
           }
         }
     }
      stage('deploy to prod repo') {
          when {
            branch "development"
          }
          steps {
             withCredentials([gitUsernamePassword(credentialsId: 'github-token', gitToolName: 'Default')]) {
                sh 'git remote add prod-repo https://github.com/zoharperetz/prod.git'
                sh 'git stash'
                sh 'git checkout --track origin/development'
                sh 'git stash pop'
                sh 'git add eks/'
                sh 'git commit -m "Commit message from jenkins"'
                sh 'git push prod-repo development:main --force'
                
             }
         }
      }
       
   
   post {        
      
        always {
            // Clean workspace here
            cleanWs()
            sh(script: 'docker rm -vf $(docker ps -a -q)')
            sh"""docker system prune --force
            """
        }
        
    }
    
}
