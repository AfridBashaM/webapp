pipeline{
    
    agent any 
    
    stages {
        
        stage('Git Checkout'){
            
            steps{
                
                script{
                    
                    git branch: 'master', url: 'https://github.com/AfridBashaM/webapp.git'
                }
            }
        }
        
        stage('SCA'){
        
            steps{
                dependencyCheck additionalArguments: '', odcInstallation: 'owasp dependency'
            }
            
        }
        
        stage('UNIT testing'){
            
            steps{
                
                script{
                    
                    sh 'mvn test'
                }
            }
        }
        stage('Integration testing'){
            
            steps{
                
                script{
                    
                    sh 'mvn verify -DskipUnitTests'
                }
            }
        }
        stage('Maven build'){
            
            steps{
                
                script{
                    
                    sh 'mvn clean install'
                }
            }
        }
        stage('Static code analysis'){
            
            steps{
                
                script{
                    
                    withSonarQubeEnv(credentialsId: 'sonar') {
                        
                        sh 'mvn clean package sonar:sonar'
                    }
                 }
                    
             }
         }
         stage('Deploy to tomcat'){
                
            steps{
                sshagent(['tomcat']) {
                    sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@52.66.142.113:/prod/apache-tomcat-9.0.71/webapps/webapp.war'
                }
             }
         }
        stage ('DAST') {
            steps {
                sshagent(['ZAP-SSH']) {
                    sh 'ssh -o  StrictHostKeyChecking=no ubuntu@13.234.34.104 "docker run -t owasp/zap2docker-stable zap-baseline.py -t http://52.66.142.113:8080/webapp/" || true'
                }
            }
        }
    }
        
}
