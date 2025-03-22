pipeline {
    agent {
        kubernetes {
            label 'testkube-agent'
            yaml """
apiVersion: v1
kind: Pod
metadata:
  name: testkube-agent-pod
  labels:
    testkube: agent
spec:
  containers:
    - name: testkube
      image: "kubeshop/testkube-cli"
      tty: true
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
        TK_NAMESPACE = 'testkube'
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
