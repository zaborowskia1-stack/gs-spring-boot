pipeline {
    agent any

    options {
        skipStagesAfterUnstable()
    }

    tools {
        maven '3.9.11'
    }

    environment {
        NEXUS_REPO = 'http://nexus:8081/repository/maven-snapshots/'
    }

    stages {
        stage('Checkout Source Code') {
            steps {
                git branch: 'main', url: 'https://github.com/zaborowskia1-stack/gs-spring-boot.git'
            }
        }

        stage('Test') {
            steps {
                sh 'git --version'
                sh 'mvn --version'
                sh 'mvn clean test'
            }
        }

        stage('Build and Package') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }

        stage('Deploy to Nexus Snapshots') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'nexus-admin', usernameVariable: 'NEXUS_USER', passwordVariable: 'NEXUS_PASS')]) {
                    sh """
                        mvn deploy:deploy-file \
                        -DgroupId=com.example \
                        -DartifactId=spring-boot-complete \
                        -Dversion=0.0.1-SNAPSHOT \
                        -Dpackaging=jar \
                        -Dfile=target/spring-boot-complete-0.0.1-SNAPSHOT.jar \
                        -DrepositoryId=nexus-admin \
                        -Durl=${NEXUS_REPO} \
                        -Dusername=${NEXUS_USER} \
                        -Dpassword=${NEXUS_PASS}
                    """
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
        success {
            echo 'Build and deployment successful!'
        }
        failure {
            echo 'Build or deployment failed!'
        }
    }
}
