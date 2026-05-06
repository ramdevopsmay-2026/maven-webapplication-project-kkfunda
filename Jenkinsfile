node{
    def mavenHome= tool name= "maven-3.9.9"
    stage('git checkout'){
        git branch: 'dev', url: 'https://github.com/ramdevopsmay-2026/maven-webapplication-project-kkfunda.git'
    }
    stage('compile'){
        sh "${mavenHome}/bin/mvn clean compile"
    }
    stage('maven build'){
        sh "${mavenHome}/bin/mvn clean package"
    }
    stage('sonar report'){
        sh "${mavenHome}/bin/mvn sonar:sonar"
    }
    stage('upload to atrifact to nexus')
    {
        sh "${mavenHome}/bin/mvn deploy"
    }
    stage('Deploy to Tomcat') {
    echo "Deploying WAR file using curl..."

    sh """
        curl -u sai:password \
        --upload-file /var/lib/jenkins/workspace/scriptedpl-ci-cd-test/target/maven-web-application.war \
        "http://13.206.237.80:8080/manager/text/deploy?path=/maven-web-application&update=true"
    """
}
}
