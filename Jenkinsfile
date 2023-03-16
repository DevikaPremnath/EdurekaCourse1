pipeline {
    agent {label 'slave'}
    stages {
        
        stage('install puppet on slave') {
            steps {
                // Install Puppet
                echo 'Install Puppet'
                sh "wget -N -O 'puppet.deb' https://apt.puppetlabs.com/puppet6-release-bionic.deb"
                sh "chmod 755 puppet.deb"
                sh "sudo dpkg -i puppet.deb"
                sh "sudo apt update"
                sh "sudo apt install -y puppet-agent"
                // Configure Puppet 
                echo 'configure puppet'
                sh "mkdir -p /etc/puppetlabs/puppet"
                sh "if [ -f /etc/puppetlabs/puppet/puppet.conf ]; then sudo rm -f /etc/puppetlabs/puppet.conf; fi"
                sh "echo '[main]\ncertname = node1.local\nserver = puppet' >> ~/puppet.conf"
                sh "sudo mv ~/puppet.conf /etc/puppetlabs/puppet/puppet.conf"
                echo 'start puppet'
                sh "sudo systemctl start puppet"
                sh "sudo systemctl enable puppet"
            }
        }

        stage('Install Docker on slave via Ansible') {
            agent{ label 'master'}
            steps {
                sh "ansible-playbook docker-install.yml -u edureka"
            }
        }

       
        stage('Docker Build and Run') {
            agent{ label 'slave'}
            steps {
                sh "sudo docker rm -f webapp || true"
		sh "sudo docker build -t test ."
                sh "sudo docker run -it -d --name webapp -p 1998:80 test"
            }
            post {
                failure {
                    sh "echo Failure"
					sh "sudo docker rm -f webapp"
                }
            }
		}
	}
}
