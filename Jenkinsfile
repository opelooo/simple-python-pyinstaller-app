node {
    try {
        stage('Build') {
            docker.image('python:2-alpine').inside {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            }
        }

        stage('Test') {
            docker.image('qnib/pytest:latest').inside {
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            }
            junit 'test-reports/results.xml'
        }

        stage('Deliver') {
            docker.image('cdrx/pyinstaller-linux:python2').inside {
                sh 'pyinstaller --onefile sources/add2vals.py'
            }
            archiveArtifacts 'dist/add2vals'
        }

    } catch (Exception e) {
        // Print log error when an exception happens
        echo "An error occurred: ${e.getMessage()}"
        currentBuild.result = 'FAILURE'
        throw e // rethrow to mark the build as failed
    }
}
