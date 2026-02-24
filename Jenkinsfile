 pipeline {
    agent any

    environment {
        DOTNET_CLI_TELEMETRY_OPTOUT = '1'
        SONAR_HOST_URL = 'http://localhost:9000'
        SONAR_TOKEN = 'your-sonar-token'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Restore') {
            steps {
                bat 'dotnet restore'
            }
        }

        stage('Build') {
            steps {
                bat 'dotnet build --configuration Release'
            }
        }

        stage('Unit Test') {
            steps {
                bat 'dotnet test --configuration Release --collect:"XPlat Code Coverage"'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                bat """
                dotnet sonarscanner begin /k:"MyProject" /d:sonar.host.url="%SONAR_HOST_URL%" /d:sonar.login="%SONAR_TOKEN%"
                dotnet build
                dotnet sonarscanner end /d:sonar.login="%SONAR_TOKEN%"
                """
            }
        }

        stage('Publish') {
            steps {
                bat 'dotnet publish --configuration Release -o publish'
            }
        }

        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: 'publish/**', fingerprint: true
            }
        }

        // Optional CD Stage (comment if CI only)
        /*
        stage('Deploy to Local Folder') {
            steps {
                bat 'xcopy publish C:\\DeployFolder /E /I /Y'
            }
        }
        */
    }

    post {
        success {
            echo 'Pipeline Successful'
        }
        failure {
            echo 'Pipeline Failed'
        }
        always {
            junit '**/TestResults/*.xml'
        }
    }
}
