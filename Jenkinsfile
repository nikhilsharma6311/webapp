pipeline {
    agent any

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "maven"
    }

    stages {
        stage('Setup parameters') {
            steps {
                script {
				properties([parameters([choice(choices: ['AppServer', 'DBServer'], name: 'Server')])])
				}
			}
		}
		stage('Build') {
            steps {
                // Get some code from a GitHub repository
                git 'https://github.com/nikhilsharma6311/webapp.git'

                // Run Maven on a Unix agent.
                bat "mvn clean build-helper:timestamp-property install package"

                // To run Maven on a Windows agent, use
                // bat "mvn -Dmaven.test.failure.ignore=true clean package"
            }
        }
        stage('Test') {
            steps {
                bat 'mvn test'
            }
		}
		stage('Deploy to AppServer') {
			when {
                expression { 
                   return params.Server == "AppServer"
                }
			}
            steps {
                  bat "scp -i C:\\Users\\nikhilsharma02\\Appserver.pem -o StrictHostKeyChecking=no C:\\Windows\\System32\\config\\systemprofile\\AppData\\Local\\Jenkins\\.jenkins\\workspace\\pipeline\\target\\WebApp*.war ec2-user@3.109.206.25:/home/ec2-user/"
                  }
                   }
		stage('Deploy to DBServer') {
			when {
                expression { 
                   return params.Server == 'DBServer'
                }
			}
            steps {
                  bat "scp -i C:\\Users\\nikhilsharma02\\jumperserver.pem C:\\Windows\\System32\\config\\systemprofile\\AppData\\Local\\Jenkins\\.jenkins\\workspace\\pipeline\\target\\WebApp*.war ec2-user@13.127.108.243:/tmp/tomcat/webapps"
                  }
            }
        stage('Upload to S3 Bucket'){
             steps {
                  s3Upload consoleLogLevel: 'INFO', dontSetBuildResultOnFailure: false, dontWaitForConcurrentBuildCompletion: false, entries: [[bucket: 'demos3bucketnikhilsharmadev', excludedFile: '', flatten: false, gzipFiles: false, keepForever: false, managedArtifacts: false, noUploadOnFailure: true, selectedRegion: 'ap-south-1', showDirectlyInBrowser: false, sourceFile: '*/*.war', storageClass: 'STANDARD', uploadFromSlave: false, useServerSideEncryption: false]], pluginFailureResultConstraint: 'FAILURE', profileName: 'Artifact Upload', userMetadata: []
                  }				  
            }
        }
	}
