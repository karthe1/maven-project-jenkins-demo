pipeline {
  agent {
    label "e2e-framework"
  }
  tools {
    maven 'Maven 3.6.0'
  }
  triggers {
    pollSCM('H/1 * * * *')
  }
  environment {
    PROJECT = 'maven-project-jenkins-demo'
    GIT_CRED = 'svn_git'
    IAC_REPO = 'https://github.com/karthe1/maven-project-jenkins-demo'
    //Below is the Kubernetes server. For our case it is not required.
//    BROKER_ADDRESS = 'failover:(nio://10.127.1.100:30616)?startupMaxReconnectAttempts=2&initialReconnectDelay=1000'
  }

  stages {
    stage('Bootstrap') {
      steps {
        // Clean-up Workspace
        sh 'git reset --hard'
        sh 'git clean -fdx'
      }
    }
    stage('Checkout maven-project-jenkins-demo project'){
      steps {
        ws("${env.WORKSPACE}/iac") {
          // Clean workspace
          cleanWs()

          git url: env.IAC_REPO,
          branch: 'master',
          credentialsId: env.GIT_CRED
        }
      }
    }
    stage('Build') {
      steps {
        sh 'mvn clean install -U'
      }
    }
    stage('Wait for Setup to be Build'){
      steps{
        sleep(time:1,unit:"MINUTES")
      }
    }
    stage('Run Maven Sample Project with JenkinsPipelie') {
      steps {
        script {
          try {
            //sh 'ssh -f passwordFile username@hostname; scp jar to destination;java -cp target/e2e-framework-0.0.1-SNAPSHOT-jar-with-dependencies.jar trilliant.performance.tools.e2e.dcs.E2eListener'
            sh 'java -jar target/jenkins-1.0-SNAPSHOT-jar-with-dependencies.jar'
          } catch(Exception e){
            echo "Stage failed when listening the responses, but we still continue"
          }
        }
      }
    }
    stage('Run Second time Maven Sample Project with JenkinsPipelie') {
      steps {
        script {
          try {
          //sh ssh -f passwordFile username@hostname; scp jar to destination;'java -cp target/e2e-framework-0.0.1-SNAPSHOT-jar-with-dependencies.jar trilliant.performance.tools.e2e.dcs.E2eMainApp'
            sh 'java -jar target/jenkins-1.0-SNAPSHOT-jar-with-dependencies.jar'
          } catch(Exception e){
            echo "Stage failed when posting the requests, but we still continue"
          }
        }
      }
    }
  }
}
