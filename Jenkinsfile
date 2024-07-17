pipeline {
  agent any
  options {
    buildDiscarder(logRotator(numToKeepStr: '5'))
  }
  environment {
    DOCKERHUB_CREDENTIALS = credentials('dh_id')
  }
  stages {
    stage('BUILD') {
      steps {
        sh 'docker build -t shereenfarag/dso-lab:test-owasp .'
      }
    }
    stage('LOGIN') {
      steps {
        sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
      }
    }
    stage('OWASP FS SCAN') {
      steps {
        dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'DP-Check'
        dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
      }
    }

    stage('PUSH') {
      steps {
        sh 'docker push shereenfarag/dso-lab:test-owasp'
      }
    }
}
  post {
    always {
      sh 'docker logout'
    }
  }
}
