pipeline{
    agent any

    environment{
        DOCKER_TAG = getDockerTag()
    }

    stages{
        stage("Build docker image"){
            steps{
                sh "docker build . -t alexanto/k8s:${DOCKER_TAG}"
            }
        }
        stage("Push to DockerHub"){
            steps{
                 sh "docker login -u alexanto -p alex_222"
                 sh "docker push alexanto/k8s:${DOCKER_TAG}"
            }
        }
        stage("Deploy to k8s"){
            steps{
                sh "chmod +x changeTag.sh"
                sh "./changeTag.sh ${DOCKER_TAG}"
                sh "kubectl apply -f node-app-pod.yml"
                sh "kubectl apply -f services.yml"
            }
        }
    }
}

def getDockerTag(){
    def tag = sh script: 'git rev-parse HEAD', returnStdout: true
    return tag
}
