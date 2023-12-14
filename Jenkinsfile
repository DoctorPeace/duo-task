pipeline {
    agent any
    stages {
        stage('Init') {
            steps {
                script {
			        if (env.GIT_BRANCH == 'origin/master') {
                        sh '''
                        kubectl create namespace production || echo "namespace production already exists"
                        echo "main:Init successful"
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                        kubectl create namespace development || echo "namespace development already exists"
                        echo "dev:Init successful"
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
                        docker build -t drpeace/duo-deploy-flask:latest -t drpeace/duo-deploy-flask:prod-v${BUILD_NUMBER} .
                        echo "main:Build successful"
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                        docker build -t drpeace/duo-deploy-flask:latest -t drpeace/duo-deploy-flask:dev-v${BUILD_NUMBER} .
                        echo "dev:Build successful"
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
                        docker push drpeace/duo-deploy-flask
                        docker push drpeace/duo-deploy-flask:prod-v${BUILD_NUMBER}
                        echo "dev:Push successful"
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                        docker push drpeace/duo-deploy-flask
                        docker push drpeace/duo-deploy-flask:dev-v${BUILD_NUMBER}
                        echo "dev:Push successful"
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
                        kubectl apply -f ./kubernetes --namespace production
                        kubectl set image deployment/flask-deployment flask-container=drpeace/duo-deploy-flask:prod-v${BUILD_NUMBER} -n production
                        echo "main:Deploy successful"
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                        kubectl apply -f ./kubernetes --namespace development
                        kubectl set image deployment/flask-deployment flask-container=drpeace/duo-deploy-flask:dev-v${BUILD_NUMBER} -n development
                        echo "dev:Deploy successful"
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
                        docker system prune -f
                        echo "main:Cleanup successful"
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                        docker system prune -f
                        echo "dev:Cleanup successful"
                        '''
                    } else {
                        echo "Cleanup - Unrecognised branch"
                    }
                } 
            }
        }
    }
}