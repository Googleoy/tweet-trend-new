def registry = 'https://gitya.jfrog.io/'
pipeline {
     agent {
        node {
            label 'maven-slave'
        }
    }
        environment {
        PATH = "/opt/apache-maven-3.9.4/bin:$PATH"
    }
        environment {
        PATH = "/opt/apache-maven-3.9.4/bin:$PATH"
 }
    Stages{
	    stage("build") {
            steps {
                    echo "------------------------- build started -------------------"
                        sh 'mvn clean deploy _Dmaven.test.skip=true'
                    echo "------------------------- build completed -------------------"
                }
            }
        stage("test"){
            steps{
                    echo "------------------------- unit test started -------------------"
                         sh 'mvn surefire-report:report'
                    echo "------------------------- build completed -------------------"
        }
    }
}
        stage('SonarQube analysis') {
                environment {
                scannerHome = tool 'gitesh-sonar-scanner'
    }
            steps {
            withSonarQubeEnv('gitesh-sonarqube-server') { // If you have configured more than one global server connection, you can specify its name
                sh "${scannerHome}/bin/sonar-scanner"
         }   }
    }   
      
        stage("Jar Publish") {
            steps {
                script {
                    echo '<--------------- Jar Publish Started --------------->'
                        def server = Artifactory.newServer url:registry+"/artifactory" ,  credentialsId:"artifact-cred"
                        def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}";
                        def uploadSpec = """{
                          "files": [
                            {
                              "pattern": "jarstaging/(*)",
                              "target": "libs-release-local/{1}",
                              "flat": "false",
                              "props" : "${properties}",
                              "exclusions": [ "*.sha1", "*.md5"]
                            }
                         ]
                     }"""
                     def buildInfo = server.upload(uploadSpec)
                     buildInfo.env.collect()
                     server.publishBuildInfo(buildInfo)
                     echo '<--------------- Jar Publish Ended --------------->'  
            
            }
        }   }
    }   
    


