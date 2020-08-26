pipeline { //Ao inves de escrevermos um Pipeline script diretamente no Jenkins. Nós construímos um
    agent any //Jenkins File que irá conter o Pipeline script específico para cada caso.
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
        stage ('API Tests') { //Fazendo os Testes da API
            steps {
                dir('api-test'){ //aparentemente cria um diretório com esse nome
                git credentialsId: 'github_login', url: 'https://github.com/pedrHenrique/tasks-api-test' //baixa o conteúdo desse repositório
                bat 'mvn test' //como ele só possui testes com a API REST, executa os mesmos
                }
            }
        }
        stage ('Deploy Frontend') {
            steps {
                dir('frontend') {
                    git credentialsId: 'github_login', url: 'https://github.com/pedrHenrique/tasks-frontend' 
                    bat 'mvn clean package'
                    deploy adapters: [tomcat8(credentialsId: 'TomCatLogin', path: '', url: 'http://localhost:8001')], contextPath: 'tasks', war: 'target\\tasks.war'
                }
            }
        }
    }
}

