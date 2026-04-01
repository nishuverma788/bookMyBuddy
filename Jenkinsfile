pipeline{
    agent any
    environment{
        MONGO="mongodb+srv://chirag:chirag@cluster0.xxq7wug.mongodb.net/doctor_app?retryWrites=true&w=majority"
        JWT="a1cd158e2b34d47af6d0b446f59a326849f0dd8eb7ac51244f4ab4c1cb99552b9f856a8fb488f20fdbf0d489fc93b4d94fb71148a1bd24e2386a6c71e4d8a3cd"
    }

    stages{
stage('Debug K8s Access') {
    steps {
        sh 'kubectl get nodes'
    }
}
        stage('Pre-Cleanup') {
	    steps {
        	sh 'docker system prune -f'
    }
}
	stage('Clone Git'){
            steps{
                git 'https://github.com/nishuverma788/bookMyBuddy.git'
            }
        }
        stage('Testing'){
            steps{
                dir('api'){
                    sh "npm install"
                    sh "npm test"
                }
            }
        }
        stage('Build Frontend Image') {
            steps {
                
                sh 'docker build -t bookmybuddy_frontend-image .'
            }
        }
         stage('Build Backend Image') {
            steps {
                sh 'docker build -t bookmybuddy_backend-image ./api'
            }
        }
stage('Push Images to DockerHub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'DockerhubCred') {

                        sh 'docker tag bookmybuddy_frontend-image nishu839/bookmybuddy_frontend-image:latest'
                        sh 'docker push nishu839/bookmybuddy_frontend-image:latest'

                        sh 'docker tag bookmybuddy_backend-image nishu839/bookmybuddy_backend-image:latest'
                        sh 'docker push nishu839/bookmybuddy_backend-image:latest'
                    }
                }
            }
        }       

 stage('Ansible Deployment') {
            steps {
                script { 
                    sh 'ansible-playbook -i inventory-k8 playbook-k8.yml'
                }
            }
        }
    }
}

