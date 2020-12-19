pipeline {

  environment {
    registryCredential = 'dockerhub'
    imagefolder = "masterarbeithhz/microservices:"
    imagetag = "signon${env.BUILD_ID}"
    giturl = 'https://github.com/masterarbeithhz/BaseArchitecture_SignOn.git'
    giturl2 = 'https://github.com/masterarbeithhz/BaseArchitecture_SharedFiles.git'
  }
  
  agent any

  stages {

    stage('Checkout Source') {
      steps {
        git url:"${giturl2}", branch:'main'
        git url:"${giturl}", branch:'main'
      }
    }
    
      stage("Build image") {
            steps {
                script {
                    myapp = docker.build("${imagefolder}${imagetag}")
                }
            }
        }
    
      stage("Push image") {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', registryCredential) {
                            myapp.push("${imagetag}")
                    }
                }
            }
        }

      stage("Prepare Yaml") {
        steps {
          script {
            def data = readFile file: "kubmanifest.yaml"
            data = data.replaceAll("JSVAR_DOCKERIMAGE", "${imagefolder}${imagetag}")
            echo data
            writeFile file: "kubmanifest.yaml", text: data
          }
        }
      }
    
    stage('Deploy App') {
      steps {
        script {
          kubernetesDeploy(configs: "kubmanifest.yaml", kubeconfigId: "mykubeconfig1")
        }
      }
    }

  }

}
