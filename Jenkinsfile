podTemplate(containers: [containerTemplate(name: 'maven', image: 'maven', command: 'sleep', args: 'infinity')]) {
  node(POD_LABEL) {
    container('maven') {
        stage('SCM') {
            checkout scm
        }  
         
        stage('Build') {
            sh 'mvn -B -DskipTests clean package'
        }

        stage('Test') {
            sh 'mvn -Dmaven.test.failure.ignore verify'
            junit 'target/surefire-reports/*.xml'
        }
        
        stage('SonarQube Analytics') {
            withSonarQubeEnv('sonarqube') {
                sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:5.0.1.3006:sonar'
            }
        }
      
        // No need to occupy a node
        stage("Wait for Quality Gate"){
            timeout(time: 1, unit: 'HOURS') { // Just in case something goes wrong, pipeline will be killed after a timeout
                def qg = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
                if (qg.status != 'OK') {
                   error "Pipeline aborted due to quality gate failure: ${qg.status}"
                }
            }
        }
    }
  }
}
