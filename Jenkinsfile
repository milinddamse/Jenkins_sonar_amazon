pipeline {
    agent any
 
    environment {
        // SonarQube configuration
        SCANNER_HOME = tool 'sonar-scanner'
        SONARQUBE_URL = 'http://192.168.17.131:9000'
        SONARQUBE_PROJECT_KEY = 'Amaon_jenekins_sonar'
    }
 
    stages {
        stage('Clone Source Code') {
            steps {
                git branch: 'main', url: 'https://github.com/milinddamse/Jenkins_sonar_amazon.git'
            }
        }
 
        stage('Build with Maven') {
            steps {
                script {
                    sh 'mvn clean package'
                }
            }
            post {
                success {
                    // Archive the WAR file for later use
                    archiveArtifacts artifacts: '/home/milind/target/Amazon.war', fingerprint: true
                }
            }
        }
 
        stage('Code Quality Analysis') {
            steps {
                withSonarQubeEnv('SonarQube Server Name') {
                    sh "mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.9.1.2184:sonar " +
                       "-Dsonar.projectKey=${SONARQUBE_PROJECT_KEY} " +
                       "-Dsonar.host.url=${SONARQUBE_URL} " +
                       "-Dsonar.login=sqa_67e7a5d2624d23bfadf69c5c813550750511375f"
                }
            }
        }
 
        stage('Wait for Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
 
        stage('Deploy to Tomcat with Ansible') {
            steps {
                script {
                    // Create an Ansible inventory file on the fly
                    writeFile file: 'inventory', text: '''
                    [local_systems]
                       localhost ansible_connection=local
                    '''
                    // Execute Ansible playbook
                    sh "ansible-playbook -i inventory tomcat-deploy.yml"
                }
            }
        }
    }
}
