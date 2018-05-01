pipeline {
  agent { label 'TrueOS-World' }

  environment {
    GH_ORG = 'trueos'
    GH_REPO = 'trueos'
  }
  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Pre-Clean') {
      steps {
        sh 'make clean'
      }
    }
    stage('World') {
      steps {
        sh 'make -j32 buildworld'
      }
    }
    stage('Kernel') {
      steps {
        sh 'make -j32 buildkernel'
      }
    }
    stage('Base Packages') {
      environment {
           PKGSIGNKEY = credentials('a50f9ddd-1460-4951-a304-ddbf6f2f7990')
      }
      steps {
        sh 'make -j32 packages'
      }
    }
    stage('Ports') {
      environment {
           PKGSIGNKEY = credentials('a50f9ddd-1460-4951-a304-ddbf6f2f7990')
      }
      steps {
        sh 'cd release && make poudriere'
      }
    }
    stage('Release') {
      post {
        success {
          archiveArtifacts artifacts: 'artifacts/**', fingerprint: true
        }
      }
      steps {
        sh 'rm -rf ${WORKSPACE}/artifacts'
        sh 'cd release && make release'
        sh 'mkdir -p ${WORKSPACE}/artifacts/repo'
        sh 'cp /usr/obj${WORKSPACE}/amd64.amd64/release/*.iso ${WORKSPACE}/artifacts'
        sh 'cp /usr/obj${WORKSPACE}/amd64.amd64/release/*.txz ${WORKSPACE}/artifacts'
        sh 'cp /usr/obj${WORKSPACE}/amd64.amd64/release/MANIFEST ${WORKSPACE}/artifacts'
        sh 'cp -r /usr/obj${WORKSPACE}/repo/* ${WORKSPACE}/artifacts/repo/'
      }
    }
    stage('Post-Clean') {
      steps {
        sh 'make clean'
        sh 'cd release && make clean'
        sh 'rm -rf /usr/obj${WORKSPACE}'
        script {
          cleanWs notFailBuild: true
        }
      }
    }
  }
}
