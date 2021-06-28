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
				
				sh("snowsql --dbname ${database} --schemaname ${schema} --config +"${snowsql_config}"+ -q 'select * from test1.Agent' --connection sf_conn  2>&1")
		        FILES_CHANGED= sh(script: "set +x && git log -m -1 --name-only --diff-filter=ACM --pretty='format:' `git rev-parse HEAD` | sort -u | grep -i '.sql\$' > ${WORKSPACE}/committed_files.txt")
                sh("set +x && echo 'DEPLOYBEGIN' && echo '******Deploying from release/${env} Branch to ${app_name} ${env} Environment******'")
                sh('''
   set +x     
   echo "" > status
   git show --pretty=fuller ${GIT_COMMIT} | head -n 10 | tr -d '[:blank:]' | sed 's/commit/CommitID:/' | sed 's/</{/' | sed 's/>/}/' | sed 's/(//' | sed 's/)//' > git_details.txt     
   COMMIT_ONELINER=`sed s/"'"//g git_details.txt | xargs`
   while read -r file || [[ -n "$file" ]]
    do
      if [[ ! -z "$file" ]]; then
       statement_type=`echo "$file" |  cut -d / -f 5`
       if [ ${statement_type} = "ddl" ]; then
        database_name=`echo "$file" |  cut -d / -f 4`
        database="${database_name}_'''+"${env}"+'''"
        schema=`echo "$file" |  cut -d / -f 6`
        exec_type=`echo "$file" |  cut -d / -f 7 | tr '[:lower:]' '[:upper:]'`
        echo "$(date): Database: ${database}"
        echo "$(date): Schema: ${schema}"
        echo "$(date): File_path: ${file}"
        echo "$(date): Executing sql file $(cat ${file})."
        echo "$(date): Proceeding to create ${exec_type} in ${database} database and ${schema} schema.! " > ${WORKSPACE}/logs/${app_name}_${BUILD_NUMBER}.log
        echo "$(date): Proceeding to create ${exec_type} in ${database} database and ${schema} schema.! " >> snow_sql_output.log
        exec_sql=`snowsql --dbname ${database} --schemaname ${schema} --config '''+"${snowsql_config}"+''' --filename ${file} --connection sf_conn  2>&1`
        echo "$(date): Snowsql execution ${exec_sql}"
        echo "${exec_sql}" >> snow_sql_output.log
       elif [ ${statement_type} = "release" ]; then
        echo "$(date): File_path: ${file}"
        echo "$(date): Executing release sql file $(cat ${file})."
        echo "$(date): Proceeding to create release query.! " > ${WORKSPACE}/logs/${app_name}_${BUILD_NUMBER}.log
        echo "$(date): Proceeding to create release query.!" >> snow_sql_output.log
        exec_sql=`snowsql --config '''+"${snowsql_config}"+''' --filename ${file} --connection wmhbo 2>&1`
        echo "$(date): Snowsql execution ${exec_sql}"
        echo "${exec_sql}" >> snow_sql_output.log
       
       else
        echo "$(date): File_path: ${file}"
        echo "$(date): Executing  sql file $(cat ${file})."
        echo "$(date): Proceeding to create .! " > ${WORKSPACE}/logs/${APP_NAME}_${BUILD_NUMBER}.log
        echo "$(date): Proceeding to create .!" >> snow_sql_output.log
        exec_sql=`snowsql --config '''+"${snowsql_config}"+''' --filename ${file} --connection wmhbo 2>&1`
        echo "$(date): Snowsql execution ${exec_sql}"
        echo "${exec_sql}" >> snow_sql_output.log  
       fi

      else
          echo "$(DATE): No user file found for creation!!"
          exit 0
      fi
      echo "***************************************************"
    done < ${WORKSPACE}/committed_files.txt
echo "---------------------------------------"

LOG_READ=$(cat snow_sql_output.log)

if grep -q 'successfully' "snow_sql_output.log"; then
  echo "$(date): All sql statements has been executed successfully"
else
  echo "$(date): One or more statement has failed to execute.!!"
  ##python wmd2c-devops-admin/python/ses/jenkins_notify_users.py --env ${ENV} --branch '''+"${branch_name}"+''' --commitdetails "GitBranch:'''+"${branch_name}"+''' ${COMMIT_ONELINER}" --url '''+"${BUILD_URL}console"+''' --build_no '''+"${BUILD_NUMBER}"+''' --logs "${LOG_READ}" --status 'failure' --buildname '''+"${JOB_NAME}"+''' --email '''+"${DL_EMAIL}"+'''
fi
''')
                sh("set +x && echo 'STAGEDONE'")

		    }
		    }
       		}
    }   
         
        }      
    
