pipeline {
    agent {
        docker {
            image 'ubuntu:latest'
        }
    }
    stages {
        stage('Build') {
            steps {
                echo "Build Success"
            }
        }
        stage('Test') {
            steps {
                echo "Test Success"
            }
        }
		stage('Manual Approval') {
			steps {
				input message: 'Lanjutkan ke tahap Deploy? (Klik "Proceed" untuk mengakhiri)'
			}
		}
    }
}
