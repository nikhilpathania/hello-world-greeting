pipeline {
    agent {
        docker { image 'jenkins-node:beta' }
    }
    stages {
        //copi source from repo
        stage('Poll'){
            steps{
                scm checkout
            }
        }
        //build de app and execute unit test
        stage('Build & Unit test'){
            //this option -DskipITs=true skip integration tests
            steps{
                sh 'mvn clean verify -DskipITs=true';
                junit '**/target/surefire-reports/TEST-*.xml'
                archive 'target/*.jar'
            }
            
        }
        // integration tests
        stage ('Integration Test'){
            //this option -Dsurefire.skip=true skip unit tests
            steps{
                sh 'mvn clean verify -Dsurefire.skip=true';
                junit '**/target/failsafe-reports/TEST-*.xml'
                archive 'target/*.jar'
            }
            
        }
        stage('Publish'){
                def server = Artifactory.server 'Default Artifactory Server'
                def uploadSpec = """{
                     "files": [
                        {
                            "pattern": "target/hello-0.0.1.war",
                            "target": "helloworld-greeting-project/${BUILD_NUMBER}/",
                            "props": "Integration-Tested=Yes;Performance-Tested=No"
                        }
                    ]
                }"""
            steps{
                server.upload(uploadSpec)
            }
        }
    }
}