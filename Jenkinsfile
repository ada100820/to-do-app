pipeline {
    agent any
   
    environment{
        SCANNER_HOME= tool 'sonar-scanner'
    }

    stages {
        stage('git-checkout') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/jaiswaladi246/to-do-app.git'
            }
        }
        
        stage('Sonar Analysis') {
            steps {
                   sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.url=http://3.110.213.114:9000/ -Dsonar.login=squ_d62d55aad4c7061393c05fc894c66b5138183956 -Dsonar.projectName=to-do-app \
                   -Dsonar.sources=. \
                   -Dsonar.projectKey=to-do-app '''
               }
            }
        
        stage('OWASP Dependency Check') {
            steps {
                   dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'DP'
                    dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
               }
            }
        stage('Docker Build') {
            steps {
               script{
                   withDockerRegistry(credentialsId: 'f13e3268-eeb1-4cf8-a6d1-6ccb06a93d43') {
                       sh "docker build -t  todoapp:latest -f docker/Dockerfile . "
                       sh "docker tag todoapp:latest username/todoapp:latest "
                   }
               }
            }
        }
        
    }
}
