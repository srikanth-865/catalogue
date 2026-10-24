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

        // stage('SonarQube Analysis') {
        //     steps {
        //         // 'My SonarQube Server' must match the name configured in Jenkins System Settings
        //         withSonarQubeEnv('sonar-server') {
        //             sh "${tool 'sonar-8'}/bin/sonar-scanner"
        //         }
        //     }
        // } 
        // stage('SonarQube Quality Gate') {
        //     steps {
        //         timeout(time: 10, unit: 'MINUTES') {
        //             script {
        //                 def qg = waitForQualityGate() // Pauses pipeline
        //                 if (qg.status != 'OK') {
        //                     error "Pipeline aborted: ${qg.status}"
        //                 }
        //             }
        //         }
        //     }

        // }
         stage('Check Dependabot Alerts') {
            steps {
                withCredentials([string(credentialsId: 'github-token', variable: 'GH_TOKEN')]) {
                    sh '''
                        set -e

                        REPO="srikanth-865/catalogue"

                        curl -s -L \
                        -H "Accept: application/vnd.github+json" \
                        -H "Authorization: Bearer ${GH_TOKEN}" \
                        -H "X-GitHub-Api-Version: 2026-03-10" \
                        "https://api.github.com/repos/${REPO}/dependabot/alerts?state=open" \
                        -o alerts.json

                        echo "---- Open Dependabot Alerts ----"
                        jq -r '.[] | "\\(.number)\\t\\(.security_vulnerability.severity)\\t\\(.dependency.package.name)\\t\\(.security_advisory.ghsa_id)"' alerts.json

                        HIGH_CRITICAL_COUNT=$(jq '[.[] | select(.security_vulnerability.severity == "high" or .security_vulnerability.severity == "critical")] | length' alerts.json)

                        echo "High/Critical alert count: ${HIGH_CRITICAL_COUNT}"

                        if [ "$HIGH_CRITICAL_COUNT" -gt 0 ]; then
                            echo "❌ Found ${HIGH_CRITICAL_COUNT} High/Critical severity dependency alert(s). Failing build."
                            exit 1
                        else
                            echo "✅ No High/Critical dependency alerts found."
                        fi
                    '''
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