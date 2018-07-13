pipeline {
  agent any

    tools {
      maven 'Maven'
        jdk 'localJDK'
    }

  parameters {
    string(name: 'staging', defaultValue: 'atlbz153199.atl.dst.ibm.com', description: 'staging environment')
      string(name: 'production', defaultValue: 'sbybz2015.sby.ibm.com', description: 'production environment')
  }

  triggers {
    pollSCM('* * * * *')
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
            sh "scp -i /home/jenkins/tomcat-demo.pem **/target/*.war root@${params.staging}:/root/apache-tomcat-8.5.31-staging/webapps"
          }
        }

        stage ('Deploy to production') {
          steps {
            sh "scp -i /home/jenkins/tomcat-demo.pem /var/lib/jenkins/jobs/pipeline-fully-automated/workspace/webapp/target/*.war root@${params.production}:/root/apache-tomcat-8.5.31-production/webapps"
          }
        }
      }
    }
  }
}
