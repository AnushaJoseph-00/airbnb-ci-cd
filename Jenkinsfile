pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'
        ECR_REPO = '342480083590.dkr.ecr.us-east-1.amazonaws.com/airbnb-clone'
        IMAGE_TAG = "${BUILD_NUMBER}"
        ECS_CLUSTER = 'airbnb-clone-cluster2'
        ECS_SERVICE = 'airbnbtaskdef1-service-rsm9g3mk'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    credentialsId: 'github-token',
                    url: 'https://github.com/AnushaJoseph-00/airbnb-clone-ci-cd.git'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    script {
                        def scannerHome = tool 'SonarQube'
                        sh """
                            ${scannerHome}/bin/sonar-scanner \
                            -Dsonar.projectKey=airbnb-clone \
                            -Dsonar.sources=. \
                            -Dsonar.exclusions=node_modules/**,.next/**,public/**,Dockerfile,Jenkinsfile,**/*.env
                        """
                    }
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Docker Build & Tag') {
            steps {
                withCredentials([
                    string(credentialsId: 'DATABASE_URL', variable: 'DATABASE_URL'),
                    string(credentialsId: 'GITHUB_CLIENT_ID', variable: 'GITHUB_CLIENT_ID'),
                    string(credentialsId: 'GITHUB_CLIENT_SECRET', variable: 'GITHUB_CLIENT_SECRET'),
                    string(credentialsId: 'GOOGLE_CLIENT_ID', variable: 'GOOGLE_CLIENT_ID'),
                    string(credentialsId: 'GOOGLE_CLIENT_SECRET', variable: 'GOOGLE_CLIENT_SECRET'),
                    string(credentialsId: 'NEXTAUTH_SECRET', variable: 'NEXTAUTH_SECRET'),
                    string(credentialsId: 'EDGE_STORE_ACCESS_KEY', variable: 'EDGE_STORE_ACCESS_KEY'),
                    string(credentialsId: 'EDGE_STORE_SECRET_KEY', variable: 'EDGE_STORE_SECRET_KEY'),
                    string(credentialsId: 'NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY', variable: 'NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY'),
                    string(credentialsId: 'STRIPE_SECRET_KEY', variable: 'STRIPE_SECRET_KEY'),
                    string(credentialsId: 'STRIPE_WEBHOOK_SECRET', variable: 'STRIPE_WEBHOOK_SECRET')
                ]) {
                    sh """
                        docker build \
                        --build-arg DATABASE_URL=\${DATABASE_URL} \
                        --build-arg GITHUB_CLIENT_ID=\${GITHUB_CLIENT_ID} \
                        --build-arg GITHUB_CLIENT_SECRET=\${GITHUB_CLIENT_SECRET} \
                        --build-arg GOOGLE_CLIENT_ID=\${GOOGLE_CLIENT_ID} \
                        --build-arg GOOGLE_CLIENT_SECRET=\${GOOGLE_CLIENT_SECRET} \
                        --build-arg NEXTAUTH_SECRET=\${NEXTAUTH_SECRET} \
                        --build-arg NEXTAUTH_URL='http://airbnb-clone-alb-1413831056.us-east-1.elb.amazonaws.com' \
                        --build-arg EDGE_STORE_ACCESS_KEY=\${EDGE_STORE_ACCESS_KEY} \
                        --build-arg EDGE_STORE_SECRET_KEY=\${EDGE_STORE_SECRET_KEY} \
                        --build-arg NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY=\${NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY} \
                        --build-arg STRIPE_SECRET_KEY=\${STRIPE_SECRET_KEY} \
                        --build-arg STRIPE_WEBHOOK_SECRET=\${STRIPE_WEBHOOK_SECRET} \
                        -t airbnb-clone:${IMAGE_TAG} .

                        docker tag airbnb-clone:${IMAGE_TAG} ${ECR_REPO}:${IMAGE_TAG}
                        docker tag airbnb-clone:${IMAGE_TAG} ${ECR_REPO}:latest
                    """
                }
            }
        }

        stage('Push to ECR') {
            steps {
                withCredentials([
                    [
                        $class: 'AmazonWebServicesCredentialsBinding',
                        credentialsId: 'aws-creds',
                        accessKeyVariable: 'AWS_ACCESS_KEY_ID',
                        secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
                    ]
                ]) {
                    sh """
                        aws ecr get-login-password --region ${AWS_REGION} | \
                        docker login --username AWS --password-stdin ${ECR_REPO}
                        docker push ${ECR_REPO}:${IMAGE_TAG}
                        docker push ${ECR_REPO}:latest
                    """
                }
            }
        }

        stage('Deploy to ECS') {
            steps {
                withCredentials([
                    [
                        $class: 'AmazonWebServicesCredentialsBinding',
                        credentialsId: 'aws-creds',
                        accessKeyVariable: 'AWS_ACCESS_KEY_ID',
                        secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
                    ]
                ]) {
                    sh """
                        aws ecs update-service \
                            --cluster ${ECS_CLUSTER} \
                            --service ${ECS_SERVICE} \
                            --force-new-deployment \
                            --region ${AWS_REGION}
                    """
                }
            }
        }
    }

    post {
        success {
            echo '✅ Airbnb Clone deployed successfully!'
        }
        failure {
            echo '❌ Pipeline failed. Check logs.'
        }
    }
}
