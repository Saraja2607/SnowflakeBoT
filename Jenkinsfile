pipeline {
    agent any
    // Stages begins here //
    stages {
        
        // Stage for Git Branches Checkout //
        stage('Checkout Git Branch') {
            steps{
                git branch: 'main', 
                    url: 'https://github.com/Saraja2607/SnowflakeBoT.git'
            }
        }
      
      stage('Deploy'){
          steps{
                echo "This is Deployment Environment"
            }          
      }
    }
  posts {
    script {
        steps{
                echo "This is Post script"
            }      
    }
  }
}
