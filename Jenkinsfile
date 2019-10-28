#!groovy
// Check properties
properties([disableConcurrentBuilds()])

pipeline {
        agent {
        label 'master'
        }
        
    environment {
                PRODUCTION_NETWORK_IP = sh(script: "cat /var/lib/jenkins/production_local_ip", , returnStdout: true).trim()
                DOCKER_HOST="ssh://jenkins@app-server"
    }
    
    stages {
        stage('Preparation') {
            steps {
                    git 'https://github.com/microservices-demo/front-end.git'
            }
        }
        
        stage('Build') {
                
                steps {
                    sh 'docker build --no-cache -t front-end .' 
                }
        }
        
       stage('Deploy') {
            environment {
                CHECK_CONTAINER = sh(script: "ssh -oStrictHostKeyChecking=no jenkins@app-server /tmp/check-front-end.sh", , returnStdout: true).trim()
                
            }
                
            steps {
                
                timeout(time:5, unit:'DAYS') {
                    input message:'Approve deployment?', submitter: 'it-ops'
                }
                
                script {
                    
                    
                    if ("${CHECK_CONTAINER}" == 'front-end') {
                        sh "docker stop front-end"
                    } else {
                        sh "docker run -d --restart always --name front-end --network socks -p 80:8079 front-end "
                    }
                }
                
                sh 'docker start front-end' 
                    
            }
        }
        
        stage('Archive') {
                steps {
                    archiveArtifacts artifacts: '**/*', fingerprint: true
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
