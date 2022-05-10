pipeline {
    agent any

     stages {
        stage('Get Code') {
            steps {
                // Obtener código del repo
                //git 'https://github.com/anieto-unir/helloworld.git'
		script {
			scmVars = checkout scm
			echo 'scm : the commit id is ' + scmVars.GIT_COMMIT
		}
            }
        }
        
        stage('Build') {
            steps {
                echo 'Eyyy, esto es Python. No hay que compilar nada!!!'
		echo 'El workspace contiene el commit \'' + scmVars.GIT_COMMIT + '\' de la rama \'' + scmVars.GIT_BRANCH + '\''
            }
        }
        
        stage('Tests') {
            parallel {
                stage('Unit') {
                    steps {
                        bat '''
                            export PYTHONPATH=${PYTHONPATH}:.
                            pytest --junitxml=result-unit.xml test/unit
                        '''
                    }
                }
                stage('Service') {
                    steps {
                        bat '''
                            export FLASK_APP=app/api.py
                            export FLASK_ENV=development
                            nohup flask run >/dev/null 2>&1
                            nohup java -jar /wiremock-jre8-standalone-2.33.1.jar --port 9090 --root-dir /var/tmp &
                            export PYTHONPATH=${PYTHONPATH}:.
                            pytest --junitxml=result-rest.xml test/rest
                        '''
                    }    
                }
            }
        }
        stage ('Results') {
            steps {
                junit 'result*.xml'
            }
        }
    }
}
