pipeline {
    agent any

    stages {

        stage("Build from Github") {
            steps {
                git url: "https://github.com/joker295/insurance-pipeline"
            }
        }

        stage("Compile the code") {
            steps {
                sh "mvn compile"
            }
        }

        stage("Testing the Code") {
            steps {
                sh "mvn test"
            }
        }

        stage("Quality Assurance of the Code") {
            steps {
                sh "mvn pmd:pmd"
            }
        }

        stage("Package the code") {
            steps {
                sh "mvn package"
            }
        }

        stage("Create Dockerfile") {
            steps {
                writeFile file: 'Dockerfile', text: '''\
FROM openjdk:17
COPY target/*.jar app.jar
ENTRYPOINT ["java", "-jar", "/app.jar"]
'''
            }
        }

        stage("Build Docker Image") {
            steps {
                sh "docker build -t trickyknight0/insurance-app:latest ."
            }
        }

        stage("Docker Login & Push") {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-pwd', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh "echo $PASS | docker login -u $USER --password-stdin"
                    sh "docker push trickyknight0/insurance-app:latest"
                }
            }
        }

        stage("Deploy to Kubernetes") {
            steps {
                sh 'kubectl apply -f Deployment.yaml'
                sh 'kubectl get svc -n monitoring'
            }
        }
    }
}
