pipeline {

  environment {
    registryCredential = 'dockerhub'
    imagetag = "landingpage${env.BUILD_ID}"
  }
  
  agent any

  stages {

    stage('Checkout Source') {
      steps {
        git url:'https://github.com/masterarbeithhz/BaseArchitecture_SignOn.git', branch:'main'
      }
    }
    
      stage("Build image") {
            steps {
                script {
                    myapp = docker.build("masterarbeithhz/microservices:${imagetag}")
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
            data = data.replaceAll("JSVAR_DOCKERIMAGE", "${imagetag}")
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
