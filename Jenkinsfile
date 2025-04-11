pipeline {
    agent { label 'codespace-agent' } // Use the agent you set up earlier
    stages {
        stage('Build Container') {
            steps {
                script {
                    // Build the Docker image using the new code from the pull request
                    sh 'docker build -t my-app:${BUILD_NUMBER} .'
                    // Optionally, run the container to verify it starts
                    sh 'docker run --rm my-app:${BUILD_NUMBER} echo "Container built successfully"'
                }
            }
        }
        stage('Run Unit Tests') {
            steps {
                // Run Maven tests (at least one unit test should pass)
                sh 'mvn test'
            }
            post {
                always {
                    // Archive test results for Jenkins to display
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') { // 'SonarQube' is the name of the SonarQube server configured in Jenkins
                    sh 'mvn sonar:sonar -Dsonar.projectKey=my-app -Dsonar.host.url=http://localhost:9000 -Dsonar.login=<squ_41bae85b9a782f74b7b2020452ad188b9eb4d7a1>'
                }
            }
        }
    }
    post {
        always {
            // Archive artifacts (e.g., the built JAR file)
            archiveArtifacts artifacts: 'target/*.jar', allowEmptyArchive: true
        }
    }
}