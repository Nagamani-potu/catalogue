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
