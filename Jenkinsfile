pipeline{
    
    agent any 
    
    stages {
        
        stage('Git Checkout'){
            
            steps{
                
                script{
                    
                    git branch: 'main', url: 'git@github.com:sami-soliman/CI-Jenkins-Maven-SonarQube-Nexus.git'
                }
            }
        }
        
}
}