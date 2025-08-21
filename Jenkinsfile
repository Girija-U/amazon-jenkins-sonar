pipeline {
    agent any

    tools {
        maven 'Maven3'
    }

    environment {
        SONAR_TOKEN = credentials('sonar-token') // Jenkins secret text credentials
    }

    stages {
        stage('Build WAR with Maven') {
            steps {
                dir('Amazon-Web') {
sh 'mvn clean package'
            }
       }
 }

        stage('SonarQube Analysis') {
            steps {
withSonarQubeEnv('sonarqube') {
sh 'mvn sonar:sonar -Dsonar.projectKey=devops_git'
                }
            }
        }

        stage('Check Quality Gate') {
            steps {
sh '''
                curl -s -u $SONAR_TOKEN: \
                "http://localhost:9000/api/qualitygates/project_status?projectKey=devops_git" \
                | grep -q '"status":"ERROR"' && echo "Quality Gate FAILED" && exit 1 || echo "Quality Gate PASSED"
                '''
            }
        }

        stage('Deploy with Ansible') {
            steps {
dir('ansible') {
sh '''
                    ansible-playbook -i inventory.ini playbook.yml
                    '''
                }
            }
        }
    }
}

