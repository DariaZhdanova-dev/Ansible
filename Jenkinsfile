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
                    final hostIp="192.168.1.227:5000"
                    final def (String code) =
                            sh(script: "curl -s -o /dev/null -w '%{response_code}' $hostIp")
                    echo "HTTP response status code: $code"

                    if (code == "200") {
                        echo 'THATS FINE'
                    }
                }   
            }            
        }
    }       
}