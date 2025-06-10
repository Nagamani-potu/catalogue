pipeline {
    agent {
        node {
            label 'AGENT-1'
        }
    }

    environment {
        packageVersion = ''
        nexusURL = '172.31.22.176:8081' // ✅ Port included
    }

    options {
        timeout(time: 1, unit: 'HOURS')
        disableConcurrentBuilds()
    }
    parameters {
        // string(name: 'PERSON', defaultValue: 'Mr Jenkins', description: 'Who should I say hello to?')

        // text(name: 'BIOGRAPHY', defaultValue: '', description: 'Enter some information about the person')

        booleanParam(name: 'Deploy', defaultValue: false, description: 'Toggle this value')

        // choice(name: 'CHOICE', choices: ['One', 'Two', 'Three'], description: 'Pick something')

        // password(name: 'PASSWORD', defaultValue: 'SECRET', description: 'Enter a password')
    }
    stages {
        stage('Get the version') {
            steps {
                script {
                    def packageJson = readJSON file: 'package.json'
                    packageVersion = packageJson.version
                    echo "Application version: $packageVersion"
                }
            }
        }

        stage('Install dependencies') {
            steps {
                sh 'npm install'
            }
        }

         stage('Unit tests') {
            steps {
                sh """
                    echo "unit tests will run here"
                """
            }
        }
        stage('Sonar Scan'){
            steps{
                sh """
                    sonar-scanner
                """
            }
        }

        stage('Build') {
            steps {
                sh '''
                    ls -la
                    zip -q -r catalogue.zip ./Jenkinsfile ./node_modules ./package.json ./package-lock.json ./schema ./server.js -x ".git/*" -x "*.zip"
                    ls -ltr
                '''
            }
        }

        stage('Verify Artifact') {
            steps {
                sh 'ls -lh catalogue.zip'
            }
        }

        stage('Publish Artifact') {
            steps {
                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: "${nexusURL}",
                    groupId: 'com.roboshop',
                    version: "${packageVersion}",
                    repository: 'catalogue',
                    credentialsId: 'nexus-auth',
                    artifacts: [[
                        artifactId: 'catalogue',
                        classifier: '',
                        file: 'catalogue.zip',
                        type: 'zip'
                    ]]
                )
            }
        }

        stage('Deploy') {
            steps {
                script {
                        def params = [
                            string(name: 'version', value: "$packageVersion"),
                            string(name: 'environment', value: "dev")
                        ]
                        build job: "catalogue-deploy", wait: true, parameters: params
                    }
            }
        }
    }

    post {
        always {
            echo 'Pipeline completed. Cleaning up workspace...'
            deleteDir()
        }
        failure {
            echo 'Pipeline failed. Please check the logs for details.'
        }
        success {
            echo 'Pipeline succeeded. Artifact published and deployment complete.'
        }
    }
}
