pipeline {
    parameters {
        choice choices: ['dev', 'prod'], name: 'select_environment'
    }
    tools { maven 'mymaven' }
    agent { label 'server1' }  // Use agent none here for global pipeline agent management
    stages {
        stage('build') {
            agent { label 'server1' }
            steps {
                sh 'mvn clean package -DskipTests=true'
            }
        }
        stage('test') {
            parallel {
                stage('testA'){
                    agent { label 'server1' }
                    steps {
                        echo "This is test A"
                        sh 'mvn test'
                    }
                }
                stage('testB'){
                    agent { label 'server1' }
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
                beforeAgent true
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
        stage('deploy_prod'){
            when { 
                expression { params.select_environment == 'prod' }
                beforeAgent true
            }
            agent { label 'server2' }
            steps {
                timeout(time: 15, unit: "MINUTES") {
	                input message: 'Do you want to approve the deployment?', ok: 'Yes'
	            }
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
