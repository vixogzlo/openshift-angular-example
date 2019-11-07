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
          npm config set registry http://nexus-santander-devops.apps.s0f1.xyz/repository/npm-proxy
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
    stage ('Registrar Docker') {
      steps{
        script {
          openshift.withCluster() {
            openshift.withProject('poc') {
              openshift.selector("bc", "angular-example").startBuild("--from-dir=./dist", "--wait=true", "--follow", "--loglevel=8")
            }
          }
        }
      }
    }

    stage('Desplegar') {
      steps {
        script {
          openshift.withCluster() {
            openshift.withProject('poc') {
              openshift.selector("dc", "angular-example").rollout().latest();
            }
          }
        }
      }
    } 

  }
}