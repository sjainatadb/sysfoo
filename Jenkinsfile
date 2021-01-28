pipeline {
  agent none
  stages {
    stage('build') {
      agent {
        docker {
          image 'maven:3.6.3-jdk-11-slim'
        }

      }
      steps {
        echo 'compile maven app'
        sh 'mvn compile'
      }
    }

    stage('test') {
      agent {
        docker {
          image 'maven:3.6.3-jdk-11-slim'
        }

      }
      steps {
        echo 'test maven app'
        sh 'mvn clean test'
      }
    }

    stage('package') {
      agent {
        docker {
          image 'maven:3.6.3-jdk-11-slim'
        }

      }
      when {
        beforeAgent true
        branch 'master'
      }
      steps {
        echo 'package maven app'
        sh 'mvn package -DskipTests'
        archiveArtifacts(artifacts: 'target/*.war', allowEmptyArchive: true)
      }
    }

    stage('Docker Build and Publish') {
      agent any
      when {
        beforeAgent true
        branch 'master'
      }
      steps {
        script {
          docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {
            def dockerImage = docker.build("sjainatadb/sysfoo:v${env.BUILD_ID}", "./")
            dockerImage.push()
            dockerImage.push("latest")
            dockerImage.push("dev")
          }
        }

      }
    }

	stage('Deploy to Dev') {
		when {
			beforeAgent true
			branch 'master'
		}

		agent any

		steps {
			echo 'Deploying to Dev Environment with Docker Compose'
			sh 'docker-compose up -d'
		}
	}

  }
  tools {
    maven 'Maven 3.6.3'
  }
}
