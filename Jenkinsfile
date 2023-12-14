pipeline {
    agent any
    stages {
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
                        kubectl apply -f ./kubernetes --namespace production
                        kubectl rollout restart deployment flask-deployment --namespace production
                        kubectl rollout restart deployment nginx-deployment --namespace production
                        echo "Main:Build successful"
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                        kubectl apply -f . --namespace development
                        kubectl apply -f ./kubernetes --namespace development
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
                        '''
                    } else {
                        echo "Cleanup - Unrecognised branch"
                    }
                } 
            }
        }
    }
}