pipeline {
    agent any
    stages {
        stage('Init') {
            steps {
                script {
                    if (env.GIT_BRANCH == 'origin/master') {
                        sh '''
                        docker rmi drpeace/duo-deploy-flask || echo "drpeace/duo-deploy-flask image does not exist"
                        docker rmi drpeace/flask-nginx || echo "drpeace/flask-nginx image does not exist"
                        docker network create project || echo "network already exists"
                        echo "Main:Build successful"
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                        docker rmi drpeace/duo-deploy-flask || echo "drpeace/duo-deploy-flask image does not exist"
                        docker rmi drpeace/flask-nginx || echo "drpeace/flask-nginx image does not exist"
                        docker network create project || echo "network already exists"
                        echo "Dev:Build successful"
                        '''
                    } else {
                        sh '''
                        echo "Init - Unrecognised branch"
                        '''
                    }    
                }   
            }
        }
        stage('Build') {
            steps {
                script {
			        if (env.GIT_BRANCH == 'origin/master') {
                        sh '''
                        echo "Build not required in master"
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                        docker build -t drpeace/duo-deploy-flask -t drpeace/duo-deploy-flask:v${BUILD_NUMBER} .
                        docker build -t drpeace/flask-nginx -t drpeace/flask-nginx:v${BUILD_NUMBER} ./nginx
                        echo "Build successful"
                        '''
                    } else {
                        sh '''
                        echo "Build - Unrecognised branch"
                        '''
                    }
                }
            }
        }
        stage('Push') {
            steps {
                script {
			        if (env.GIT_BRANCH == 'origin/master') {
                        sh '''
                        echo "Push not required in master"
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                        docker push drpeace/duo-deploy-flask
                        docker push drpeace/duo-deploy-flask:v${BUILD_NUMBER}
                        docker push drpeace/flask-nginx
                        docker push drpeace/flask-nginx:v${BUILD_NUMBER}
                        echo "Push successful"
                        '''
                    } else {
                        sh '''
                        echo "Push - Unrecognised branch"
                        '''
                    }
                }    
           }
        }
        stage('Deploy') {
            steps {
                script {
                    if (env.GIT_BRANCH == 'origin/master') {
                        sh '''
                        kubectl apply -f ./kubernetes
                        kubectl rollout restart deployment flask-deployment
                        kubectl rollout restart deployment nginx-deployment
                        echo "Main:Build successful"
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                        kubectl apply -f .
                        echo "Dev:Build successful"
                        '''
                    } else {
                        echo "Deploy - Unrecognised branch"
                    }
                }
            }
        }
        stage('Cleanup') {
            steps {
                script {
                    if (env.GIT_BRANCH == 'origin/master') {
                        sh '''
                        echo "rmi not required in master"
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                        docker system prune -f
                        docker rmi drpeace/duo-deploy-flask:v${BUILD_NUMBER}
                        docker rmi drpeace/flask-nginx:v${BUILD_NUMBER}
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                        echo "Cleanup - Unrecognised branch"
                    }
                } 
            }
        }
    }
}