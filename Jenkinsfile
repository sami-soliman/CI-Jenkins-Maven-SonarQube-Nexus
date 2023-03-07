pipeline{
    
    agent any 
    
    stages {
        
        stage('Git Checkout'){
            
            steps{
                
                script{
                    
                    git branch: 'main', url: 'https://github.com/sami-soliman/CI-Jenkins-Maven-SonarQube-Nexus.git'
                }
            }
        }
        stage('Unit Test'){
            
            steps{
                
                sh '''#!/bin/bash
                 echo "performing uint testing"
                 mvn test
                '''
            }
        }
        
}
}