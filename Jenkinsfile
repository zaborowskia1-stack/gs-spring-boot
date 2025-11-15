pipeline {
    agent any

    options {
        skipStagesAfterUnstable()
    }

    tools {
        maven '3.9.11'
    }

    environment {
        NEXUS_URL = "http://nexus:8081"
        NEXUS_REPO = "maven-releases"
        NEXUS_CREDENTIALS = "nexus-admin"
        GROUP_ID = "com.example"
        ARTIFACT_ID = "spring-boot-complete"
        VERSION = "0.0.1-SNAPSHOT"
        PACKAGING = "jar"
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

        stage('Deploy to Nexus') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${NEXUS_CREDENTIALS}", 
                                                 usernameVariable: 'NEXUS_USER', 
                                                 passwordVariable: 'NEXUS_PASS')]) {
                    sh '''
                        mvn deploy:deploy-file \
                          -DgroupId=${GROUP_ID} \
                          -DartifactId=${ARTIFACT_ID} \
                          -Dversion=${VERSION} \
                          -Dpackaging=${PACKAGING} \
                          -Dfile=target/${ARTIFACT_ID}-${VERSION}.jar \
                          -DrepositoryId=nexus-releases \
                          -Durl=${NEXUS_URL}/repository/${NEXUS_REPO} \
                          -Dusername=${NEXUS_USER} \
                          -Dpassword=${NEXUS_PASS}
                    '''
                }
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
            echo 'Build failed!'
        }
    }
}
