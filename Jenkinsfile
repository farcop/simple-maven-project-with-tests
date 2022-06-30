podTemplate(containers: [containerTemplate(name: 'maven', image: 'maven', command: 'sleep', args: 'infinity')]) {
  node(POD_LABEL) {
    checkout scm
    container('maven') {
      
        stage('Build') {
            sh 'mvn -B -DskipTests clean package'
        }

        stage('Test') {
            sh 'mvn -Dmaven.test.failure.ignore verify'
            // junit 'target/surefire-reports/*.xml'
        }
        
        stage('SonarQube Analytics') {
            withSonarQubeEnv('sonarqube') {
                sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.2:sonar'
            }
        }
    }
//     junit '**/target/surefire-reports/TEST-*.xml'
  }
}
