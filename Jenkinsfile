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
        
    }
}