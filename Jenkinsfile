pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                withMaven( maven: 'mvn3.9.1' ) {
                    sh 'mvn clean package'
                }
            }
        } 
        stage('RLTest') {
            steps {
                sh '/bin/RLSecure/rl-secure scan target/struts2-showcase.war -s /bin/RLSecure WebApps/struts2-showcase@v2.5.28_$BUILD_NUMBER --keep-reference'
                sh '/bin/RLSecure/rl-secure report -s /bin/RLSecure -p WebApps/struts2-showcase@v2.5.28_$BUILD_NUMBER --format cyclonedx,rl-html,rl-json --output-path RLreports/$BUILD_NUMBER'
                sh '/bin/RLSecure/rl-secure status -s /bin/RLSecure -p WebApps/struts2-showcase@v2.5.28_$BUILD_NUMBER --return-status --no-color'
            }
        }
    }
    post {
        always {
            sh 'mkdir -p RLreports/latest && cp RLreports/$BUILD_NUMBER/*.json RLreports/latest'
            publishHTML([reportDir: 'RLreports/$BUILD_NUMBER/rl-html', reportFiles: 'sdlc.html', reportName: 'ReversingLabs Report', allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportTitles: '', useWrapperFileDirectly: true])
            archiveArtifacts artifacts: 'RLreports/latest/*.json', onlyIfSuccessful: false
        }
    }    
}
