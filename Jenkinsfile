def appname = "hello-newapp"
def repo = "hello-newapp"  // Replace with your DockerHub username
def appimage = "${repo}/${appname}"
def apptag = "${env.BUILD_NUMBER}"

podTemplate(containers: [
      containerTemplate(name: 'jnlp', image: 'jenkins/inbound-agent', ttyEnabled: true),
      containerTemplate(name: 'docker', image: 'docker:dind', command: 'cat', ttyEnabled: true, privileged: true)
  ])
  volumes: [
    emptyDirVolume(mountPath: '/var/lib/docker', memory: false) // Q: Why do we need this volume?
  ]
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
              sh "docker build -t danielavidan/${appname}:${apptag} ."
            //   sh "echo docker push $appimage"
            }
        } //end build
    }
}
