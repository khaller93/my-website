pipeline {
  agent { label 'docker' }

  stages {
    stage('Deploy Personal Website') {
      agent { label "linux && amd64 && docker" }
      steps {
        cleanWs(deleteDirs: true)
        script {

          stage('Clone') {
            checkout(
              [
                $class: 'GitSCM', 
                branches: [
                  [
                    name: '*/main'
                  ]
                ], 
                doGenerateSubmoduleConfigurations: false, 
                extensions: [
                  [
                    $class: 'SubmoduleOption', 
                    disableSubmodules: false, 
                    parentCredentials: true, 
                    recursiveSubmodules: true, 
                    reference: '', 
                    trackingSubmodules: false
                  ]
                ], 
                submoduleCfg: [], 
                userRemoteConfigs: [
                  [
                    credentialsId: 'github-khaller93', 
                    url: 'git@github.com:khaller93/my-website.git'
                  ]
                ]
              ]
            )
          }

          stage('Compile') {
            def pwd = sh(script: "pwd | tr -d '\\n'", returnStdout: true)
            sh "docker run --pull always --rm -v $pwd:/src klakegg/hugo:0.89.0"
          }

          stage('Deploy') {
            withCredentials([file(credentialsId: 'kevinhaller-dev-ssh-key',
                                  variable: 'SSH_KEY')]) {
              sh """rsync -av -e 'ssh -p 222 -o StrictHostKeyChecking=no -i $SSH_KEY' \
                    public/ kevinhn@www457.your-server.de:public_html/repo \
                    --delete \
                    --exclude=datasets \
                    --exclude=nostrpfp.jpeg
                """
            }
          }

        }
      }
    }
  }
}