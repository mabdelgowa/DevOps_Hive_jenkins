pipeline{
  environment {
    registry = "docker.io/namespace/acmewebsite"
    registryCredential = 'docker-hub'
    dockerImage = ''
    AWS_ACCESS_KEY_ID = credentials('jenkins_access_key_id')
    AWS_SECRET_ACCESS_KEY = credentials('jenkins_aws_secret_access_key')
    TF_VAR_env_prefix = 'test'
  }
  agent any
  stages{
    stage("Installing Librariesand Requirements"){
      steps{
        script{
          echo "Installing Project Libraries and Requirements"
          sh "python -m pip install --upgrade pip"
          sh "pip install prometheus-client flake8 pytest fastapi uvicorn pytest requests prometheus-client httpx"
          sh "if [ -f requirements.txt ]; then pip install -r requirements.txt; fi"
      }
    }
  }
    stage("Lenting"){
      steps{
        script{
          echo "Lenting The Code"
          sh "flake8 . --count --select=E9,F63,F7,F82 --show-source --statistics"
          sh "flake8 . --count --exit-zero --max-complexity=10 --max-line-length=127 --statistics"
        }
      } 
    }
    stage("Test"){
     steps{
       script{
         echo "Testing The Code"
         sh "pytest ./test/unit.py"
       }
     } 
    }
    stage("build image"){
      steps{
        script{
          echo "building the image of application"
          sh 'docker build -t mahmoudabdelgowad/devops_hive:jenkins .'
          docker.withRegistry( 'https://docker.io', registryCredential ) {
            sh 'docker push mahmoudabdelgowad/devops_hive:jenkins '
          }

        }
      }
    }
  }
  post {
    failure {
      mail( to: 'mabdelgowad144@gmail.com',
              subject: "FAILED: Build ${env.JOB_NAME}",
              body: "Build failed ${env.JOB_NAME} build no: ${env.BUILD_NUMBER}.\n\nView the log at:\n ${env.BUILD_URL}\n\nBlue Ocean:\n${env.RUN_DISPLAY_URL}"
      )
    }
  }
}
