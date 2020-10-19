node {
    checkout scm
    docker.image('jenkins-node:beta').inside("""--entrypoint=''""") {
        //build de app and execute unit test
        stage('Build & Unit test'){
            //this option -DskipITs=true skip integration tests
            sh 'mvn clean verify -DskipITs=true';
            junit '**/target/surefire-reports/TEST-*.xml'
            archiveArtifacts artifacts: 'target/**/*.jar'
            
        }
        // integration tests
        stage ('Integration Test'){
            //this option -Dsurefire.skip=true skip unit tests
            sh 'mvn clean verify -Dsurefire.skip=true';
            junit '**/target/failsafe-reports/TEST-*.xml'
            archiveArtifacts artifacts: 'target/**/*.jar'
            
        }
        stage('Publish'){
            def server = Artifactory.server 'Default Artifactory Server'
            def uploadSpec = """{
                    "files": [
                    {
                        "pattern": "target/hello-0.0.1.war",
                        "target": "example-project/${BUILD_NUMBER}/",
                        "props": "Integration-Tested=Yes;Performance-Tested=No"
                    }
                ]
            }"""
            server.upload spec: uploadSpec, failNoOp: true
        }
    }
}