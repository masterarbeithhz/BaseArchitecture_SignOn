pipeline {

  environment {
    registryCredential = 'dockerhub'
    imagefolder = "masterarbeithhz/microservices:"
    imagetag = "signon${env.BUILD_ID}"
    giturl = 'https://github.com/masterarbeithhz/BaseArchitecture_SignOn.git'
  }
  
  agent any

  stages {

     stage('CheckoutModule1') {
        steps {
            sh 'mkdir -p Module1'
            dir("Module1")
            {
                git branch: "main",
                url: 'https://github.com/masterarbeithhz/BaseArchitecture_SignOn.git'
            }
        }
    }

    stage('CheckoutModule2') {
        steps {
            sh 'mkdir -p Module2'
            dir("Module2")
            {
                git branch: "main",
                url: 'https://github.com/masterarbeithhz/BaseArchitecture_SharedFiles.git'
            }
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
