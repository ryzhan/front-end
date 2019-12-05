#!groovy
pipeline{
    agent any
       
       stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/ryzhan/front-end.git'
            }
        }   
        
        stage('Build front-end') {
            steps {
                
                sh 'docker login -u _json_key -p "$(cat /var/lib/jenkins/credential/if-101-demo1-02c2a2eae285.json)" https://gcr.io'
                sh "docker build -t gcr.io/if-101-demo1/front-end:3.0.0-$BUILD_NUMBER -t gcr.io/if-101-demo1/front-end:latest ."
                sh "docker push gcr.io/if-101-demo1/front-end"
                sh "docker rmi -f gcr.io/if-101-demo1/front-end:latest gcr.io/if-101-demo1/front-end:3.0.0-$BUILD_NUMBER"
                
            }
            
        }
           
        stage('Archive workspace') {
                steps {
                    archiveArtifacts artifacts: '**/*', fingerprint: true
                }
        }
        
        stage('Approve for Deploy') {
        
                steps {
                    timeout(time:5, unit:'DAYS') {
                        input message:'Approve deployment?'
                    }

                }
        }   
           
        stage('Run front-end') {
            
            steps {
                
                sh "git clone https://github.com/ryzhan/ConfDemo3.git"
                dir('./ConfDemo3/ansible'){
                    sh 'ansible-playbook build_microservices.yml --tags "front-end-run" --extra-var "BUILD_NUMBER=$BUILD_NUMBER"'
                }
                
            }
            
        }
   
    }
    
    post {
        always {
            echo 'I have finished'
            echo 'And cleaned workspace'
            deleteDir()
        }
        success {
            echo 'Job succeeeded!'
        }
        unstable {
            echo 'I am unstable :/'
        }
        failure {
            echo 'I failed :('
        }
        changed {
            echo 'Things were different before...'
        }
    }
}
