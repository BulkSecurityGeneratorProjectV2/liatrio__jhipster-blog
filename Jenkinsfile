pipeline {
    agent {
        docker {
            image 'jhipster/jhipster:v5.7.0'
            args '-u root -e MAVEN_OPTS="-Duser.home=./"'
        }
    }
    stages {
        stage('check java') {
            steps {
                sh "java -version"
            }
        }
        stage('clean') {
            steps {
                sh "chmod +x mvnw"
                sh "./mvnw clean"
            }
        }
        stage('install tools') {
            steps {
                sh "./mvnw com.github.eirslett:frontend-maven-plugin:install-node-and-npm -DnodeVersion=v10.13.0 -DnpmVersion=6.4.1"
            }
        }
        stage('npm install') {
            steps {
                sh "./mvnw com.github.eirslett:frontend-maven-plugin:npm"
            }
        }
        stage('backend tests') {
            steps {
                sh "./mvnw test"
                junit '**/target/surefire-reports/TEST-*.xml'
            }
        }
        stage('frontend tests') {
            steps {
                sh "./mvnw com.github.eirslett:frontend-maven-plugin:npm -Dfrontend.npm.arguments='test -- -u'"
                junit '**/target/test-results/jest/TESTS-*.xml'
            }
        }
        stage('package and deploy') {
            environment {
                HEROKU_API_KEY=credentials('heroku')
            }
            steps {
                sh "./mvnw com.heroku.sdk:heroku-maven-plugin:2.0.5:deploy -DskipTests -Pprod -Dheroku.buildpacks=heroku/jvm -Dheroku.appName=bens-ironic-blog-demo"
                archiveArtifacts artifacts: '**/target/*.war', fingerprint: true
            }
        }
    }
}
//    docker.image('jhipster/jhipster:v5.7.0').inside('-u root -e MAVEN_OPTS="-Duser.home=./"') {
//        stage('quality analysis') {
//            withSonarQubeEnv('sonar') {
//                sh "./mvnw sonar:sonar"
//            }
//        }
//    }
//
//    def dockerImage
//    stage('build docker') {
//        sh "cp -R src/main/docker target/"
//        sh "cp target/*.war target/docker/"
//        dockerImage = docker.build('docker-login/blog', 'target/docker')
//    }
//
//    stage('publish docker') {
//        docker.withRegistry('https://registry.hub.docker.com', 'docker-login') {
//            dockerImage.push 'latest'
//        }
//    }
