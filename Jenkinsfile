pipeline {
    agent any

    environment {
        DOCKER_USERNAME = 'mohamedmtolba'
        DOCKER_PASSWORD = 'tolba.1234'
        KUBECONFIG = '/tmp/kube/config' // استخدام المسار الجديد
    }

    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Docker Login') {
            steps {
                script {
                    sh '''
                    echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
                    '''
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh '''
                    docker build -t flask-app . 
                    '''
                }
            }
        }

        stage('Push Docker Image to Registry') {
            steps {
                script {
                    sh '''
                    docker tag flask-app mohamedmtolba/flask-app:latest
                    docker push mohamedmtolba/flask-app:latest
                    '''
                }
            }
        }

        stage('Check Kubernetes Access') { 
            steps {
                script {
                    sh '''
                    minikube kubectl -- get nodes
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh '''
                    minikube kubectl -- apply -f K8S/deployment.yml -n python-flask-app
                    '''
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                script {
                    sh '''
                    minikube kubectl -- get pods -n python-flask-app
                    '''
                }
            }
        }

        stage('Push to Team Repository') {
            steps {
                script {
                    sh '''
                    git remote remove team-repo || true
                    git remote add team-repo https://github.com/AbdullahElmasry/DevOps_engineer_track_project_DEPI.git

                    git config --global user.name "AbdullahElmasry"
                    git config --global user.email "611afnanmohamed@gmail.com"
                    git add .

                    # Check if there are changes to commit
                    if [ "$(git diff --cached --quiet; echo $?)" -ne 0 ]; then
                        git commit -m "Automated commit from Jenkins" || true
                        git push team-repo main
                    else
                        echo "لا توجد تغييرات لارتكابها"
                    fi
                    '''
                }
            }
        }
    }
}

