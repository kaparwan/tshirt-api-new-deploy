/* groovylint-disable NestedBlockDepth, SpaceAfterMethodCallName, SpaceInsideParentheses */
pipeline {
  agent any

  parameters {
    booleanParam(name: 'cloudhubDeploy', defaultValue: false)
  }

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

    stage('Deploy Local/Standalone or Cloudhub') {
      steps {
        script {
          if (params.cloudhubDeploy) {
            withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId:'anypoint-login-credentials',
usernameVariable: 'ANYPOINT_USERNAME', passwordVariable: 'ANYPOINT_PASSWORD']
            ]) {
              sh 'echo *** cloudhub deploy *** ; \
             mvn deploy  -DmuleDeploy   -Dmule.version=4.4.0 \
            -Danypoint.userName=$ANYPOINT_USERNAME  \
            -Danypoint.userPass=$ANYPOINT_PASSWORD \
            -DappName=tshirt-api-cloudhub \
            -Dcloudhub.environment=Sandbox \
            -DappName=tshirtApiCloudhub \
            -Dcloudhub.workerType=micro \
            -Dcloudhub.workers=1 '
            }
          } else {
            sshagent(credentials : ['af181f3a-9f7b-44ec-b2db-e8d9e63dc800']) {
              sh 'ssh -o StrictHostKeyChecking=no vagrant@prod-runtime hostname'
              sh 'scp -o StrictHostKeyChecking=no  ./target/tshirt-api*.jar  vagrant@prod-runtime:/opt/mule/apps/'
            }
          }
        }
      }
    }
    }
}