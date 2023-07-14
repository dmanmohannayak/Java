pipeline {
    agent any

    environment {
        function_name = 'java-sample'
    }

    stages {
        stage('Build') {
            steps {
                echo 'Build'
                sh 'mvn package'
            }
        }

        stage('Fetch') {
            steps {
                sh 'git rev-parse --resolve-git-dir /var/lib/jenkins/workspace/New_main@2/.git'
                sh 'git config remote.origin.url https://github.com/dmanmohannayak/Java.git'
                sh 'git fetch --no-tags --force --progress -- https://github.com/dmanmohannayak/Java.git +refs/heads/main:refs/remotes/origin/main'
            }
        }

        stage("SonarQube analysis") {
            agent any
            when {
                branch 'main'
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
                    } catch (Exception ex) {
                        // Handle the exception if required
                    }
                }
            }
        }

        stage('Push') {
            steps {
                echo 'Push'
                sh "aws s3 cp target/sample-1.0.3.jar s3://javasample1"
            }
        }

        stage('Deployments') {
            parallel {
                stage('Deploy to Dev') {
                    steps {
                        echo 'Build'
                        //sh "aws lambda update-function-code --function-name $function_name --region us-east-1 --s3-bucket bermtecbatch31 --s3-key sample-1.0.3.jar"
                    }
                }

                stage('Deploy to test') {
                    when {
                        branch 'main'
                    }
                    steps {
                        echo 'Build'
                        // sh "aws lambda update-function-code --function-name $function_name --region us-east-1 --s3-bucket bermtecbatch31 --s3-key sample-1.0.3.jar"
                    }
                }
            }
        }
    }
}
