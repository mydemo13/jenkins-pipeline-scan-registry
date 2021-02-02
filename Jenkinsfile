node {
    def image

    stage('Clean') {
        sh 'rm * -f -r -d -v'
    }

    stage('cloneRepository') {
        checkout scm
    }

    stage('pullImage') {
        image = docker.image("${params.imageName}")
        image.pull()
    }
    
    stage('Scan image with twistcli') {
        withCredentials([usernamePassword(credentialsId: 'twistlock_creds', passwordVariable: 'TL_PASS', usernameVariable: 'TL_USER')]) {
            sh 'curl -k -u $TL_USER:$TL_PASS --output ./twistcli https://${TL_CONSOLE}/api/v1/util/twistcli'
            sh "sudo chmod a+x ./twistcli"
            try {
                sh './twistcli images scan --u $TL_USER --p $TL_PASS --address https://${TL_CONSOLE}  --details ${imageName}'
            } finally {
                sh "docker image rm ${imageName}"
            }
        }
    }
}
