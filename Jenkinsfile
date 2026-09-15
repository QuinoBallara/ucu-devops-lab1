// Starting point. Students grow this file module by module.
pipeline {
    agent any
 
    options {
        timestamps()
    }

    parameters {
        choice(name: 'ENTORNO', choices: ['dev', 'qa',  'prod'], description: 'Ambiente de despliegue'),
        string(name: 'VERSION', defaultValue: '1.0.0', description: 'Versión de la aplicación'),
        booleanParam(name: 'EJECUTAR_TESTS', defaultValue: true, description: 'Correr los test')
    }
 
    stages {
        stage('Instalar dependencias') {
            steps {
                sh '''
                    python3 -m venv .venv
                    . .venv/bin/activate
                    pip install --quiet -r requirements.txt
                '''
            }
        }
 
        stage('Lint') {
            steps {
                sh '''
                    . .venv/bin/activate
                    ruff check .
                '''
            }
        }
 
        stage('Test') {
            when {
                expression { params.EJECUTAR_TESTS}
            }
            steps {
                sh '''
                    . .venv/bin/activate
                    pytest --junitxml=reports/junit.xml
                '''
            }
        }

        stage('Aprobacion') {
            when {
                expression { params.ENTORNO == 'prod' }
            }
            steps {
                input message: "Desplegar la version ${params.VERSION} a PRODUCCION?", ok: 'Si, desplegar'
            }
        }
 
        stage('Deploy') {
            steps {
                echo "Desplegando ${params.VERSION} al ambiente ${params.ENTORNO}"
                sh 'echo "Entorno desde el shell: $ENTORNO, version $VERSION"'
            }
        }
    }
    }
 
    post {
        always {
            junit 'reports/junit.xml'
        }
    }
}