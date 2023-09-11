pipeline {
    agent any

    environment {
        APP_NAME        = "vuln-node-proj"
        APP_VERSION     = "1"
        DEFAULT_BRANCH  = 'master'
        USER_ID         = sh (returnStdout: true, script: 'echo \$(git show -s --pretty=%an)').trim()
    }

    stages {
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        stage('Install CycloneDX') {
            steps {
                sh 'npm install --global @cyclonedx/cyclonedx-npm'
            }
        }

        stage ('Generate BOM - Dependency track') {
          steps {
            sh 'cyclonedx-npm --output-file bom.json'
          }
        }

        stage('Load Dependency Track') {
            steps {
                //ingest results to dependency track platform
                withCredentials([string(credentialsId: 'dependency-track-api-key-global', variable: 'API_KEY')])
                {
                  dependencyTrackPublisher artifact:'bom.json', projectName:"${env.APP_NAME}", projectVersion:"${env.APP_VERSION}", synchronous:true, dependencyTrackApiKey:"${API_KEY}"
                 }
            }
        }

    }
}
