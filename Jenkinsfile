node {
    properties([
        pipelineTriggers([pollSCM('H/2 * * * *')]) // Memeriksa perubahan setiap 2 menit
    ])

    stage('Build') {
        checkout scm
        docker.image('python:2-alpine').inside {
            sh 'python -m py_compile ./sources/add2vals.py ./sources/calc.py'
        }
    }

    stage('Test') {
        docker.image('qnib/pytest:latest').inside {
            sh 'py.test --verbose --junit-xml test-reports/results.xml ./sources/test_calc.py'
        }
        junit 'test-reports/results.xml'
    }

    stage('Deliver') {
        docker.image('cdrx/pyinstaller-linux:python2').inside {
            sh 'pyinstaller --onefile ./sources/add2vals.py'
        }
        archiveArtifacts artifacts: 'dist/add2vals'
    }
}
