pipeline {
  agent any
  stages {
    stage ('Setup Env') {
      steps {
        sh 'npm install'
      }
    }
    stage ('Build') {
      steps {
        sh './node_modules/.bin/hexo clean'
        sh './node_modules/.bin/hexo generate'
      }
    }
    stage ('Tar Static And Deploy') {
      steps {
        sh 'tar -zcvf deploy.tar.gz .deploy_git'
        sh 'cp deploy.tar.gz /www/wwwroot/blog.si-yee.com'
        sh 'rm -rf /www/wwwroot/blog.si-yee.com/static'
        sh 'rm -rf /www/wwwroot/blog.si-yee.com/backup'
        sh 'mkdir -p /www/wwwroot/blog.si-yee.com/static'
        sh 'mkdir -p rm -rf /www/wwwroot/blog.si-yee.com/backup'
        sh 'tar -zxvf /www/wwwroot/blog.si-yee.com/deploy.tar.gz -C /www/wwwroot/blog.si-yee.com/backup/'
        sh 'cp -R /www/wwwroot/blog.si-yee.com/backup/deploy_git/* /www/wwwroot/blog.si-yee.com/static/'
      }
    }
  }
}