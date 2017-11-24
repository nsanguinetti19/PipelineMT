pipeline {
    agent any

    stages {
        stage('Update') {
            steps {
                echo '----- Update from GXServer -----'
				build 'Update'
            }
        }
		stage('Build') {
		    when {
              expression {
                currentBuild.result == null || currentBuild.result == 'SUCCESS' 
              }
            }
            steps {
                echo '----- Building MT -----'
				build 'Build'
            }
        }
		stage('Validaciones') {
			parallel {
				stage('Comparar Navegaciones') {
					steps {
						echo '----- Comparo Navegaciones -----'
					}
				}
				stage('Test Unitario') {
					steps {
						echo '----- Testing.. -----'
					}
				}
				stage('Comparar API') {
					steps {
						echo '----- Comparo API -----'
					}
				}
			}
		}
        stage('Deploy') {
			environment {
				KBDir = credentials('MTKBDir')
				TADir = credentials('MTTADir')
				TMDir = credentials('MTTMDir')
				BetaBatchDir = credentials('MTBetaBatchDir') 
				KBBatchDir = credentials('MTBatchKBDir')
			}
            when {
              expression {
                currentBuild.result == null || currentBuild.result == 'SUCCESS' 
              }
            }
			parallel {
				stage('Deploy TA') {
					steps {
						echo 'Deploying...'
						build job: 'Deploy', parameters: [text(name: 'DeployOrigen', value: "${KBDir}"), text(name: 'DeployDestino', value: "${TADir}")]
					}
				}
				stage('Deploy Beta') {
					steps {
						echo 'Deploying...'
						build job: 'Deploy', parameters: [text(name: 'DeployOrigen', value: "${KBDir}"), text(name: 'DeployDestino', value: "${TMDir}")]
					}
				}
				stage('Deploy BetaBatch') {
					steps {
						echo 'Deploying...'
						build job: 'Deploy', parameters: [text(name: 'DeployOrigen', value: "${KBBatchDir}"), text(name: 'DeployDestino', value: "${BetaBatchDir}")]
					}
				}
			}
        }
		stage('Testeo Integracion') {
			steps {
				echo '----- Testeo automático -----'
			}
		}
    }
}