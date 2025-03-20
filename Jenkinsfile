pipeline {
    agent any
  
    environment {
		TK_NAMESPACE = 'testkube'
		TK_VERSION = '1.16.7'
		// KUBECONFIG = 'C:\\Users\\Priya.Singh\\.kube\\config'
		// TESTKUBE_PATH = "C:\\Program Files\\Testkube"
		// KUBECONFIG = credentials('kubeconfig-cred')
		KUBECONFIG = credentials('config')
    }
	
    stages {		
		stage('Setup Testkube') {
            steps {
                script {
                    // setupTestkube()
                    // bat 'kubectl testkube run test priya'
                   
					//bat '''
                    //    echo %KUBECONFIG%
                    //    kubectl config view
                    //    kubectl testkube run test priya
                    // '''
					
					//setupTestkube()
					withEnv(["PATH+TESTKUBE=C:\\Program Files\\Testkube"]) {                    bat """
                        echo %KUBECONFIG%
                        kubectl config view
                        kubectl-testkube run test priya
                    """
					}
				}
		    }
        }
    }
}
