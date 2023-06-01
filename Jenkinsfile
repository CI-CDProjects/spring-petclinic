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
        stage('Sonar Analysis') {
            steps {
                    // performing sonarqube analysis with "withSonarQubeENV(<Name of Server configured in Jenkins>)"
                withSonarQubeEnv('SONAR_CLOUD') {
                    // requires SonarQube Scanner for Maven 3.2+
                    // sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.2:sonar'
                    sh 'mvn clean package sonar:sonar'
                }
            }
        }
        stage('Archive the Package') {
            steps {
                archiveArtifacts artifacts: '**/target/spring-petclinic-*.jar',
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
            mail to: "tanya@acc.in",
                from: 'tqdevops@qt.com',
                subject: "Jenkins build status of ${JOB_NAME} with id ${BUILD_ID}",
                body: "Refer Here ${BUILD_URL} for more details. Build has Failed"
        }
    }
}