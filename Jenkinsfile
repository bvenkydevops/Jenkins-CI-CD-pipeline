pipeline {
    agent any
    tools {
        maven 'maven-3.8'
        jdk 'jdk-11'
    }
    environment {
        REGISTRY = "jfrog.company.com/docker"
        IMAGE_NAME = "myapp"
        SONARQUBE = "sonarqube-server"
    }

    stages {
        /* Declarative Checkout (Auto) */
        stage('Checkout') {
            steps {
                echo "Declarative checkout happens automatically"
            }
        }
        stage('Jira ID Validation') {
            steps {
                echo "Validating Jira ID from PR"
                // custom script / webhook validation
            }
        }
        stage('Unit Testing') {
            steps {
                sh 'mvn test'
            }
        }
        stage('SonarQube Scan') {
            steps {
                withSonarQubeEnv("${SONARQUBE}") {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        stage('Build Artifact') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }
        stage('Docker Build') {
            steps {
                sh """
                  docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} .
                """
            }
        }
        stage('Docker Image Scan') {
            steps {
                echo "Scanning image with Twistlock & JFrog X-Ray"
                // Fail build if CVE-1 / CVE-2 found
            }
        }
        stage('Re-Tag & Push Image') {
            steps {
                sh """
                  docker tag ${IMAGE_NAME}:${BUILD_NUMBER} ${REGISTRY}/${IMAGE_NAME}:${BUILD_NUMBER}
                  docker push ${REGISTRY}/${IMAGE_NAME}:${BUILD_NUMBER}
                """
            }
        }
        /* -------- ENV PREPARATION -------- */

        stage('Prepare Dev Env') {
            when { branch 'dev' }
            steps {
                sh 'kubectl apply -f k8s/dev/'
            }
        }
        stage('Prepare QA Env') {
            when { branch 'qa' }
            steps {
                sh 'kubectl apply -f k8s/qa/'
            }
        }
        stage('Prepare Prod Env') {
            when { branch 'main' }
            steps {
                sh 'kubectl apply -f k8s/prod/'
            }
        }
        /* -------- DEPLOYMENT -------- */

        stage('Deploy Dev') {
            when { branch 'dev' }
            steps {
                sh 'kubectl rollout restart deployment myapp -n dev'
            }
        }
        stage('Deploy QA') {
            when { branch 'qa' }
            steps {
                sh 'kubectl rollout restart deployment myapp -n qa'
            }
        }

        stage('Deploy Prod') {
            when { branch 'main' }
            steps {
                sh 'kubectl rollout restart deployment myapp -n prod'
            }
        }
          /* -------- DOWNSTREAM JOB -------- */

        stage('Trigger Downstream E2E Job') {
            when {
                anyOf {
                    branch 'qa'
                    branch 'main'
                }
            }
            steps {
                echo "Triggering Downstream E2E Automation Job"
                build job: 'E2E-Automation-Job',
                      parameters: [
                          string(name: 'ENV', value: "${BRANCH_NAME}"),
                          string(name: 'IMAGE_TAG', value: "${BUILD_NUMBER}")
                      ],
                      propagate: true,
                      wait: true
            }
        }
    }
    post {
        success {
            echo "Pipeline succeeded"
            // email notification
        }

        failure {
            echo "Pipeline failed → Rollback triggered"
            sh 'kubectl rollout undo deployment myapp'
            // failure email
        }
        always {
                echo "Cleaning workspace"
            cleanWs()
        }
    }
}
