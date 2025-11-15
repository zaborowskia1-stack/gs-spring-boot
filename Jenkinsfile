pipeline {
    agent any

    environment {
        NEXUS_URL = 'http://172.17.0.2:8081/repository/maven-snapshots/'
        NEXUS_CREDENTIALS = 'nexus-admin' // the ID of the credentials in Jenkins
        JAR_FILE = 'target/spring-boot-complete-0.0.1-SNAPSHOT.jar'
        GROUP_ID = 'com.example'
        ARTIFACT_ID = 'spring-boot-complete'
        VERSION = '0.0.1-SNAPSHOT'
        PACKAGING = 'jar'
    }

    stages {
        stage('Deploy Existing JAR to Nexus') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${NEXUS_CREDENTIALS}", 
                                                  usernameVariable: 'NEXUS_USER', 
                                                  passwordVariable: 'NEXUS_PASS')]) {
                    sh """
                        mvn deploy:deploy-file \
                          -DgroupId=${GROUP_ID} \
                          -DartifactId=${ARTIFACT_ID} \
                          -Dversion=${VERSION} \
                          -Dpackaging=${PACKAGING} \
                          -Dfile=${JAR_FILE} \
                          -DrepositoryId=nexus-admin \
                          -Durl=${NEXUS_URL} \
                          -Dusername=${NEXUS_USER} \
                          -Dpassword=${NEXUS_PASS}
                    """
                }
            }
        }
    }

    post {
        success { echo 'JAR successfully deployed to Nexus snapshot repository!' }
        failure { echo 'Deployment failed.' }
    }
}
