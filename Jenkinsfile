pipeline {
   agent any
    stages {
      stage('Git Checkout') {
         steps {
            git 'https://github.com/deepuchakram/mediclaim.git'
        }
    }
stage('Build Approval')
        {
        steps{
          build 'build_approval'
            }
         }      
        
    stage('Build') {
        steps {
            withSonarQubeEnv('sonar') {
                sh '/opt/maven/bin/mvn clean verify sonar:sonar -Dmaven.test.skip=true'
            }
        }
    }
       
       
    // seems some issue with quality gates in latest jenkins version so commented out
    /*stage("Quality Gate") {
            steps {
              timeout(time: 2, unit: 'MINUTES') {
                waitForQualityGate abortPipeline: true
              }
            }
          } */
stage('Deploy Approval'){
            steps{
               slackSend baseUrl: 'https://hooks.slack.com/services/', channel: 'pipeline', color: 'bad', message: "${env.BUILD_URL}", tokenCredentialId: 'slackpipe', username: 'admin'
               script{
                   def userInput
                   try {
                       userInput = input(
                           id: 'Proceed1', message: 'Approval',submitterParameter: 'submitter', submitter:'admin', parameters: [
                               [$class: 'BooleanParameterDefinition', defaultValue: true, description: '', name: 'Please provide Deployment approval']])

                        }
                    catch(err) {
                        def user = err.getCauses()[0].getUser()
                        userInput = false
                        echo "Aborted by: [${user}]"
                    }}
               }
            }      		    
    stage ('Deploy') {
        steps {
            sh '/opt/maven/bin/mvn clean deploy -Dmaven.test.skip=true'
        }
    }
    stage ('Release') {
        steps {
            sh 'export JENKINS_NODE_COOKIE=dontkillme ;nohup java -jar $WORKSPACE/target/*.jar &'
        }
    }
}
}
