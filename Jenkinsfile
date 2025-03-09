pipeline {
    parameters {
        choice choices: ['dev', 'prod'], name: 'select_environment'
    }
    tools { maven 'mymaven' }
    agent none  // Use agent none here for global pipeline agent management
    stages {
        stage('build') {
            agent { label 'server1' }
            steps {
                sh 'mvn clean package -DskipTests=true'
            }
        }
        stage('test') {
            agent { label 'server1' }  // The agent is assigned here
            parallel {
                testA: {
                    steps {
                        echo "This is test A"
                        sh 'mvn test'
                    }
                }
                testB: {
                    steps {
                        echo "This is test B"
                        sh 'mvn test'
                    }
                }
            }
            post {
                success {
                    dir('webapp/target/') {
                        stash name: "maven-build", includes: "*.war"
                    }
                }
            }
        }
        stage('deploy_dev') {
            when { 
                expression { params.select_environment == 'dev' }
            }
            agent { label 'server1' }
            steps {
                dir('/var/www/html') {
                    unstash "maven-build"
                }
                sh """
                cd /var/www/html/
                jar -xvf webapp.war
                """
            }
        }
    }
}
