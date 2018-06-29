pipeline {
  agent any

  tools {
    gradle "gradle"
  }

  environment {
    GIT_CREDENTIALS = 'GIT_CREDENTIALS'
    MVN_BUILD_OPTS = '-Dskip.ut=true'
    MVN_GLOBAL_SETTINGS = 'MVN_GLOBAL_SETTINGS'
    MVN_SETTINGS = 'MVN_SETTINGS'
    TLS_JAVA = 'JAVA8'
    TLS_MAVEN = 'MAVEN3'
    REGION= 'us-east-1'
    S3_BUCKET = 'optumlifesciencesrwe'
  }

  options {
    buildDiscarder(logRotator(numToKeepStr: '10', artifactNumToKeepStr: '10'))
    disableConcurrentBuilds()
    timeout(time: 60, unit: 'MINUTES')
  }

  stages {
    stage('Checkout SCM') {
      steps {
        checkout scm
      }
    }

    stage('Compile, Build and Unit Test') {
      steps {
          sh 'gradle clean build'
      }
    }

    stage('Upload artifact to S3') {
      steps {
        withAWS(region: $REGION,credentials: 'AWS_CREDENTIALS') {
          s3Upload(bucket:"$S3_BUCKET", path:'ard-generator/', includePathPattern:'*.jar', workingDir:'build/libs')
        }
      }
    }
  }
}
