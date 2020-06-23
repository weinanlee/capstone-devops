pipeline {
    agent any
    stages {

        stage('Lint HTML') {
            steps {
                sh 'tidy -mq **/*.html'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD']]){
                    sh '''
                        docker build -t weinanli/catnip .
                    '''
                }
            }
        }

        stage('Push Image To Dockerhub') {
            steps {
                withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD']]){
                    sh '''
                        docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD
                        docker push weinanli/catnip
                    '''
                }
            }
        }

        stage('Create conf file cluster') {
            steps {
                withAWS(region:'us-west-2', credentials:'aws-devops') {
                    sh '''
                        aws eks --region us-west-2 update-kubeconfig --name capstone-devops-cluster
                    '''
                }
            }
        }

        stage('Set kubectl context') {
            steps {
                withAWS(region:'us-west-2', credentials:'aws-devops') {
                    sh '''
                        kubectl config use-context arn:aws:eks:us-west-2:918317714877:cluster/capstone-devops-cluster
                    '''
                }
            }
        }


        // stage('Deploy blue container') {
        //     steps {
        //         withAWS(region:'us-west-2', credentials:'aws-devops') {
        //             sh '''
        //                 kubectl apply -f ./blue-controller.json
        //             '''
        //         }
        //     }
        // }

        // stage('Deploy green container') {
        //     steps {
        //         withAWS(region:'us-west-2', credentials:'aws-devops') {
        //             sh '''
        //                 kubectl apply -f ./green-controller.json
        //             '''
        //         }
        //     }
        // }


        // stage('Create service in cluster, redirect to green') {
        //     steps {
        //         withAWS(region:'us-west-2', credentials:'aws-devops') {
        //             sh '''
        //                 kubectl apply -f green-service.json
        //             '''
        //         }
        //     }
        // }

        // stage('Wait user approve') {
        //     steps {
        //         input "Ready to redirect traffic to blue?"
        //     }
        // }

        // stage('Create service in cluster, redirect to blue') {
        //     steps {
        //         withAWS(region:'us-west-2', credentials:'aws-devops') {
        //             sh '''
        //                 kubectl apply -f blue-service.json
        //             '''
        //         }
        //     }
        // } 


    }
}
