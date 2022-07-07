node {
    def app
    def full_image_name = 'deepfenceio/jenkins-example:latest'

    stage('Clone repository') {
        checkout scm
    }

    stage('Build image') {
        app = docker.build("${full_image_name}", "-f Dockerfile .")
    }

    stage('Run Deepfence IOC Scanner'){
        DeepfenceAgent = docker.image("harshvkarn/dis:latest")
        try {
            c = DeepfenceAgent.run("--rm --name=deepfence-ioc-scanner -v /var/run/docker.sock:/var/run/docker.sock", "--image-name ${full_image_name}")
            sh "docker logs -f ${c.id}"
            def out = sh script: "docker inspect ${c.id} --format='{{.State.ExitCode}}'", returnStdout: true
            sh "exit ${out}"
        } finally {
            c.stop()
        }
    }

    stage('Remove unused docker image') {
        sh "docker rmi ${full_image_name}"
    }
}
