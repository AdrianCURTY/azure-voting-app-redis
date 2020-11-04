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
            bat label: 'trying to run test', returnStatus: true, script: ''' 
               cd tests
               pipenv install --skip-lock
               pipenv run pytest
               '''
         }
      }
      stage('Stop test app') {
         steps {
            bat script: """
               docker-compose down
            """
         }
      }
       stage('Push Container'){
         steps {
            echo "WORKSPACE is $WORKSPACE"
            dir("$WORKSPACE/azure-vote"){
               script {
                  docker.withRegistry('http://index.docker.io/v1/','DockerHub'){
                     def image = docker.build('naidra/jenkins-course')
                     //image.push('1')
                  }
               }
            }
         }
      } 
      stage('Run Anchor'){
            steps {
               bat script: """
               echo naidra/jenkins-course > anchor-images
               """
               anchore name: 'anchor-images'
            }
      }
      //Powershell.exe -executionpolicy remotesigned -File  C:\Users\SE\Desktop\ps.ps1
   }

}
