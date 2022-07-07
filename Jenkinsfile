stages {
        stage('Test') {
            steps {
                sh """
                    npm install
                    npm test
                    npm run lint
                """
            }
        }
        stage('SonarQube Analysis') {
            when { branch 'master' }
            steps {
                script {
                    scannerHome = tool 'JDSonarQube';
                }
                withSonarQubeEnv('JDSonarQube') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }
        stage('Veracode SCA') {
            when { branch 'master' }
            steps { sh 'curl -sSL https://download.sourceclear.com/ci.sh | sh' }
        }
        stage('Dev - Build and Deploy') {
            agent { label 'devl' }
            when {
                beforeAgent true
                expression { devApproved == true }
            }
            steps {
                samBuild('edl-current-state-deadletter-handler.yaml')
                samDeploy('edl-current-state-deadletter-handler', 'devl')
            }
            post {
                always {
                    cleanWs()
                }
            }
        }
}
