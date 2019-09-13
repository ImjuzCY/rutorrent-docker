pipeline {
  environment {
    registry = "romancin/rutorrent"
    repository = "rutorrent"
    withCredentials = 'dockerhub'
    registryCredential = 'dockerhub'
  }
  agent any
  stages {
    stage('Cloning Git Repository') {
      steps {
        git url: 'https://github.com/romancin/rutorrent-docker.git',
            branch: 'master'
      }
    }
    stage('Building image and pushing it to the registry') {
            steps {
                script {
                    def version = readFile('VERSION')
                    def versions = version.split('\\.')
                    def major = '0.9.8-' + versions[0]
                    def minor = '0.9.8-' + versions[0] + '.' + versions[1]
                    def patch = '0.9.8-' + version.trim()
                    docker.withRegistry('', registryCredential) {
                        def image = docker.build registry + ":latest"
                        image.push()
                        image.push(major)
                        image.push(minor)
                        image.push(patch)
                    }
                }
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'DOCKERHUB_PASSWORD', usernameVariable: 'DOCKERHUB_USERNAME')]) {
                    docker.image('sheogorath/readme-to-dockerhub').run('-v $PWD:/data -e DOCKERHUB_USERNAME=$DOCKERHUB_USERNAME -e DOCKERHUB_PASSWORD=$DOCKERHUB_PASSWORD -e DOCKERHUB_REPO_NAME=$repository')
                    }
                }
            }
    }
 }
 post {
        success {
            telegramSend '[Jenkins] - Pipeline CI-rutorrent-docker $BUILD_URL finalizado con estado :: $BUILD_STATUS'    
        }
    }
}
