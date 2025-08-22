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
