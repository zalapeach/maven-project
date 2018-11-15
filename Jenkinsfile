pipeline {
  agent { label 'master' }

    tools {
      maven 'Maven'
        jdk 'localJDK'
    }

  parameters {
    string(name: 'staging', defaultValue: 'atlbz153199.atl.dst.ibm.com', description: 'staging environment')
      string(name: 'production', defaultValue: 'sbybz2015.sby.ibm.com', description: 'production environment')
  }

  triggers {
    pollSCM('0 0 1 * *')
  }

  stages {
    stage('Build') {
      steps {
        sh 'mvn clean package'
      }
      post {
        success {
          echo 'Now archiving...'
          archiveArtifacts artifacts: '**/target/*.war'
        }
      }
    }

    stage('Deployments') {
      parallel {
        stage('Deploy to staging') {
          steps {
            sh 'whoami'
            sh "scp **/target/*.war root@${params.staging}:/root/apache-tomcat-8.5.31-staging/webapps"
          }
        }

        stage ('Deploy to production') {
          steps {
            sh 'whoami'
            sh "scp **/target/*.war root@${params.production}:/root/apache-tomcat-8.5.31-production/webapps"
          }
        }
      }
    }
  }
}
