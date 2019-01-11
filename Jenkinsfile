node {

    checkout scm

    env.DOCKER_API_VERSION="1.23"
    
    sh "git rev-parse --short HEAD > commit-id"

    tag = readFile('commit-id').replace("\n", "").replace("\r", "")
    appName = "hello-kenzan"
    registryHost = "127.0.0.1:30400/"
    imageName = "${registryHost}${appName}:${tag}"
    env.BUILDIMG=imageName
    yamlContent = load("applications/${appName}/k8s/deployment.yaml") 
    
    stage "Build"
    
        sh "docker build -t ${imageName} -f applications/hello-kenzan/Dockerfile applications/hello-kenzan"
    
    stage "Push"

        sh "docker push ${imageName}"

    stage "Deploy"
        sh "sed 's/<IMAGE_NAME>/${imageName}/' applications/${appName}/k8s/deployment.yaml > applications/${appName}/k8s/deployment.yaml"
        sh "sed 's/<IMAGE_NAME>/${imageName}/' applications/${appName}/k8s/manual-deployment.yaml > applications/${appName}/k8s/manual-deployment.yaml"

        echo "${yamlContent}"
        kubernetesDeploy configs: "applications/${appName}/k8s/*.yaml", kubeconfigId: 'kenzan_kubeconfig'

}