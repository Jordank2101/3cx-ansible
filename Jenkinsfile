pipeline {
    agent any
    
    parameters {
        string(name: 'THREECX_IP', defaultValue: '195.201.113.22')
        choice(name: 'SSH_KEY_ID', choices: ['jenkins-3cx-ssh'])
    }
    
    stages {
        stage('Setup') {
            steps {
                sh '''
                sudo apt update || true
                sudo apt install -y ansible openssh-client || true
                which ansible-playbook || echo "Ansible missing!"
                '''
            }
        }
        stage('Deploy 3CX') {
            steps {
                dir('/workspace/3cx-ansible') {
                    withCredentials([sshUserPrivateKey(credentialsId: "${params.SSH_KEY_ID}",
                                                       keyFileVariable: 'SSH_KEY')]) {
                        sh '''
                        ansible-playbook playbook.yml \\
                          --private-key "$SSH_KEY" \\
                          -e threecx_server_ip="$THREECX_IP" \\
                          -e "ansible_ssh_common_args=-o StrictHostKeyChecking=no"
                        '''
                    }
                }
            }
        }
    }
}
