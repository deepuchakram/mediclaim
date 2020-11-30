pipeline {
   agent any
    stages {
      stage('Source Checkout') {
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
                sh '/opt/maven/bin/mvn clean verify sonar:sonar -Dmaven.test.skip=false'
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
       stage('Deployment Approval')
        {
        steps{
          build 'deploy_approval'
            }
         }      

    stage ('Deployment to Nexus') {
        steps {
            sh '/opt/maven/bin/mvn clean deploy -Dmaven.test.skip=false'
        }
    }
    stage ('Deployment in Dev server') {
        steps {
            sh 'export JENKINS_NODE_COOKIE=dontkillme ;nohup java -jar $WORKSPACE/target/*.jar &'
        }
    }
}
}
