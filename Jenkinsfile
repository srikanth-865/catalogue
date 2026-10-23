pipeline{
      agent {
        node {
            label 'ROBOSHOP' //we gave in jenkins UI for to match label for this name to run this pipeline.
         }
      }
        environment{
            def appversion = ""  //def variable globally to access wvwerystage
            acc_id = "349627593718"
            project = "roboshop"
            component = "catalogue"
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
        // this command gives us coverage report and test cases report, sonarqube access this to check quality gate
        stage('Unit tests') {
            steps {
                script {
                    sh """
                        npm test
                    """
                } 
            }
        }

        stage('SonarQube Analysis') {
            steps {
                // 'My SonarQube Server' must match the name configured in Jenkins System Settings
                withSonarQubeEnv('sonar-server') {
                    sh "${tool 'sonar-8'}/bin/sonar-scanner"
                }
            }
        } 
        stage('SonarQube Quality Gate') {
            steps {
                timeout(time: 10, unit: 'MINUTES') {
                    script {
                        def qg = waitForQualityGate() // Pauses pipeline
                        if (qg.status != 'OK') {
                            error "Pipeline aborted: ${qg.status}"
                        }
                    }
                }
            }

        }
       stage("docker build"){
        steps{
            script{ 
                // in this block we get aws authentication Jenkins uses the AWS credentials stored under aws-creds to authenticate with AWS for the enclosed commands.
                withAWS(credentials: 'aws-creds', region: 'us-east-1') {
                sh """

                     aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin ${acc_id}.dkr.ecr.us-east-1.amazonaws.com
                    docker build -t ${acc_id}.dkr.ecr.us-east-1.amazonaws.com/${project}/${component}:${appversion} .
                    docker push ${acc_id}.dkr.ecr.us-east-1.amazonaws.com/${project}/${component}:${appversion}   
                """
              }
          }
        } 
        }
      }
  post{ 
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