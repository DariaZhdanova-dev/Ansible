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
                echo '-----------------------Deploy------------------------'
                ansiblePlaybook(
                    credentialsId: 'ansible_admin', 
                    vaultCredentialsId: 'secret_password',
                    inventory: 'inventory', 
                    become: true,
                    playbook: 'Playbook.yml')
            }
        }
        
        stage('Test nginx') {
            steps {
                script{
                    final hostIp="192.168.0.103"
                    def code = sh([script: "seq 1 3 | xargs -I % -P 3 curl -s -I $hostIp:5000 | grep HTTP/ | awk {'print \$2'} | grep 200 | wc -l", returnStdout: true ]).trim()
                    if (code != 3){
                        error("Not all servers are online!")
                    }
                }   
            }            
        }
    }       
}