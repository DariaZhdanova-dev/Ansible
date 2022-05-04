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
                echo '-----------------------Install roles------------------------'
                sh '''
                    ansible-galaxy install -r requirements.yml --force
                '''
                echo '-----------------------Deploy------------------------'
                ansiblePlaybook(
                    credentialsId: 'ansible_admin', 
                    vaultCredentialsId: 'secret_password',
                    inventory: 'inventory', 
                    become: true,
                    playbook: 'Playbook.yml', 
                    extras: "-vvv"
                    )
            }
        }
        
        stage('Test nginx') {
            steps {
                script{
                    final hostIp="192.168.0.103"
                    def code = sh([script: "curl -s -o /dev/null -w '%{http_code}' $hostIp:5000", returnStdout: true ]).trim()
                    if (code != "200"){
                        error("Can't even proceed 1 request!")
                    }
                    def hostsOnline = sh([script: "seq 1 3 | xargs -I % -P 3 curl -s $hostIp:5000 | grep hostname | awk {'print \$2'}", returnStdout: true ]).trim()
                    if ( !(hostsOnline.contains("server_3") && hostsOnline.contains("server_1") && hostsOnline.contains("server_2"))){
                         error("Not all servers are online!")
                    }
                    echo '------------------------TESTS PASSED--------------------------'
                }   
            }            
        }
    }       
}