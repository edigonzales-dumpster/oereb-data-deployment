pipeline {
    agent any
    stages {
        stage('Prepare') {
            steps {
                git 'https://github.com/edigonzales/oereb-data-deployment.git'
            }
        }
        stage('Compile') {
            steps {
                sh './gradlew --no-daemon replaceCantonalLandUsePlansData_2502'
            }
        }
    }
}