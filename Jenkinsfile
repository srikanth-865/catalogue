pipeline{
      agent {
        node {
            label 'ROBOSHOP' //we gave in jenkins UI for to match label for this name to run this pipeline
        }

      }

    stages{
        stage( 'Read Version'){
            steps{
                script{
                    
                      def  packageJson = readJSON file: 'package.json'
                      def  appversion =  packageJson.version
                      echo "The application version is: ${appversion}"

                }
            }

        }
    }


post { 
        always { 
            echo 'I will always say Hello again!'
        }
        success { 
            echo 'I will run when success'
        }
        failure { 
            echo 'I will Run when it is failed'
        }
    }
}