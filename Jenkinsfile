pipeline {
    agent any
    tools {
        maven 'Maven'
        jdk 'openJDK8'
    }
    stages {
        stage('Maven Build') {
            steps {
                script {
                    mvn = tool(name: 'Maven', type: 'maven') + '/bin/mvn'
                }
                sh "${mvn} clean install"
            }
        }

        stage('Static Code Analysis (SonarQube)') {
            steps {
                echo "====++++  Static Code Analysis (SonarQube) ++++===="
                // Use 'sonarqube' as the hostname to connect to the SonarQube service within Docker Compose
                sh "${mvn} clean test install sonar:sonar -Dsonar.host.url=http://sonarqube:9000"
            }
        }

        stage('Nexus Deployment') {
            steps {
                echo "====++++  Nexus Deployment ++++===="
                // Use 'nexus' as the hostname to connect to the Nexus service within Docker Compose
                sh "${mvn} deploy -Dmaven.test.skip=true -Dmaven.install.skip=true -Dmaven.deploy.skip=true -Dsonar.skip=true -DskipITs=true -Dmaven.repo.local=/var/jenkins_home/.m2/repository -DaltDeploymentRepository=nexus::default::http://nexus:8081/repository/my-repository/"
            }
        }
    }
}
