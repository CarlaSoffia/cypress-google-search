pipeline {
      agent any

      tools {nodejs "NodeJS"}


      parameters{
          string(name: 'SPEC', defaultValue:"cypress/integration/example.js", description: "Enter the cypress script path that you want to execute")
          choice(name: 'BROWSER', choices:['electron', 'chrome', 'edge', 'firefox'], description: "Select the browser to be used in your cypress tests")
      }

    stages {
        stage('Run automated tests'){
            steps {
                sh 'npm prune'
                sh 'npm cache clean --force'
                sh 'npm i'
                sh 'npx cypress run --config baseUrl="https://www.google.com" --browser ${BROWSER} --spec ${SPEC}'
            }
        }
        stage('SonarQube analysis') {
            steps {
                echo "SonarQube testing..."
           }
        }
        stage('JMeter Test') {
            steps {
                echo "JMeter testing..."
           }
        }
        stage('Perform manual testing...'){
            steps {
                timeout(activity: true, time: 5) {
                    input 'Proceed to production?'
                }
           }
        }

    }
}
