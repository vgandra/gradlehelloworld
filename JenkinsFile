node { 
	checkout scm
	env.PATH ="${tool 'gradle'}/bin:${env.PATH}"
	stash excludes: 'target/', includes: '**', name: 'source'
	emailext attachLog: true,body: 'Test', compressLog: true, subject: 'Test jenkins Pipelines', to: 'sgandra@altimetrik.com,snachiappan@altimetrik.com' 
	properties([pipelineTriggers([cron('0 10 * * *')])])
	//checkout([$class: 'GitSCM', branches: [[name: '*/master']], browser: [$class: 'Phabricator', repo: 'ssh://phvcs@platformworks.altimetrik.com:2222/diffusion/9/hachon.git', repoUrl: 'https://platformworks.altimetrik.com/'], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[]]])
	stage('validate') {
		sh 'gradle validate'
	} 
	stage('compile') {
		sh 'gradle compile'
	} 
	stage('package') {
		 sh 'gradle clean package -DskipTests'
	}
	stage('install') {
		sh 'gradle clean install'
	} 
	stage('test') {
		parallel 'integration': {
			sh 'gradle clean verify'
		}, 'quality': {
			//sh 'gradle sonar:sonar'
			} 
	} 
	stage('deploy') {
		unstash 'source'
		sh 'cp target/*.jar /opt/deploy'
	}
}
