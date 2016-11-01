#!groovy

node {

    def testImg
    stage('Build') {
      git url: 'https://github.com/enydrueda/rails-on-docker.git', credentialsId: 'github'
      testImg = docker.build("enydrueda/jenkins_test:${env.BUILD_TAG}", '.')
    }

    stage('Test') {
      mysql = docker.image('mysql:5.7.7');

      mysql.withRun ("--env MYSQL_ROOT_PASSWORD=mypass") {db ->
        testImg.inside("--link=${db.id}:db") {
          sh 'bin/rails db:migrate RAILS_ENV=test'
          sh 'rake test'
        }
      }
    }

    stage('Promote Image') {
      // All the tests passed. We can now retag and push the 'latest' image.
      docker.withRegistry("https://registry.hub.docker.com", "docker-registry"){
        testImg.push()
        testImg.push('latest')
      }
    }
  }