
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
            }
            failure {
                echo 'Build or tests failed!'
            }
        }
}
