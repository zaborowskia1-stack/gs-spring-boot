pipeline {
    agent any

    options {
        skipStagesAfterUnstable()
    }

    tools {
        maven '3.9.11'
    }

    environment {
        NEXUS_CREDENTIALS = credentials('nexus-admin') // Jenkins credentials ID
        NEXUS_URL = 'http://172.17.0.2:8081/repository/maven-releases/'
        GROUP_ID = 'com.example'
        ARTIFACT_ID = 'spring-boot-complete'
        VERSION = '0.0.1' // Must not be a SNAPSHOT
        PACKAGING = 'jar'
        FILE = "target/${ARTIFACT_ID}-${VERSION}.jar"
    }

    stages {
        stage('Checkout Source Code') {
            steps {
                git branch: 'main', url: 'git@github.com:zaborowskia1-stack/gs-spring-boot.git'
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
                sh "mvn clean package -DskipTests -Drevision=${VERSION}"
            }
        }

        stage('Deploy to Nexus') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'nexus-admin', 
                                                 usernameVariable: 'NEXUS_USER', 
                                                 passwordVariable: 'NEXUS_PASS')]) {
                    sh """
                        mvn deploy:deploy-file \
                        -DgroupId=${GROUP_ID} \
                        -DartifactId=${ARTIFACT_ID} \
                        -Dversion=${VERSION} \
                        -Dpackaging=${PACKAGING} \
                        -Dfile=${FILE} \
                        -DrepositoryId=nexus-admin \
                        -Durl=${NEXUS_URL} \
                        -Dusername=${NEXUS_USER} \
                        -Dpassword=${NEXUS_PASS}
                    """
                }
            }
        }

        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: "target/*.jar", fingerprint: true
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
        success {
            echo 'Release build successful!'
        }
        failure {
            echo 'Release build failed!'
        }
    }
}
