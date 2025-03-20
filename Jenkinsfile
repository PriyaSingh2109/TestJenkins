pipeline {
    agent any
  
    options {
        buildDiscarder logRotator(numToKeepStr: '10')
    }

    environment {
        dockerImage = ''
        NON_PROD_IMAGE_NAME = "ari_responsive_shared_testautomation-specflow"
        ECR_NONPROD_URL = "https://848590249604.dkr.ecr.us-west-2.amazonaws.com/${NON_PROD_IMAGE_NAME}"
        ECR_NONPROD_CREDENTIALS = 'arn:aws:iam::848590249604:user/ari_responsive_ci_cd'
		PROD_IMAGE_NAME = "ari_responsive_prod_testautomation-specflow"
		ECR_PROD_URL = "https://990265700609.dkr.ecr.us-west-2.amazonaws.com/${PROD_IMAGE_NAME}"
        ECR_PROD_CREDENTIALS = 'arn:aws:iam::990265700609:user/ari_responsive_ci_cd'
    }
    
	parameters {
        booleanParam defaultValue: false, description: 'Check this box to push the image to Prod ECR registry', name: 'PushImageToProd'
       
    }
	
    stages {		
		stage('Clean Workspace and Checkout Code') {
            steps {
                cleanWs()
				script {
					def checkoutVars = checkout scm
					env.GIT_URL = checkoutVars.GIT_URL
					env.GIT_BRANCH = checkoutVars.GIT_BRANCH
					env.GIT_COMMIT = checkoutVars.GIT_COMMIT
					env.GIT_AUTHOR = sh (script: 'git show -s --pretty=%ae ${GIT_COMMIT} | cut -d "@" -f 1', returnStdout: true).trim().replaceAll("\\s","").toLowerCase()
				}
            }
        }
		stage("Clean up unused docker resources") {
            steps{
                sh "docker system prune -f"
            }
        }
		stage('Add Nuget.COnfig') {
            steps {
			  dir('./ResponsiveAutomationSolution'){
                script {
                    withCredentials([aws(credentialsId: 'ldv-sharedservices-spark-auto_ci_cd', accessKeyVariable: 'AWS_ACCESS_KEY_ID', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                        authToken = sh(
                            returnStdout: true,
                            script: 'aws codeartifact get-authorization-token \
                                --domain leadventure \
                                --output text \
                                --query authorizationToken \
                                --region us-west-2'
                        ).trim()
                        repository = sh(
                            returnStdout: true,
                            script: 'aws codeartifact get-repository-endpoint \
                                --domain leadventure \
                                --format nuget \
                                --output text \
                                --region us-west-2 \
                                --repository aut'
                        ).trim()
                    }
                    withDotNet(sdk: 'dotNet80') {
                        sh('dotnet new nugetconfig')
                        sh("dotnet nuget add source ${repository}v3/index.json \
                            --configfile nuget.config \
                            --name leadventure-test \
                            --password ${authToken} \
                            --store-password-in-clear-text \
                            --username aws"
                        )
                    }
                }
				}
            }
        }
        
        stage('Build Temp Image for ResponsiveAutomationTests') {
			when {
                not { anyOf { branch 'master'; branch 'test1'; branch 'test2'; branch 'test3'; branch 'test4'; branch 'test0' } }
			}
            steps {
                script {
					def branch = env.GIT_BRANCH
				    if(branch.contains('PR-')){
					println "Branch Name "+ "${GIT_BRANCH}"
					dockerImage = docker.build("${NON_PROD_IMAGE_NAME}:${GIT_BRANCH}",".")
					}
					else{
					println " Author "+ "${GIT_AUTHOR}"
					dockerImage = docker.build("${NON_PROD_IMAGE_NAME}:${GIT_AUTHOR}",".")
					}
                    println "Docker Image URL: "+dockerImage
                }
            }
        }
		
		stage('Build Image for ResponsiveAutomationTests') {
			when {
                anyOf { branch 'master'; branch 'test1'; branch 'test2'; branch 'test3'; branch 'test4'; branch 'test0' }
			}
            steps {
                script {
                    dockerImage = docker.build("${NON_PROD_IMAGE_NAME}:${GIT_BRANCH}",".")
                    println "Docker Image URL: "+dockerImage
                }
            }
        }

        stage("Publish Image To Non-Prod Registry") {
		  when {
                not { changeRequest() }
            }
            steps{
                script {
                    docker.withRegistry(ECR_NONPROD_URL, "ecr:us-west-2:${ECR_NONPROD_CREDENTIALS}") {
                        dockerImage.push()
                        println "Docker Image Pushed Successfully"
                    }
                }
            }
        }
		
		
		 stage("Publish Image To Prod Registry") {
		  when{
		    anyOf{
			  expression { return params.PushImageToProd }
              branch 'master'			  
			}
			not { changeRequest() }
		  }
           steps {
                script {
                    
					if(params.PushImageToProd){
					sh "docker tag ${NON_PROD_IMAGE_NAME}:${GIT_AUTHOR} ${PROD_IMAGE_NAME}:${GIT_AUTHOR}"
					   dockerImage = docker.image("${PROD_IMAGE_NAME}:${GIT_AUTHOR}")
					}else{
					sh "docker tag ${NON_PROD_IMAGE_NAME}:${GIT_BRANCH} ${PROD_IMAGE_NAME}:${GIT_BRANCH}"
					  dockerImage = docker.image("${PROD_IMAGE_NAME}:${GIT_BRANCH}")
					}
                    

                    docker.withRegistry(ECR_PROD_URL, "ecr:us-west-2:${ECR_PROD_CREDENTIALS}") {
                        dockerImage.push()
                    }
                }
            }
        }
    }

    post {
        always {
            recordIssues enabledForFailure: true, aggregatingResults: true, tools: [msBuild()]
        }
    }
}