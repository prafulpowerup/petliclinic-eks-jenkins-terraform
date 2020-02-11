pipeline {
    agent any
       triggers {
        pollSCM "* * * * *"
       }
    stages {
        stage('Build Application') { 
            steps {
                echo '=== Building Petclinic Application ==='
                sh 'mvn -B -DskipTests clean package' 
            }
        }
        stage('Test Application') {
            steps {
                echo '=== Testing Petclinic Application ==='
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('Build Docker Image') {
            when {
                branch 'master'
            }
            steps {
                echo '=== Building Petclinic Docker Image ==='
                script {
                    app = docker.build("318478960641.dkr.ecr.us-east-1.amazonaws.com/eksdemo-java")
                }
            }
        }
        stage('Push Docker Image') {
            when {
                branch 'master'
            }
            steps {
                echo '=== Pushing Petclinic Docker Image ==='
                sh '$(aws ecr get-login --region us-east-1 --no-include-email)'
                sh "docker build -t 318478960641.dkr.ecr.us-east-1.amazonaws.com/eksdemo-java:${env.BUILD_NUMBER} ."
                sh "docker push 318478960641.dkr.ecr.us-east-1.amazonaws.com/eksdemo-java:${env.BUILD_NUMBER}"
            
                }
            
        }
        stage('Remove local images') {
            steps {
                echo '=== Delete the local docker images ==='
                sh("docker rmi -f 318478960641.dkr.ecr.us-east-1.amazonaws.com/eksdemo-java:latest || :")
            }
        }
    }
}
