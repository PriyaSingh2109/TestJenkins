pipeline {
    agent {
        kubernetes {
            label 'testkube-agent'  // The label used for this pod template
            yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    testkube: agent
spec:
  containers:
    - name: testkube
      image: "kubeshop/testkube-cli"  // Use an image with kubectl and Testkube installed
      command:
        - cat
      tty: true
      volumeMounts:
        - name: kubeconfig
          mountPath: /root/.kube
          subPath: config
  volumes:
    - name: kubeconfig
      secret:
        secretName: kubeconfigcred  // Your Kubernetes secret with the kubeconfig file
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
                    withEnv(["PATH+TESTKUBE=/usr/local/bin"]) {
                        sh """
                            echo \$KUBECONFIG
                            kubectl config view
                            kubectl-testkube run test Priya
                        """
                    }
                }
            }
        }
    }
}
