pipeline {
    agent any

    environment {
        JAVA_HOME = '/usr/lib/jvm/java-11-openjdk-amd64'  // Update with your Java path
        MAVEN_HOME = '/opt/apache-maven-3.8.6'             // Update with your Maven path
        SONAR_SCANNER_HOME = '/opt/sonar-scanner-4.6.2.2472-linux'  // Update with your SonarScanner path
        PATH = "$JAVA_HOME/bin:$MAVEN_HOME/bin:$SONAR_SCANNER_HOME/bin:$PATH"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Unit Tests') {
            steps {
                sh 'mvn test'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    script {
                        def scannerHome = tool 'SonarQubeScanner'
                        withEnv(["PATH+SONAR_SCANNER_HOME=${scannerHome}/bin"]) {
                            sh """
                                sonar-scanner -Dsonar.projectKey=my-project \
                                              -Dsonar.sources=src \
                                              -Dsonar.host.url=http://192.168.100.10:9000 \
                                              -Dsonar.login=my-sonar-token
                            """
                        }
                    }
                }
            }
        }
    }

    post {
        always {
            junit '**/target/surefire-reports/*.xml'  // Capture test results
            archiveArtifacts 'target/*.jar'           // Archive build artifacts
        }
    }
}

