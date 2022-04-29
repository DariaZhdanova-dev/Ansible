pipeline {    
    agent any
    stages {
        stage('Get git code') {
            steps {
                checkout scm
            }
        }

        stage('Deploy') {
            steps {

                ansiblePlaybook(
                    credentialsId: 'ansible', 
                    vaultCredentialsId: 'secret_pass',
                    sudoUser: 'admin',
                    sudo: true, 
                    inventory: 'inventory', 
                    playbook: 'Playbook.yml')
            }
        }
        
        stage('Run test for nginx') {
            steps {
                sh '''
                    nginx_IP=$(docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' nginx)
                    answer_code=$(curl -I $nginx_IP:80 2>/dev/null | head -n1 | awk '{print $2}')
                    if (( answer_code == 200 )); 
                        then 
                            echo "SUCCESS";
                        else
                            echo "FAILURE. Server return $answer_code";
                    fi
                '''
            }               
        }
    }       
}