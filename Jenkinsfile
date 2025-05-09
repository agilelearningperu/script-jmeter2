pipeline {
    agent any

    environment {
        JMETER_PATH = "/opt/jmeter/bin/jmeter"
        TEST_PLAN = "test/Performance_API2.jmx"
        RESULT_DIR = "results"
        REPORT_DIR = "reportes"
    }

    stages {
        stage('Preparar entorno') {
            steps {
                echo "Limpieza previa..."
                sh "rm -rf ${RESULT_DIR} ${REPORT_DIR} || true"
                sh "mkdir -p ${RESULT_DIR} ${REPORT_DIR}"
            }
        }

        stage('Ejecutar prueba JMeter') {
            steps {
                echo "Ejecutando prueba con JMeter..."
                sh """
                    ${JMETER_PATH} -n -t ${TEST_PLAN} -l ${RESULT_DIR}/resultados.jtl
                """
            }
        }

        stage('Generar reporte HTML') {
            steps {
                echo "Generando reporte HTML..."
                sh """
                    ${JMETER_PATH} -g ${RESULT_DIR}/resultados.jtl -o ${REPORT_DIR}
                """
            }
        }

        stage('Publicar artefactos') {
            steps {
                echo "Publicando artefactos..."
                archiveArtifacts artifacts: "${RESULT_DIR}/**/*.*", fingerprint: true
                archiveArtifacts artifacts: "${REPORT_DIR}/**/*.*", fingerprint: true
            }
        }
		
		stage('Publicar Reporte HTML') {
			steps {
				publishHTML(target: [
					reportDir: 'reportes', // carpeta donde JMeter generó el reporte
					reportFiles: 'index.html',
					reportName: 'Reporte de Performance',
					keepAll: true,
					alwaysLinkToLastBuild: true,
					allowMissing: false
				])
			}
		}
		stage('Publicar resultados en Performance Plugin') {
            steps {
                perfReport sourceDataFiles: 'results/resultados.jtl'
            }
        }

    }
}
