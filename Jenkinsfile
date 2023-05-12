pipeline {
    agent { label 'JDK_17' }
    triggers { pollSCM('* * * * *') }
    parameters {
        choice(name: 'MAVEN_GOAL', choices: ['package', 'install', 'clean', 'test', 'compile'], description: 'This is a Maven goal.')
    }
    stages {
        stage('VCS') {
            steps {
                git url: 'https://github.com/CI-CDProjects/spring-petclinic.git',
                    branch: 'decl'
            }
        }
        stage('Build the Package') {
        tools {
            jdk 'OPENJDK_17'
            }
            steps {
                sh "mvn ${params.MAVEN_GOAL}"
            }
        }
        stage('Archive the Package') {
            steps {
                archiveArtifacts artifacts: '**/target/spring-petclinic-3.0.0-SNAPSHOT.jar',
                                onlyIfSuccessful: true
            }
        }
        stage('Publish the Test Results') {
            steps {
                junit testResults: '**/surefire-reports/TEST-*.xml',
                    allowEmptyResults: true
            }
        }
    }
    post {            
        success {
            mail to: 'tqdevops-team@qt.com',
                from: 'tqdevops@qt.com',
                subject: "Jenkins build status of ${JOB_NAME} with id ${BUILD_ID}",
                body: "Refer Here ${BUILD_URL} for more details. Build is Successful"
        }
        failure {
            mail to: "${GIT_AUTHOR_EMAIL}",
                from: 'tqdevops@qt.com',
                subject: "Jenkins build status of ${JOB_NAME} with id ${BUILD_ID}",
                body: "Refer Here ${BUILD_URL} for more details. Build has Failed"
        }
    }
}