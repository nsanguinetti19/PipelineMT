pipeline {
    agent any

    stages {
        stage('Update') {
			environment {
				GXPROGRAMDIR = credentials('GXPROGRAMDIR')
				SERVERUSER = credentials('MTSERVERUSER')
				SERVERPASS = credentials('MTSERVERPASS')
				KBDIR = credentials('MTKBDir')
			}
            steps {
                echo '----- Update from GXServer -----'
				build job:'Update', parameters: [text(name: 'GXPROGRAMDIR', value: "${GXPROGRAMDIR}"),text(name: 'SERVERUSER', value: "${SERVERUSER}"),text(name: 'SERVERPASS', value: "${SERVERPASS}"),text(name: 'KBDIR', value: "${KBDIR}"),text(name: 'KBVERSION', value: "MT15")]
			}
        }
		stage('Build') {
			environment {
				GXPROGRAMDIR = credentials('GXPROGRAMDIR')
				SERVERUSER = credentials('MTSERVERUSER')
				SERVERPASS = credentials('MTSERVERPASS')
				KBDIR = credentials('MTKBDir')
			}
            steps {
                echo '----- Building -----'
				build job:'Build', parameters: [text(name: 'GXPROGRAMDIR', value: "${GXPROGRAMDIR}"),text(name: 'SERVERUSER', value: "${SERVERUSER}"),text(name: 'SERVERPASS', value: "${SERVERPASS}"),text(name: 'KBDIR', value: "${KBDIR}"),text(name: 'KBVERSION', value: "MT15")]
            }
        }
		stage('Validaciones') {
			parallel {
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
		stage('Clean Beta'){
			environment {
				MTDir = credentials('MTTMDir')
				MTUser = credentials('MTBetaIISUser')
			}
			steps {
				build job: 'Clean', parameters: [text(name: 'Directorio', value: "${MTDir}"),  text(name: 'Usuario', value: "${MTUser}"), text(name: 'ComputerName', value: "EVA")]
			}
		}
        stage('Deploy') {
			environment {
				KBDir = credentials('MTKBDir')
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
						echo 'No existe el ambiente de TA aún.'
					}
				}
				stage('Deploy Beta') {
					steps {
						echo 'Deploying...'
						build job: 'Deploy', parameters: [text(name: 'DeployOrigen', value: "${KBDir}\\Pro\\web"), text(name: 'DeployDestino', value: "${TMDir}")]
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