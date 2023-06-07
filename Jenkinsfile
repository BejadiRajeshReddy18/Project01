pipeline {
    agent any
    tools{
        jdk 'jdk11'
        maven 'maven3'
    }
    environment{
        SCANNER_HOME= tool 'sonar-scanner'
    }

    stages {
        stage('git-checkout') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/jaiswaladi246/Devops-CICD.git'
            }
        }

        stage('Code-Compile') {
            steps {
               sh "mvn clean compile"
            }
        }
        
// 		stage('OWASP Dependency Check') {
//             steps {
//               dependencyCheck additionalArguments: '', odcInstallation: 'DP-check'
//                     dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
//             }
//         }


        stage('Sonar Analysis') {
            steps {
               withSonarQubeEnv('sonar-server'){
                   sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Devops-CICD \
                   -Dsonar.java.binaries=. \
                   -Dsonar.projectKey=Devops-CICD '''
               }
            }
        }

		 stage('trivy') {
            steps {
               sh " trivy fs  --security-checks vuln,config /var/lib/jenkins/workspace/project101 "
            }
        }

        stage('Code-Build') {
            steps {
               sh "mvn clean install"
            }
        }
        
        stage('Docker Build') {
            steps {
               withDockerRegistry(credentialsId: '4b244bb6-e5aa-48b9-8544-bfd016365d42', url: 'https://index.docker.io/v1/') {
                     sh " docker build -t devopscicd ."
                   }
                 }
               }

        stage('Docker Push') {
            steps {
              script{
                  withDockerRegistry(credentialsId: '4b244bb6-e5aa-48b9-8544-bfd016365d42') {
                    sh "docker tag  devopscicd rajeshreddy0/devopscicd:latest "
                    sh "docker push  rajeshreddy0/devopscicd:latest "
                 }
              }
            }
        }
        
        
// 		stage('Deploy to Tomcat') {
//             steps {
//               sh "  cp /home/ubuntu/myagent/_work/5/s/target/petclinic.war /opt/apache-tomcat-9.0.65/webapps/ "
//             }
//         }
        
    }
}
