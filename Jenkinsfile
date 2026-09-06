def appname = "hello-newapp"
def repo = "hello-newapp"  // Replace with your DockerHub username
def appimage = "${repo}/${appname}"
def apptag = "${env.BUILD_NUMBER}"

podTemplate(containers: [
      containerTemplate(name: 'jnlp', image: 'jenkins/inbound-agent', ttyEnabled: true),
      containerTemplate(name: 'docker', image: 'docker:dind', ttyEnabled: true, privileged: true)
  ],
      // <<< שינוי 2: הוספנו volume ל-Docker daemon
    volumes: [
        emptyDirVolume(
            mountPath: '/var/lib/docker',
            memory: false
        )
    ])
  {
    node(POD_LABEL) {
        stage('chackout') {
            container('jnlp') {
            sh '/usr/bin/git config --global http.sslVerify false'
	    checkout scm
          }
        } // end chackout

        stage('build') {
            container('docker') {
              echo "Building docker image..."
            //   sh "docker build -t danielavidan/${appname}:${apptag} ."
              dockerImage = docker.build("${appimage}:${apptag}")

            }
        } //end build

     stage('push') {
            container('docker') {
              script {
                docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-creds') {
                  dockerImage.push()
                }
              }
            }
        } //end push

    }
}
