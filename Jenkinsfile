node {
    try {
        stage('Build') {
            // sh 'docker rm gallant_kare sleepy_sanderson'
            sh 'docker images'
            sh 'docker rmi cdrx/pyinstaller-linux:python2'
            throw e
            docker.image('python:2-alpine').inside {
                echo "Building Python files..."
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            }
        }

        stage('Test') {
            docker.image('qnib/pytest:latest').inside {
                echo "Running tests..."
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            }
            junit 'test-reports/results.xml'
        }

        stage('Deliver') {
            docker.image('cdrx/pyinstaller-linux:python2').inside {
                echo "Running PyInstaller..."
                sh 'pyinstaller --onefile sources/add2vals.py'
            }
            archiveArtifacts 'dist/add2vals'
        }

    } catch (Exception e) {
        // Print the error and log the container status
        echo "An error occurred: ${e.getMessage()}"
        
        // Check container status for debugging
        echo "Checking Docker container status..."
        try {
            def containers = sh(script: 'docker ps -a', returnStdout: true).trim()
            echo "Current Docker containers: ${containers}"
        } catch (dockerError) {
            echo "Failed to get Docker container status: ${dockerError.getMessage()}"
        }

        currentBuild.result = 'FAILURE'
        throw e // rethrow to mark the build as failed
    } finally {
        if (currentBuild.result == 'UNSTABLE') {
            // Skip remaining stages if the build is unstable
            currentBuild.result = 'SUCCESS'
        }
    }
}
