pipeline {
  agent any

  environment {
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
        sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.url=http://3.108.218.14:9000/ -Dsonar.login=squ_d62d55aad4c7061393c05fc894c66b5138183956 -Dsonar.projectName=to-do-app \
               -Dsonar.sources=. \
               -Dsonar.projectKey=to-do-app '''
      }
    }

    stage('OWASP Dependency Check') {
        steps {
            dependencyCheck additionalArguments: '--scan ./ --output=dependency-check-report.xml', odcInstallation: 'DP'
              // Process XML using XSLT (optional)
            sh 'xsltproc your-stylesheet.xsl dependency-check-report.xml > dependency-check-report.html' // Assuming you have a stylesheet
        }
        // Archive both reports (if XSLT is used)
        archiveArtifacts artifacts: 'dependency-check-report.xml,dependency-check-report.html', fingerprint: true
    }

    stage('Docker Build') {
      steps {
        script {
          withDockerRegistry(credentialsId: 'f13e3268-eeb1-4cf8-a6d1-6ccb06a93d43') {
            sh "docker build -t todoapp:latest -f backend/Dockerfile ."
            sh "docker tag todoapp:latest ada100820/todoapp:latest "
            sh "docker push ada100820/todoapp:latest"
          }
        }
      }
    }

    stage('Trivy Scan') {
      steps {
        script {
          sh 'trivy image --format template --template /usr/local/share/trivy/templates/html.tpl --output trivy-report.html username/todoapp:latest'
        }
      }
    }

    stage('Docker Deploy') {
      steps {
        script {
          withDockerRegistry(credentialsId: 'f13e3268-eeb1-4cf8-a6d1-6ccb06a93d43') {
            sh "docker run -d --name to-do-app -p 4000:4000 username/todoapp:latest "
          }
        }
      }
    }
  }
}
