pipeline{
  agent { 
    node {
      label 'nodejs8'
    } 
  }
  stages{
    stage ('Checkout codigo fuente'){
      steps{
        checkout scm
      }
    }
    stage ('Instalar dependencias'){
      steps{
        sh '''
          npm config set registry http://nexus3-test1.apps.ocp.cl.bsch/repository/npm-proxy/
          npm install --verbose -d 
          npm install --save classlist.js
        '''
      }
    }
    stage ('Revisión calidad con LINT'){
      steps{
        sh '$(npm bin)/ng lint'
      }
    }
    stage ('Construcción Aplicación') {
      steps{
        sh '$(npm bin)/ng build --prod --build-optimizer'
      }
    }
    stage ('Desplegar') {
      steps{
        script {
          openshift.withCluster() {
            openshift.withProject() {
              openshift.selector("bc", "angular-example").startBuild("--from-dir=./dist/openshift-angular-example/", "--wait=true", "--follow", "--loglevel=8")
            }
          }
        }
      }
    }

  }
}