pipeline {
    agent { label 'server1' }
    environment{
        NAME = "Kanishka"
    }
    parameters {
        string defaultValue: 'Sunasinghe', name: 'LASTNAME'
    }
    tools { maven 'mymaven' }
    stages{
        stage('build'){
            steps{
                sh 'mvn clean package'
                echo 'hello $NAME ${params.LASTNAME}'
            }
            post{
                success{
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }
    }
}
