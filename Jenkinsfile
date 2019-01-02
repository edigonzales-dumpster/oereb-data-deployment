pipeline {
    agent any
    stages {
        stage('Prepare') {
            steps {
                git 'https://github.com/edigonzales/oereb-data-deployment.git'
            }
        }
        stage('Export and archive data') {
            steps {
                sh './gradlew --no-daemon exportLandUsePlansDataset'
                archiveArtifacts artifacts: 'ch.so.arp.nutzungsplanung.oereb.xtf', fingerprint: true
                fileOperations([fileDeleteOperation(excludes: '', includes: 'ch.so.arp.nutzungsplanung.oereb.xtf')])
            }
        }
        stage('Grab data from archive') {
            steps {
                copyArtifacts(
                    projectName: '${JOB_NAME}',
                    filter: 'ch.so.arp.nutzungsplanung.oereb.xtf',
                    fingerprintArtifacts: true,
                    selector: [$class: 'SpecificBuildSelector', buildNumber: '${BUILD_NUMBER}'],
                    target: '/tmp/${JOB_NAME}/${BUILD_NUMBER}'
                )
            }
        }
        stage('Import to Staging') {
            steps {
                sh './gradlew --no-daemon importLandUsePlansDataSetToOerebStaging -Pxtf=/tmp/${JOB_NAME}/${BUILD_NUMBER}/ch.so.arp.nutzungsplanung.oereb.xtf'
            }
        }   
        stage('Validation') {
            steps {
                input message: 'Daten publizieren?', parameters: [string(defaultValue: '', description: '', name: 'Message', trim: true)], submitter: 'stefan'
            }
        }
        stage('Import to Production') {
            steps {
                sh './gradlew --no-daemon importLandUsePlansDataSetToOerebProduction -Pxtf=/tmp/${JOB_NAME}/${BUILD_NUMBER}/ch.so.arp.nutzungsplanung.oereb.xtf'
            }
        } 
       
    }
    post {
        always {
            deleteDir() 
        }
    }
}
