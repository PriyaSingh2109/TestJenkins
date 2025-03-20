pipeline {
    agent {
        kubernetes {
            label 'testkube-agent-namspc'  // This should match the label you defined in your pod template
			cloud 'Kubernetes'
			defaultContainer 'testkube-cli' // The container within the pod template
           
        }
    }

  
    environment {
		TK_NAMESPACE = 'testkube'
		TK_VERSION = '1.16.7'
		// KUBECONFIG = 'C:\\Users\\Priya.Singh\\.kube\\config'
		// TESTKUBE_PATH = "C:\\Program Files\\Testkube"
		// KUBECONFIG = credentials('kubeconfig-cred')
		KUBECONFIG = credentials('kubeconfigcred')
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
					//withEnv(["PATH+TESTKUBE=C:\\Program Files\\Testkube"]) {                   
					bat """
                        echo %KUBECONFIG%
                        kubectl config view
                        kubectl-testkube run test priya
                    """
					//}
				}
		    }
        }
    }
}
