pipeline {
    agent { 
        docker { 
            image 'nginx' 
            args '-u root:root -p 9889:80'
        }
    }
    stages {
        stage('start nginx') {
            steps {
                sh 'service nginx start'
            }
        }
		stage('copy index.html') {
		    steps {
                git 'https://github.com/Maks-Nikodimov/pr11.git'
                sh 'cp ./index.html /usr/share/nginx/html'
		    }
		}    
		stage ('HTTP_CODE') {
		    steps {
			    sh "echo '--ipv4' >> ~/.curlrc"
                sh 'curl --write-out %{http_code} --silent --output /dev/null http://localhost > ./HTTP_CODE'
                sh 'echo -n "200" > ./200'
                sh 'if [ -z "$(diff -q HTTP_CODE  200)" ] ; then \
                        echo "CHECK OK"; \
                    else \
                        echo "Fail"; exit 1; \
                    fi \
                '
			}
		}
		stage ('MD5') {
		    steps {
                sh "curl -sL http://localhost | md5sum | cut -d ' ' -f1 > ./ONLINE_MD5"
                sh "md5sum /usr/share/nginx/html/index.html | awk '{print \$1}' > ./LOCAL_MD5"
                sh 'if [ -z "$(diff -q ONLINE_MD5  LOCAL_MD5)" ] ; then \
                        echo "CHECK OK"; \
                    else \
                        echo "Fail"; exit 1; \
                    fi \
                '
		    }
        }
    }
	post {  
        failure {  
            telegramSend(message:'Build NGINX failure',chatId:-1001764465334)  
        }  
    }
}
