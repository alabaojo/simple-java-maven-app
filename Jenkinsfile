pipeline {
    agent {
        docker {
            image 'maven:3-alpine'
            args '-v /root/.m2:/root/.m2'
        }
    }

    environment {
        IMAGE = readMavenPom().getArtifactId()    //Use Pipeline Utility Steps
        VERSION = readMavenPom().getVersion()
    }
    stages {
        stage('BuildB') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('Build and Publish Image') {
            when {
                branch 'master'    //only run these steps on the master branch
            }
            steps {
                sh '
                    docker build -t ${IMAGE} .
                    docker tag ${IMAGE} ${IMAGE}:${VERSION}
                    docker push ${IMAGE}:${VERSION}
                '
            }
        }
        stage('Deliver') { 
            steps {
                sh './jenkins/scripts/deliver.sh' 
            }
        }
    }
}