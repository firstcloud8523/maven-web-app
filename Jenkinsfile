node{
    
    stage('Clone repo'){
        git credentialsId: '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', url: 'https://github.com/firstcloud8523/maven-web-app.git'
    }
    
    stage('Maven Build'){
        def mavenHome = tool name: "maven-3.9.5", type: "maven"
        def mavenCMD = "${mavenHome}/bin/mvn"
        sh "${mavenCMD} clean package"
    }
    
    stage('SonarQube analysis') {       
        withSonarQubeEnv('Sonar-Server-7.8') {
       	sh "mvn sonar:sonar"    	
    }
        
    stage('upload war to nexus'){
	steps{
		nexusArtifactUploader artifacts: [	
			[
				artifactId: '01-maven-web-app',
				classifier: '',
				file: 'target/01-maven-web-app.war',
				type: war		
			]	
		],
		credentialsId: 'nexus3',
		groupId: 'in.firstcloud',
		nexusUrl: '',
		protocol: 'http',
		repository: 'firstcloud-release'
		version: '1.0.0'
	}
}
    
    stage('Build Image'){
        sh 'docker build -t firstcloud8523/mavenwebapp .'
    }
    
    stage('Push Image'){
        withCredentials([string(credentialsId: 'DOCKER-CREDENTIALS', variable: 'DOCKER_CREDENTIALS')]) {
            sh 'docker login -u firstcloud8523 -p ${DOCKER_CREDENTIALS}'
        }
        sh 'docker push firstcloud8523/mavenwebapp'
    }
    
    stage('Deploy App'){
        kubernetesDeploy(
            configs: 'maven-web-app-deploy.yml',
            kubeconfigId: 'Kube-Config'
        )
    }    
}
