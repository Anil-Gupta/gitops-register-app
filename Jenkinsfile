pipeline {
    agent { label "jenkins-slave" }
    environment {
              APP_NAME = "register-app-pipeline"
    }

    stages {
        stage("Cleanup Workspace") {
            steps {
                echo 'Starting workspace...'
                cleanWs()
                echo 'End workspace...'
            }
        }

        stage("Checkout from SCM") {
               steps {
                   echo 'Checkout from SCM...'
                   git branch: 'main1', credentialsId: 'github', url: 'https://github.com/Anil-Gupta/gitops-register-app'
                   echo 'End from SCM...'
               }
        }

        stage("Update the Deployment Tags") {
            steps {
                echo 'Started Updating Deployment Tags...'
                sh """
                   cat deployment.yaml
                   sed -i 's/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g' deployment.yaml
                   cat deployment.yaml
                """
                echo 'Ending Updating Deployment Tags...'
            }
        }

        stage("Push the changed deployment file to Git") {
            steps {
                echo 'Starting pushing change deployment file to git...'
                sh """
                   git config --global user.name "Anil-Gupta"
                   git config --global user.email "anilgupta.bcs@gmail.com"
                   git add deployment.yaml
                   git commit -m "Updated Deployment Manifest"
                """
                withCredentials([gitUsernamePassword(credentialsId: 'github', gitToolName: 'Default')]) {
                  sh "git push https://github.com/Anil-Gupta/gitops-register-app main"
                echo 'Ending pushing change deployment file to git...'
                }
            }
        }
      
    }
    post {
       failure {
           echo 'failure email'
             emailext body: '''${SCRIPT, template="groovy-html.template"}''', 
                      subject: "${env.JOB_NAME} - Build # ${env.BUILD_NUMBER} - Failed", 
                      mimeType: 'text/html',to: "devops.anilgupta@gmail.com"
      }
      success {
          echo 'success email'
            emailext body: '''${SCRIPT, template="groovy-html.template"}''', 
                     subject: "${env.JOB_NAME} - Build # ${env.BUILD_NUMBER} - Successful", 
                     mimeType: 'text/html',to: "devops.anilgupta@gmail.com"
      }      
   }
}
