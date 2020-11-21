pipeline {
   agent any
    stages {
      stage('Git Checkout') {
         steps {
            git 'https://github.com/deepuchakram/mediclaim.git'
        }
    }
    stage('Build') {
        steps {
            withSonarQubeEnv('sonar') {
                sh '/opt/maven/bin/mvn clean verify sonar:sonar -Dmaven.test.skip=true'
            }
        }
    }
       stage('Build Approval')
        {
        steps{
         slackSend baseUrl: 'https://hooks.slack.com/services/', channel: 'pipeline', color: 'bad', message: "${env.BUILD_URL}", tokenCredentialId: 'slackpipe', username: 'admin'
         script{
           def userInput
           try {
             userInput = input(id: 'userInput', message: 'Build Approval', 
			 submitterParameter: 'submitter',
			 submitter:'buildapprover', 
			 parameters: [[$class: 'BooleanParameterDefinition', defaultValue: true, description: 'Build Approval', name: 'env']])
             } catch(err) { 
             def user = err.getCauses()[0].getUser()
             userInput = false
             echo "Aborted by: [${user}]"
            }
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
