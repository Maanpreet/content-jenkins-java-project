pipeline {
  agent none


  stages {
    stage('Unit test'){
      agent {
        label 'slave'
      }
      steps{
        sh 'ant -f test.xml -v'
        junit 'reports/result.xml'
      }
    }
    stage('build jar file') {
      agent {
        label 'slave'
      }
      steps {
          sh 'ant -f build.xml -v'
        }
        post {
          success {
            archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
          }
        }
    }


    stage('Deploy jar file to branch folder'){
      agent {
        label 'slave'
      }
      steps{
        sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/${env.BRANCH_NAME}/"
      }
    }

    stage('Testing Jar  on CentOS'){
      agent {
        label 'centos'
      }
      steps{
        sh "wget http://10.32.1.77/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"
      }
    }

    stage('Running test on Debian docker container'){
      agent{
        docker 'openjdk:8u121-jre'
      }
      steps{
        sh "wget http://10.32.1.77/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 3"

      }
    }

    stage('Prmote to green'){
      agent{
        label 'slave'
      }
      when {
        branch 'development'
      }
      steps{
        sh "cp /var/www/html/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.BUILD_NUMBER}.jar  /var/www/html/rectangles/green/"

       }
    }

    stage ('push changes to preprod branch'){
      agent {
        label 'centos'
      }
      when{
        branch 'development'
      }
      steps{
        echo "Stashing git repo"
        sh 'git stash'
        echo "checking to development branch as jenkins checks out to particular ref"
        sh 'git checkout development'
        echo "checking out to preprod branch for above same reason"
        sh 'git checkout preprod'
        echo "mergin development to preprod"
        sh 'git merge development'
        echo "pushing origin to preprod"
        sh 'git push origin preprod'
      }
    }
  }
}
