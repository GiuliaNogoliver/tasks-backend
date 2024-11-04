pipeline {
    agent any
    stages {
        stage('Build Backend') {
            steps {
                bat 'mvn clean package -DskipTests=true'
            }
        }

        stage('Unit Tests') {
            steps {
                bat 'mvn test'
            }
        }

        stage('Sonar Analysis') {
            // environment {
            //     scannerHome = tool 'SONAR_SCANNER'
            // }
            // steps {
            //     withSonarQubeEnv('SONAR_INSTALLATION'){
            //         bat "\"${scannerHome}\\bin\\sonar-scanner\" -e -D\"sonar.projectKey=DeployBac\" -D\"sonar.host.url=http://localhost:9000\" -D\"sonar.login=e88061e20d003a15872df2e7aaa46d97a5be69da\""
            //     }
            // }
            // steps {
            //     bat 'sonar.projectKey=DeployBac
            //             sonar.host.url=http://localhost:9000
            //             sonar.login=e88061e20d003a15872df2e7aaa46d97a5be69da
            //             sonar.java.binaries=target'
            // }
            steps {
                bat 'mvn clean install'
            }
        }

        stage('Quality Gate'){
            steps {
                // sleep(5)
                // timeout(time: 1, unit: 'MINUTES'){
                //     waitForQualityGate abortPipeline: true
                // }
                bat 'mvn test'
            }
        }

        stage('Deploy Backend'){
            steps{
                deploy adapters: [tomcat9(credentialsId: 'tomcat_login', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
            }
        }

        stage('API Test'){
            steps {
                dir('api-test') {
                    git branch: 'main', credentialsId: 'github_login', url: 'https://github.com/GiuliaNogoliver/tasks-api-test'
                    bat 'mvn test'
                }
            }
        }

        stage('Deploy Front'){
            steps {
                git credentialsId: 'github_login', url: 'https://github.com/GiuliaNogoliver/tasks-frontend'
                    bat 'mvn clean package'
                    deploy adapters: [tomcat9(credentialsId: 'tomcat_login', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks-frontend', war: 'target/tasks.war'
            }
        }

        stage('Functional Test Automation'){
            steps {
                dir('funcional-test') {
                    git branch: 'main', credentialsId: 'github_login', url: 'https://github.com/GiuliaNogoliver/tasks-funcional-tests'
                    bat 'mvn test'
                }
            }
        }

        stage('Deploy Prod'){
            steps {
                bat 'mvn clean install'
            }
            // steps {
            //     bat 'docker-compose build'
            //     bat 'docker-compose up -d'
            // }
        }

        stage('Health Check'){
            steps {
                dir('funcional-test') {
                    sleep(2)
                    bat 'mvn verify -DskipTests'
                }
            }
        }
    }
}