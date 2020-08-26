pipeline {
    agent any
    stages {
        stage ('Build Backend') {
            steps {
                bat 'mvn clean package -DskipTests=true' //Clean apaga o conteúdo
            }
        }
        stage ('Unit Tests') {
            steps {
                bat 'mvn test'
            }
        }
        stage ('Sonar Analysis') {
            environment {
                scannerHome = tool 'SONAR_SCANNER'
            }
            steps {
                withSonarQubeEnv('SONAR_LOCAL') {
                    bat "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBack -Dsonar.host.url=http://localhost:9000 -Dsonar.login=1f9bad6c384a60961ec46265dc05e8ee0a9629b4 -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/**,**/src/test/**,**/model/**,**Application.java "
                }
            }
        }
        stage ('Quality Gate') {
            steps {
                sleep(25) //Aparentemente esse sleep serviu para garantir que o qualitygate já tenha sido recebido.
                timeout(time: 1, unit: 'MINUTES') { 
                    waitForQualityGate abortPipeline: true
                } 
            }
        }
        stage ('Deploy Backend') {
            steps {
                deploy adapters: [tomcat8(credentialsId: 'TomCatLogin', path: '', url: 'http://localhost:8001')], contextPath: 'tasks-backend', war: 'target\\tasks-backend.war'
            }
        }
        stage ('API Tests') {
            steps {
                git credentialsId: 'github_login', url: 'https://github.com/pedrHenrique/tasks-api-test'
                bat 'mvn test'
            }
        }
    }
}