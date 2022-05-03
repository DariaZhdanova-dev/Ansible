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
        
        stage('Run test nginx') {
            steps {
                script{
                    final hostIp="192.168.0.103"
                    final def (String code) =
                            sh(script: "seq 1 3 | xargs -I % -P 3 curl -s -I $hostIp:5000 | grep HTTP/ | awk {'print ${2}'}")
                    echo "HTTP response status code: $code"
                }   
            }            
        }
    }       
}