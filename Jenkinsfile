pipeline {
    agent any

    options {
        skipStagesAfterUnstable()
    }

    tools {
        maven '3.9.11'
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

        stage('Deploy to Nexus') {
            environment {
                NEXUS_REPO = 'http://nexus:8081/repository/maven-releases'
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'nexus-admin', 
                                                 passwordVariable: 'NEXUS_PASS', 
                                                 usernameVariable: 'NEXUS_USER')]) {
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

        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
        success {
            echo 'Build and deploy successful!'
        }
        failure {
            echo 'Build or deploy failed!'
        }
    }
}
