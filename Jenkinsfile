node {
    stage('git clone') {
        git branch: 'main',
            url: 'https://github.com/Anton788/magic'
            
    }
    stage('build') {
        sh 'mvn -f java/pom.xml clean verify'
    }
    stage('test') {
        sh 'mvn -f java/pom.xml test'
    }
    stage('sonar_qube') {
        def scannerHome = tool 'MySonar';
        withSonarQubeEnv('MySonar') {
            sh "${scannerHome}/bin/sonar-scanner \
            -Dsonar.projectKey=pipeline \
            -Dsonar.sources=java/src/main \
            -Dsonar.tests=java/src/test \
            -Dsonar.java.binaries=java/target/classes \
            -Dsonar.junit.reportPaths=java/target/surefire-reports \
            -Dsonar.coverage.jacoco.xmlReportPaths=java/target/site/jacoco/jacoco.xml"
        }
    }
    stage('allure') {
        allure([
                includeProperties: false,
                jdk: '',
                properties: [],
                reportBuildPolicy: 'ALWAYS',
                results: [[path: 'java/target/allure-results']]
            ])
    }
    stage('deploy') {
        withCredentials([file(credentialsId: 'aazhogov_ansible_pass', variable: 'VAULT_PASSWORD')]) {
            sh 'ansible-playbook deploy/playbook.yml --extra-vars "ansible_sudo_pass=cHONtic" -i deploy/hosts.txt'
        }
    }
}
