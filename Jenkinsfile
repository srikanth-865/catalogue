pipeline{
      agent {
        node {
            label 'ROBOSHOP' //we gave in jenkins UI for to match label for this name to run this pipeline
        }
      }
        environment{
            def appversion = ""  //def variable globally to access wvwerystage
           }


    stages{
        stage( 'Read Version'){   //stage 1
            steps{
                script{
                    
                      def  packageJson = readJSON file: 'package.json'
                      appversion =  packageJson.version
                      echo "The application version is: ${appversion}"

                }
            }

        }

        stage('installing dependencies'){
            steps{
                script{
                    sh """
                       npm install
                    """
                }
            }
        }
        stage('Testing the variable') { //stage 2
            steps {
                script{
                      echo " application version in local variable: ${appversion} "
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