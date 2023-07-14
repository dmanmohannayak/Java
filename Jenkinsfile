pipeline {
    agent any
    stages {

        // CI Start
        stage('Build') {
            steps {
                echo 'Build'
                sh 'mvn package'
            }
        }

        stage("SonarQube analysis") {
            agent any
            when {
                anyOf {
                    branch 'main'
                }
            }
            steps {
                withSonarQubeEnv('Sonar') {
                    sh 'mvn sonar:sonar'
                }
            }
        }
        stage("Quality Gate") {
            steps {
                script {
                    try {
                        timeout(time: 10, unit: 'MINUTES') {
                            waitForQualityGate abortPipeline: true
                        }
                    }
                    catch (Exception ex) {

                    }
                }
            }
        }
        stage('Push') {
            steps {
                echo 'Push'
            }
        }

        // Ci Ended

        // CD Started

       
        stage('Deploy to Dev') {
            steps {
                echo 'Build'
            }
        }

        stage('Deploy to test ') {
            when {
                branch 'main'
            }
            steps {
                echo 'Build'
            }
        }
            
    }
        // CD Ended
}
