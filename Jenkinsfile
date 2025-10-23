pipeline {
agent any

options {
    skipStagesAfterUnstable()
}

tools {
    maven '3.9.11'
}

stages {
    stage('Check Environment') {
        steps {
            sh 'echo $PATH'
            sh 'java -version'
            sh 'mvn -v'
        }
    }

    stage('Build') {
        steps {
            sh 'mvn clean compile'
        }
    }

    stage('Test') {
        steps {
            sh 'mvn test'
        }
        post {
            always {
                junit 'target/surefire-reports/*.xml'
            }
        }
    }
    stage('Package') {
        steps {
            sh 'mvn package'
        }
    }
    stage('Deliver') {
        steps {
            sh 'ls -F'
            sh './jenkins/scripts/deliver.sh'
        }
    }
}
}

