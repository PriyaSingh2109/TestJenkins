pipeline {
    agent {
        kubernetes {
            label 'testkube-agent'
            yaml """
apiVersion: v1
kind: Pod
metadata:
  name: testkubeagentpod
  labels:
    testkube: agent
spec:
  containers:
    - name: testkubecont
      image: "kubeshop/testkube-cli"
      tty: true
	  command:
        - "sh"
      args:
        - "-c"
        - "while true; do sleep 3600; done"
      volumeMounts:
        - name: kubeconfig
          mountPath: /root/.kube
          subPath: config
  volumes:
    - name: kubeconfig
      secret:
        secretName: kubeconfigcred
"""
        }
    }
    
    environment {
        TK_NAMESPACE = 'default'
        TK_VERSION = '1.16.7'
        KUBECONFIG = '/root/.kube/config'
    }
    
    stages {
        stage('Setup Testkube') {
            steps {
                script {
				
					// Print environment variables for debugging
                    echo "KUBECONFIG: ${KUBECONFIG}"
                    echo "TK_NAMESPACE: ${TK_NAMESPACE}"
                    echo "TK_VERSION: ${TK_VERSION}"
                    
                    // Check Kubernetes context and configurations
                    echo "Checking kubectl configuration..."
                    sh 'kubectl config view'
					
					echo "Running Testkube command..."
                    // withEnv(["PATH+TESTKUBE=/usr/local/bin"]) {
                        sh """
                            kubectl-testkube run test Priya
							# Keeping container alive after running the test
							tail -f /dev/null
                        """
                   // }
                }
            }
        }
    }
}
