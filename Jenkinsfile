pipeline {
      agent any

      tools {nodejs "NodeJS"}


      parameters{
          string(name: 'SPEC', defaultValue:"cypress/integration/example.js", description: "Enter the cypress script path that you want to execute")
          choice(name: 'BROWSER', choices:['electron', 'chrome', 'edge', 'firefox'], description: "Select the browser to be used in your cypress tests")
          booleanParam(name: 'skip_automated', defaultValue: true, description: 'Set to true to skip the automated')
          booleanParam(name: 'skip_sonar', defaultValue: true, description: 'Set to true to skip the sonar')
          booleanParam(name: 'skip_jmeter', defaultValue: true, description: 'Set to true to skip the jmeter')
      }

    stages {
        stage('Run automated tests'){
            when { expression { params.skip_automated != true } }
            steps {
                sh 'npm prune'
                sh 'npm cache clean --force'
                sh 'npm i'
                sh 'npm install --save-dev mochawesome mochawesome-merge mochawesome-report-generator'
                sh 'npx cypress run --config baseUrl="https://34.18.35.125" --browser ${BROWSER} --spec ${SPEC}'
            }
            post {
                success {
                    publishHTML (
                        target : [
                            allowMissing: false,
                            alwaysLinkToLastBuild: true,
                            keepAll: true,
                            reportDir: 'mochawesome-report',
                            reportFiles: 'mochawesome.html',
                            reportName: 'My Reports',
                            reportTitles: 'The Report'])

                }
            }
        }
        stage('SonarQube analysis') {
           when { expression { params.skip_sonar != true } }
            steps {
                    script {
                            scannerHome = tool 'SonarQube';
                        }
                    withSonarQubeEnv('SonarQube') {
                    sh "${scannerHome}/bin/sonar-scanner"
                    }
            }
           
        }
        stage('JMeter Test') {           
           when { expression { params.skip_jmeter != true } } 
            steps {
                script {
                    // Path to the JMeter installation directory
                    def jmeterHome = '/usr/share/jmeter'

                    // Path to the JMeter test script
                    def jmeterScript = './jenkins_io.jmx'

                    // Execute JMeter test
                    sh "${jmeterHome}/bin/jmeter -n -t ${jmeterScript} -l result.jtl"
                }
            }
            post {
                always {
                    // Archive JTL result file
                    archiveArtifacts 'result.jtl'
                }
                success {
                    // Publish JMeter report using Performance plugin
                    perfReport filterRegex:'', sourceDataFiles: 'result.jtl'
                }
            }
           
        }
        stage('Perform manual testing...'){
            steps {
                timeout(activity: true, time: 2, unit:'DAYS') {
                    input 'Proceed to production?'
                }
           }
        }

    }
}
