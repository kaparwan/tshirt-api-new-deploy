/* groovylint-disable SpaceAfterMethodCallName, SpaceInsideParentheses */
pipeline {
  agent any
  tools {
      maven 'maven-3.8.5'
  }

    stages {
        stage('Env Variables') {
      steps {
        echo "The build number is ${env.BUILD_NUMBER}"
        echo "You can also use \${BUILD_NUMBER} -> ${BUILD_NUMBER}"
        sh 'echo "I can access $BUILD_NUMBER in shell command as well."'
      }
        }

    // stage('Unit Test Execution - Paused') {
    //   steps {
    //     sh 'echo Unit Test  #mvn clean test'
    //   }
    // }

    stage('Build' ) {
      steps {
        //sh 'mvn -e clean package #deploy -DmuleDeploy'
        sh '# mvn -e clean deploy -DmuleDeploy'
        sh 'mvn -e clean install'
      }
    }

    stage ('Deploy') {
      steps {
        sshagent(credentials : ['ac114bc0-d7ab-4967-904b-1dda36b8314a']) {
            sh 'ssh -o StrictHostKeyChecking=no vagrant@dev-runtime hostname'
            sh 'scp -o StrictHostKeyChecking=no ./target/*.jar  vagrant@dev-runtime:/opt/mule/apps/'
        }
      }
    }
    }
}