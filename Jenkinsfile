currentBuild.displayName = "nodeapp#"+currentBuild.number

pipeline{
    agent any

    environment{
        DOCKER_TAG = getDockerTag()
        ECR_REPO = "502207623589.dkr.ecr.us-east-1.amazonaws.com/terraform-demo"
    }

    stages{
        stage("Git Checkout"){
            steps{
                git 'https://github.com/alexanto222/node-app.git'
            }
        }
        stage("Build docker image"){
            steps{
                sh "docker build . -t terraform-demo:v1"
            }
        }
        stage("Push to ECR"){
            steps{
                 sh "aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin ${ECR_REPO}"
                 sh "docker tag terraform-demo:v1 ${ECR_REPO}:${DOCKER_TAG}"
                 sh "docker push ${ECR_REPO}:${DOCKER_TAG}"
            }
        }
        stage("Deploy to k8s"){
            steps{
                sh "chmod +x changeTag.sh"
                sh "./changeTag.sh ${DOCKER_TAG}"
                sh "kubectl apply -f app-deploy.yaml"
                sh "kubectl apply -f app-service.yaml"
            }
        }
    }
}

def getDockerTag(){
    def tag = sh script: 'git rev-parse HEAD', returnStdout: true
    return tag
}
