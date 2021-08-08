pipeline{
    agent any
    stages{
        stage('DeployToProduction'){
            steps{
                kubernetesDeploy(
                  kubeconfigId: 'kubeconfig',
                  configs: 'loadbalancer-service.yml',
                  enableConfigSubstitution: true  
                )
            }
        }
    }
    post{
        always{
            echo "========always========"
        }
        success{
            echo "========pipeline executed successfully ========"
        }
        failure{
            echo "========pipeline execution failed========"
        }
    }
}