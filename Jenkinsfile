pipeline {
	agent any

    // options {
    //     skipStagesAfterUnstable()
    // }

	stages {
	
        // stage('Build') {
        // 	steps {
        //         sh './gradlew -b build.gradle clean build'
        //     }
        //     post {
        //         always {
        //             archiveArtifacts artifacts: 'build/libs/*.?ar', fingerprint: true
        //         }
        //     }
        // }
        
  
        stage('SonarQube Analysis') {
            steps {
                script {
                        withSonarQubeEnv('SonarQube') {
                        sh './gradlew sonarqube'
                    } 
                }
            }
        }
        // stage("Quality Gate"){
        //     steps {
        //         script {
        //             waitForQualityGate abortPipeline: true
        //         }
        //     }
        // }

        stage("Quality Gate"){
            steps {
                script {
                    timeout(time: 1, unit: 'HOURS') {
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                            sh "Quality gate failed"
                            // emailext body: 'Your code was failed due to sonarqube quality gate', subject: 'Jenkins Failed Report', to: 'opeomotayo@gmail.com'
                            // error "Pipeline aborted due to quality gate failure: ${qg.status}"
                        }
                    }
                }
            }
        }
        stage('Test Reports') {
            steps {
                sh './gradlew test'
            }
        }
            
        stage('Nexus Upload') {
            steps {
                script {
                    nexusPublisher nexusInstanceId: 'nexus-server', nexusRepositoryId: 'gradle-snapshots',
                    packages: [ 
                        [$class: 'MavenPackage', 
                        mavenAssetList: [ 
                            [classifier: '', 
                            extension: 'jar', 
                            filePath: "build/libs/spring-boot-api-example-1.0.0.jar"],
                        ],
                        mavenCoordinate: [
                            artifactId: 'spring-boot-api-example', 
                            groupId: 'com.opeomotayo', 
                            packaging: "jar", version: "1.0.0"]
                        ]
                    ]
                    // nexusArtifactUploader artifacts: 
                    // [[  artifactId: 'spring-boot-app', 
                    //     file: "build/libs/spring-boot-api-example-1.0.0.jar",
                    //     type: 'jar'
                    // ]], 
                    //     credentialsId: 'nexusAdminCreds', 
                    //     groupId: 'com.opeomotayo', 
                    //     nexusUrl: '172.22.22.11:8081', 
                    //     nexusVersion: 'nexus3', 
                    //     protocol: 'http', 
                    //     repository: 'gradle-snapshots', 
                    //     version: "1.0.${BUILD_NUMBER}-SNAPSHOT"
                }
            }
        }
    }
}     