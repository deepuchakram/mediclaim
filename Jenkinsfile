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
			//withSonarQubeEnv('sonar') {
				//shell '/opt/maven/bin/mvn clean verify sonar:sonar -Dmaven.test.skip=true'
				shell '/opt/maven/bin/mvn sonar:sonar \
  -Dsonar.projectKey=Mediclaim \
  -Dsonar.host.url=http://13.233.158.46:9000 \
  -Dsonar.login=claim1' 
			//}
		}
	}
	stage("Quality Gate") {
            steps {
		       
             timeout(time: 2, unit: 'MINUTES') {
               // waitForQualityGate abortPipeline: true
              }
            }
          }
	
	stage ('Deploy') {
		steps {
			shell '/opt/maven/bin/mvn clean deploy -Dmaven.test.skip=true'
		}
	}
	stage ('Release') {
		steps {
			shell 'export JENKINS_NODE_COOKIE=dontkillme ;nohup java -jar $WORKSPACE/target/*.jar &'
		}
	}
	/*stage ('DB Migration') {
		steps {
			shell '/opt/maven/bin/mvn clean flyway:migrate'
		}
	}*/
}
	post {
        always {
            emailext body: "${currentBuild.currentResult}: Project Name : ${env.JOB_NAME} Build ID : ${env.BUILD_NUMBER}\n\n Approval Link :  ${env.BUILD_URL}", recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']], subject: 'Test'
        }
    }

}
