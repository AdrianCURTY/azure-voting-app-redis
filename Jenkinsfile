pipeline {
   agent any
   stages {
      stage('Verify Branch') {
         steps {
            echo "$GIT_BRANCH"
         }
      }
      stage('Docker Build') {
         steps {
            bat script: 'docker images -a'
            bat script: """
               cd azure-vote/
               docker images -a
               docker build -t jenkins-pipeline .
               docker images -a
               cd ..
            """
         }
      }
      stage('Start test app') {
         steps {
            bat script: """
               docker-compose up -d
               Powershell.exe -executionpolicy remotesigned -File scripts/test_container.ps1
            """
         }
         post {
            success {
               echo "App started successfully :)"
            }
            failure {
               echo "App failed to start :("
            }
         }
      }
      stage('Run Tests') {
         steps {
            bat """
            cd tests
            set PIPENV_VENV_IN_PROJECT=true
            pipenv install pipenv install --skip-lock
            pipenv shell /c pytest
            """
          //  bat script: "pytest teststest_sample.py"
         }
      }
      stage('Stop test app') {
         steps {
            bat script: """
               docker-compose down
            """
         }
      }
      //Powershell.exe -executionpolicy remotesigned -File  C:\Users\SE\Desktop\ps.ps1
   }

}
