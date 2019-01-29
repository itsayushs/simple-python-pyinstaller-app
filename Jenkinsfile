pipeline {
    agent none
    stages {
        stage('Build') {
            agent {
                docker {
                    image 'python:3-alpine'
                }
            }
            steps {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            }
        }
        stage('Test') {
            agent {
                docker {
                    image 'python:pytest'
                }
            }
            steps {
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }
        stage('Analysis') {
            agent {
                docker {
                    image 'pylint:latest'
                }
            }
            steps {
            sh 'pylint --disable=W1202 --output-format=parseable --reports=no sources/test_calc.py > pylint.log || echo "pylint exited with $?"'
            sh 'cat pylint.log'
            step([
             $class                     : 'WarningsPublisher',
             parserConfigurations       : [[
                                              parserName: 'pyLint',
                                              pattern   : 'pylint.log'
                                      ]],
             unstableTotalAll           : '0',
             usePreviousBuildAsReference: true
             ])
            }
        }
    }
}

