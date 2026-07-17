def registry = 'https://trial0hq743.jfrog.io/

pipeline {
    agent any

    environment {
        PATH = "/opt/maven/bin:$PATH"
    }

    stages {

        stage("Build") {
            steps{
		echo '---- Build started ------------------'
		sh 'mvn clean deploy -Dmaven.test.skip=true'
		echo '---- Build completed ------------------'
	    }
	}

	stage("test"){
	   steps{
		echo " ------Unit test started------------"
		sh 'mvn surefire-report:report'
		echo " ------Unit test completed------------"
	    }
	}

	stage("sonarQube analysis"){
	   environment {
		scannerHome = tool 'saidemy-sonar-scanner'

	   }

	   steps{
		withSonarQubeEnv('saidemy-sonarqube-server'){

		   sh "${scannerHome}/bin/sonar-scanner"
		}
	  }	
	}
	
	stage("Jar Publish") {
		steps {
		   script {
			echo '<--Jar Publish Started-------->'
			def server = Artifactory.newServer url: registry + "/artifactory", credentialsId: "artifact-cred"
			def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}"
			def uploadSpec = """{
				"files": [
				    {
				      "pattern": "jarstaging/(*)",
				      "target": "soumya-libs-release-local/{1}",
				      "flat": "false",
				      "props": "${properties}",
				      "exclusions": [ "*.shal", "*.md5"]
				     }
				]
			   }"""
			def buildInfo = server.upload(uploadSpec)
			buildInfo.env.collect()
			server.publishBuildInfo(buildInfo)
			echo '<--Jar Publish ended-------->'
		}	
      	}
      } 
    }
  }
