pipeline {
    agent any
    
    environment {
        // Define environment variables
        APP_NAME = 'my-application'
        VERSION = "${BUILD_NUMBER}"
        ARTIFACT_NAME = "${APP_NAME}-${VERSION}.jar"
        SONAR_PROJECT_KEY = 'my-app-key'
    }
    
    tools {
        // Define tools (configure these in Jenkins Global Tool Configuration)
        maven 'Maven'
        jdk 'JDK-21'
    }
    
    stages {
        stage('Code Checkout') {
            steps {
                echo '========== Stage 1: Checking out code from Git =========='
                git branch: 'main',
                    credentialsId: 'git-credentials',
                    url: 'https://github.com/anushar1912/jenkines-test'
                
                echo 'Code checkout completed successfully'
            }
        }
        
        stage('Build') {
            steps {
                echo '========== Stage 2: Building the application =========='
                script {
                    // For Maven project
                    sh 'mvn clean compile'
                    
                    // Alternative for Gradle:
                    // sh './gradlew clean build'
                    
                    // Alternative for Node.js:
                    // sh 'npm install'
                    // sh 'npm run build'
                }
                echo 'Build completed successfully'
            }
        }
        
        stage('SAST - SonarQube Analysis') {
            steps {
                echo '========== Stage 3: Running SAST with SonarQube =========='
                script {
                    withSonarQubeEnv('SonarQube') {
                        // For Maven
                        sh """
                            mvn sonar:sonar \
                              -Dsonar.projectKey=${SONAR_PROJECT_KEY} \
                              -Dsonar.projectName=${APP_NAME} \
                              -Dsonar.projectVersion=${VERSION}
                        """
                        
                        // Alternative for Gradle:
                        // sh './gradlew sonarqube'
                        
                        // Alternative using SonarScanner CLI:
                        // sh """
                        //     sonar-scanner \
                        //       -Dsonar.projectKey=${SONAR_PROJECT_KEY} \
                        //       -Dsonar.sources=src \
                        //       -Dsonar.host.url=${SONAR_HOST_URL}
                        // """
                    }
                }
                echo 'SonarQube analysis completed'
            }
        }
        
        stage('Quality Gate') {
            steps {
                echo '========== Checking SonarQube Quality Gate =========='
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
                echo 'Quality Gate passed'
            }
        }
        
        stage('Artifact Packaging') {
            steps {
                echo '========== Stage 4: Packaging artifacts =========='
                script {
                    // For Maven - create JAR/WAR
                    sh 'mvn package -DskipTests'
                    
                    // Archive the artifact
                    archiveArtifacts artifacts: "target/${ARTIFACT_NAME}", 
                                     fingerprint: true,
                                     allowEmptyArchive: false
                    
                    // Alternative for Docker image
                    // sh """
                    //     docker build -t ${APP_NAME}:${VERSION} .
                    //     docker tag ${APP_NAME}:${VERSION} ${APP_NAME}:latest
                    // """
                }
                echo 'Artifact packaging completed'
            }
        }
        
        stage('Deployment Simulation') {
            steps {
                echo '========== Stage 5: Simulating deployment =========='
                script {
                    // Simulate deployment to different environments
                    
                    // Option 1: Copy to deployment directory
                    sh """
                        echo 'Deploying to DEV environment...'
                        mkdir -p /tmp/deployments/dev
                        cp target/${ARTIFACT_NAME} /tmp/deployments/dev/
                        echo 'Application deployed to DEV: /tmp/deployments/dev/${ARTIFACT_NAME}'
                    """
                    
                    // Option 2: Simulate Docker deployment
                    sh """
                        echo 'Simulating Docker container deployment...'
                        echo 'Container would be started with: docker run -d -p 8080:8080 ${APP_NAME}:${VERSION}'
                    """
                    
                    // Option 3: Simulate Kubernetes deployment
                    sh """
                        echo 'Simulating Kubernetes deployment...'
                        echo 'Would execute: kubectl apply -f k8s/deployment.yaml'
                        echo 'Deployment Name: ${APP_NAME}'
                        echo 'Version: ${VERSION}'
                        echo 'Replicas: 3'
                        echo 'Deployment Status: SIMULATED SUCCESS'
                    """
                    
                    // Health check simulation
                    sleep 5
                    echo 'Running health checks...'
                    sh """
                        echo 'Health Check: OK'
                        echo 'Status: Running'
                        echo 'Deployment completed successfully!'
                    """
                }
            }
        }
    }
    
    post {
        success {
            echo '========== Pipeline Completed Successfully =========='
            emailext(
                subject: "SUCCESS: Jenkins Pipeline - ${APP_NAME} #${BUILD_NUMBER}",
                body: """
                    Pipeline executed successfully!
                    
                    Job: ${JOB_NAME}
                    Build: ${BUILD_NUMBER}
                    Artifact: ${ARTIFACT_NAME}
                    
                    View details: ${BUILD_URL}
                """,
                to: 'team@example.com'
            )
        }
        
        failure {
            echo '========== Pipeline Failed =========='
            emailext(
                subject: "FAILURE: Jenkins Pipeline - ${APP_NAME} #${BUILD_NUMBER}",
                body: """
                    Pipeline execution failed!
                    
                    Job: ${JOB_NAME}
                    Build: ${BUILD_NUMBER}
                    
                    View logs: ${BUILD_URL}console
                """,
                to: 'team@example.com'
            )
        }
        
        always {
            echo 'Cleaning up workspace...'
            cleanWs()
        }
    }
}
