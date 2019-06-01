pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
		Stage('DeployToStaging') {
			when {
				branch 'master'
			}
			steps {
				withCredentials([usernamePassword(credentialsId: 'webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
				sshPublisher(
					failOnError: true,
					continuoOnError: false,
					publisher: [
						sshPublisherDesc(
							configName: 'staging',
							sshCredentials: [
								username: $USERNAME",
								encryptedPassphrase: "$USERPASS"
							],
							transfers: [
								sshTransfer(
									sourceFiles: 'dist/trainSchedule.zip',
									removePrefix: 'dist/'
									remoteDirectory: 'tmp',
									execCommand: 'sudo /user/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule && sudo /user/bin/systemctl start train-schedule'
								)
							]
						)
					]
				)
			}
		}

		Stage('DeployToProduction') {
			when {
				branch 'master'
			}
			steps {
				input 'Does the staging environment look OK?'
				milestone (1)
				withCredentials([usernamePassword(credentialsId: 'webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
				sshPublisher(
					failOnError: true,
					continuoOnError: false,
					publisher: [
						sshPublisherDesc(
							configName: 'production',
							sshCredentials: [
								username: $USERNAME",
								encryptedPassphrase: "$USERPASS"
							],
							transfers: [
								sshTransfer(
									sourceFiles: 'dist/trainSchedule.zip',
									removePrefix: 'dist/'
									remoteDirectory: 'tmp',
									execCommand: 'sudo /user/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule && sudo /user/bin/systemctl start train-schedule'
								)
							]
						)
					]
				)
			}
		}		
    }
}
