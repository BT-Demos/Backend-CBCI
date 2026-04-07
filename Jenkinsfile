
pipeline {
    agent any

    stages {
        stage('Build') {
            stages {
                stage('Compile') {
                    steps {
                        echo 'Compiling...'
                        sleep 5
                    }
                }
                stage('Package') {
                    steps {
                        echo 'Packaging....'
                        sleep 5
                    }
                }
            }
        }

        stage('Registering build artifact') {
            steps {
                script {
                    echo 'Registering the metadata'
                    echo 'Another echo to make the pipeline a bit more complex'
                    def artifactId = registerBuildArtifactMetadata(
                        name: "Backend-CBCI",
                        version: "12.26",
                        type: "docker",
                        url: "http://localhost:2001",
                        digest: "6f637064707039346163663237383938",
                        label: "new-artifact"
                    )
                    echo "Artifact Id is: ${artifactId}"
                    env.ARTIFACT_ID = artifactId
                    sleep 3
                }
            }
        }

        stage('Test with Trivy Scan') {
            steps {
                sh '''
                if ! command -v trivy > /dev/null; then
                  echo "Installing Trivy..."
                  curl -sL https://github.com/aquasecurity/trivy/releases/download/v0.65.0/trivy_0.65.0_Linux-64bit.tar.gz | tar zxvf - -C /tmp
                  mv /tmp/trivy ./trivy
                  chmod +x ./trivy
                fi

                # Run Trivy scan and save SARIF report
                ./trivy fs . --format sarif --output trivy-results.sarif || true
                '''
            }
        }
        stage('Deploy to Preprod') {
            steps {
                echo 'Deploying...'
                registerDeployedArtifactMetadata(
                    artifactId: "${env.ARTIFACT_ID}",
                    targetEnvironment: "pre-prod",
                    labels: "pre-prod"
                )
            }
        }
        stage('Deploy to QA') {
            steps {
                echo 'Deploying...'
                registerDeployedArtifactMetadata(
                    artifactId: "${env.ARTIFACT_ID}",
                    targetEnvironment: "qa",
                    labels: "qa"
                )
            }
        }
    }

        post {
            always {
                echo 'Pipeline completed.'
                archiveArtifacts artifacts: 'trivy-results.sarif', fingerprint: true
            }
            failure {
                echo 'Build or tests failed!'
            }
        }
}
