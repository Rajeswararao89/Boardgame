
pipeline {
    agent any
    
    tools {
        jdk 'jdk17'
        maven 'maven3'
    }

    environment {
        SCANNER_HOME= tool 'sonar-scanner'
    }

    stages {
        stage('Git Checkout') {
            steps {
               git branch: 'main', credentialsId: 'git-cred', url: 'https://github.com/Rajeswararao89/Boardgame'
            }
        }
        
        stage('Compile') {
            steps {
                sh "mvn compile"
            }
        }
        
        stage('Test') {
            steps {
                sh "mvn test"
            }
        }
        
        stage('File System Scan') {
            steps {
                sh "trivy fs --format table -o trivy-fs-report.html ."
            }
        }
        
stage('SonarQube Analysis') {
    steps {
        script {
            def scannerPath = "/var/lib/jenkins/tools/hudson.plugins.sonar.SonarRunnerInstallation/sonar-scanner/bin/sonar-scanner"
            
            sh """
                # Run sonar-scanner with a different working directory
                ${scannerPath} \\
                  -Dsonar.host.url=http://13.232.142.180:9000 \\
                  -Dsonar.token=squ_d03d0de62da06b855d4f97bb26cad3a1a3958778 \\
                  -Dsonar.projectKey=BoardGame \\
                  -Dsonar.projectName=BoardGame \\
                  -Dsonar.sources=src/main/java \\
                  -Dsonar.java.binaries=target/classes \\
                  -Dsonar.junit.reportsPath=target/surefire-reports \\
                  -Dsonar.jacoco.reportsPath=target/jacoco.exec \\
                  -Dsonar.working.directory=/tmp/sonar-scanner-work \\
                  -Dsonar.scanner.forcePermission=true
            """
        }
    }
}

stage('Quality Gate') {
    steps {
        script {
            // Wait for quality gate result (optional)
            sleep 30 // Wait a bit for analysis to complete
            echo "Quality Gate check would go here"
        }
    }
}
        
        stage('Build') {
            steps {
               sh "mvn package"
            }
        }
        
stage('Publish To Nexus') {
    steps {
        withMaven(globalMavenSettingsConfig: 'global-settings', jdk: 'jdk17', maven: 'maven3') {
            sh """
                mvn deploy -DskipTests \\
                -DaltReleaseDeploymentRepository=nexus-releases::default::http://admin:Raj.nexus@65.0.185.253:8081/repository/maven-releases/ \\
                -DaltSnapshotDeploymentRepository=nexus-snapshots::default::http://admin:Raj.nexus@65.0.185.253:8081/repository/maven-snapshots/
            """
        }
    }
}
        
        stage('Build & Tag Docker Image') {
            steps {
               script {
                   withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                            sh "docker build -t adijaiswal/boardshack:latest ."
                    }
               }
            }
        }
        
        stage('Docker Image Scan') {
            steps {
                sh "trivy image --format table -o trivy-image-report.html rajeshwararao78/boardshack:latest"
            }
        }
        
        stage('Push Docker Image') {
            steps {
               script {
                   withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                            sh "docker push rajeshwararao78/boardshack:latest"
                    }
               }
            }
        }
        stage('Deploy To Kubernetes') {
            steps {
               withKubeConfig(caCertificate: '', clusterName: 'kubernetes', contextName: '', credentialsId: 'k8-cred', namespace: 'webapps', restrictKubeConfigAccess: false, serverUrl: 'https://52.66.195.30:6443') {
                        sh "kubectl apply -f deployment-service.yaml"
                }
            }
        }
        
        stage('Verify the Deployment') {
            steps {
               withKubeConfig(caCertificate: '', clusterName: 'kubernetes', contextName: '', credentialsId: 'k8-cred', namespace: 'webapps', restrictKubeConfigAccess: false, serverUrl: 'https://52.66.195.30:6443') {
                        sh "kubectl get pods -n webapps"
                        sh "kubectl get svc -n webapps"
                }
            }
        }
        
        
    }
    post {
    always {
        script {
            def jobName = env.JOB_NAME
            def buildNumber = env.BUILD_NUMBER
            def pipelineStatus = currentBuild.result ?: 'UNKNOWN'
            def bannerColor = pipelineStatus.toUpperCase() == 'SUCCESS' ? 'green' : 'red'

            def body = """
                <html>
                <body>
                <div style="border: 4px solid ${bannerColor}; padding: 10px;">
                <h2>${jobName} - Build ${buildNumber}</h2>
                <div style="background-color: ${bannerColor}; padding: 10px;">
                <h3 style="color: white;">Pipeline Status: ${pipelineStatus.toUpperCase()}</h3>
                </div>
                <p>Check the <a href="${BUILD_URL}">console output</a>.</p>
                </div>
                </body>
                </html>
            """

            emailext (
                subject: "${jobName} - Build ${buildNumber} - ${pipelineStatus.toUpperCase()}",
                body: body,
                to: 'jaiswaladi246@gmail.com',
                from: 'jenkins@example.com',
                replyTo: 'jenkins@example.com',
                mimeType: 'text/html',
                attachmentsPattern: 'trivy-image-report.html'
            )
        }
    }
}

}
