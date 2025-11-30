pipeline {
    agent any
    
    environment {
        SONAR_SCANNER = tool 'SonarScanner'
        SONAR_SERVER = 'SonarQubeServer'
        ARTIFACTORY_SERVER = 'Artifactory'
    }

    stages {

        stage('Build') {
            steps {
                sh 'mvn clean install -DskipTests'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQubeServer') {
                    sh '''
                    mvn sonar:sonar \
                        -Dsonar.projectKey=java \
                        -Dsonar.projectName=java \
                        -Dsonar.host.url=$SONAR_HOST_URL \
                        -Dsonar.login=$SONAR_AUTH_TOKEN
                    '''
                }
            }
        }

        stage('Upload to Artifactory') {
            steps {
                script {
                    def server = Artifactory.server('Artifactory')

                    def uploadSpec = """{
                        "files": [{
                            "pattern": "target/*.jar",
                            "target": "my-repo-local/"
                        }]
                    }"""

                    server.upload(uploadSpec)
                }
            }
        }
    }
}

