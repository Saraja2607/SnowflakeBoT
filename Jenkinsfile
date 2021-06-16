pipeline {
    agent any
    // Stages begins here //
    stages {
        
        // Stage for Git Branches Checkout //
        stage('Checkout Git Branch') {
            steps{
                echo "This is Checkout Git Branch"
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
