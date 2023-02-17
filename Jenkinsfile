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
        
        stage ('Check-Git-Secreats') {
            steps {
                sshagent(['ZAP-SSH']) {
                    sh 'ssh -o  StrictHostKeyChecking=no ubuntu@13.234.34.104 "docker run gesellix/trufflehog --json https://github.com/AfridBashaM/webapp.git > trufflehog"'
                    
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
        stage('SAST'){
            
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
                deploy adapters: [tomcat9(credentialsId: '1223df4e-4823-4a39-ad2e-36f53efa10ac', path: '', url: 'http://52.66.142.113:8080/')], contextPath: null, war: '**/*.war'
             }
         }
        stage ('DAST') {
            steps {
                sshagent(['ZAP-SSH']) {
                    sh 'ssh -o  StrictHostKeyChecking=no ubuntu@13.234.34.104 "docker run -t owasp/zap2docker-stable zap-baseline.py -t http://52.66.142.113:8080/webapp/"'
                }
            }
        }
    }
        
}
