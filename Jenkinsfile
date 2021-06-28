def env='dev'
def app_name='snowflake'
def deployment_host='localhost'
def become_user='jenkins'
def repo_name='SnowflakeBoT'
def snowsql_config="/home/ubuntu/.snowsql/config"
def remote_origin='origin'
def branch_name='main'
def code_repo_name='git@github.com:Saraja2607/SnowflakeBoT.git' // SSH Repository of deploy repo //
def admin_repo='git@github.com:Saraja2607/sf_admin.git' //Admin Ansible repo here //

def DL_EMAIL="${o}" // NO ACTION REQUIRED : Email ID to send pipeline notification //
def jenkins_host="${JENKINS_IP_URL}" 
//def git_creds="${GIT_CREDS_ADMIN}"// GitHub credentials- Change ID as per Jenkins server https://jenkins_url/credentials/ //
def GIT_COMMIT // NO ACTION REQUIRED : For Git Commit ID assignment //
def LOG_READ // NO ACTION REQUIRED : For console log assignment //
def COMMIT_ONELINER  // NO ACTION REQUIRED : For Commit info assignment //
def WORKSPACE=/home/ubuntu



// Declarative Pipeline starts here //
pipeline {
    agent any
    // Stages begins here //
    stages {
        
        // Stage for Git Branches Checkout //
        stage('Checkout Git Branch') {
           steps{
		   
             git branch: "${branch_name}",
             //credentialsId: "${git_creds}",
             url: "${code_repo_name}"
                    
             // Create Ansible admin directory and do a checkout from master //
             //dir('wmd2c-devops-admin') {
             checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Saraja2607/SnowflakeBoT.git']]]) 
                   // }
             
            }
        }
            
        // Stage to deploy the files to destination server //
       	stage('Deploy'){
		    steps {
		        script {
				
				sh('''snowsql --dbname 'sf_bot' --schemaname 'test1' --config  '''+"${snowsql_config}"+''' + -q 'select * from Agents' --connection sf_conn  2>&1''')
		        FILES_CHANGED= sh(script: "set +x && git log -m -1 --name-only --diff-filter=ACM --pretty='format:' `git rev-parse HEAD` | sort -u | grep -i '.sql\$' > ${WORKSPACE}/committed_files.txt")
                sh("set +x && echo 'DEPLOYBEGIN' && echo '******Deploying from release/${env} Branch to ${app_name} ${env} Environment******'")
                
                sh("set +x && echo 'STAGEDONE'")

		    }
		    }
       		}
    }   
         
        }      
    
