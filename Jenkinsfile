pipeline {    
    agent any
    stages {
        stage('Get git code') {
            steps {
                echo '----------------------Get-git-code------------------'
                checkout scm
            }
        }

        stage('Deploy') {
            steps {
                echo '-----------------------Deploy------------------------'
                ansiblePlaybook(
                    credentialsId: 'ansible_admin', 
                    vaultCredentialsId: 'secret_password',
                    sudoUser: 'administrator',
                    inventory: 'inventory', 
                    become: true,
                    playbook: 'Playbook.yml')
            }
        }
        
        stage('Run test nginx') {
            steps {
                echo '--------------------nginx test--------------------'
                sh '''
                    nginx_IP=$(docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' nginx)
                    answer_code=$(curl -I $nginx_IP:80 2>/dev/null | head -n1 | awk '{print $2}')
                    if (( answer_code == 200 )); 
                        then 
                            echo "SUCCESS";
                        else
                            echo "FAILURE!";
                    fi
                '''
            }               
        }
    }       
}