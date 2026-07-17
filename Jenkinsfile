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
     }
  }
