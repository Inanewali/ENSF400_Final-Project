pipeline {
    agent { label 'codespace-agent' }
    stages {
        stage('Build Container') {
            steps {
                script {
                    sh 'docker build -t my-app:${BUILD_NUMBER} .'
                    sh 'docker run --rm my-app:${BUILD_NUMBER} echo "Container built successfully"'
                }
            }
        }
        stage('Unit Tests') {
            steps {
                sh './gradlew test'
            }
            post {
                always {
                    junit 'build/test-results/test/*.xml'
                }
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh './gradlew sonarqube -Dsonar.projectKey=my-app -Dsonar.host.url=http://localhost:9000 -Dsonar.login=<your-sonar-token>'
                }
            }
        }
        stage('Database Tests') {
            steps {
                sh './gradlew integrate'
            }
            post {
                always {
                    junit 'build/test-results/integrate/*.xml'
                }
            }
        }
        stage('BDD Tests') {
            steps {
                sh './gradlew generateCucumberReports'
                sh './gradlew jacocoTestReport'
            }
            post {
                always {
                    junit 'build/test-results/bdd/*.xml'
                }
            }
        }
        stage('Security: Dependency Analysis') {
            steps {
                sh './gradlew dependencyCheckAnalyze'
            }
        }
        stage('Performance Tests') {
            steps {
                sh './gradlew runPerfTests'
            }
        }
        stage('Mutation Tests') {
            steps {
                sh './gradlew pitest'
            }
        }
        stage('Build Documentation') {
            steps {
                sh './gradlew javadoc'
            }
        }
    }
}