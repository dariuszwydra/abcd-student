pipeline {
    agent any
    options {
        skipDefaultCheckout(true)
    }
    stages {
        stage('Code checkout from GitHub') {
            steps {
                script {
                    cleanWs()
                    git credentialsId: 'github-token', url: 'https://github.com/dariuszwydra/abcd-student', branch: 'main'
                }
            }
        }
        // stage('ZAP Scan') {
        //     steps {
        //         sh '''
        //             docker run --name zap \
        //             -v C:/Users/dnw/Desktop/ABCDevSecOps/abcd-student/.zap:/zap/wrk:rw \
        //             -v C:/Users/dnw/Desktop/ABCDevSecOps/reports:/zap/wrk/reports \
        //             -t ghcr.io/zaproxy/zaproxy:stable bash -c \\
        //             "zap.sh -cmd -addonupdate && \\
        //             zap.sh -cmd -addoninstall communityScripts && \\
        //             zap.sh -cmd -addoninstall pscanrulesAlpha && \\
        //             zap.sh -cmd -addoninstall pscanrulesBeta && \\
        //             zap.sh -cmd -autorun /zap/wrk/passive.yaml"
        //         '''
        //     }
        // }
        // stage('OSV-scanner Scan') {
        //     steps {
        //         sh '''
        //             osv-scanner --lockfile package-lock.json --format json --output osvscanner-report.json
        //         '''
        //     }
        // }
        stage('Trufflehog Scan') {
            steps {
                sh '''
                    trufflehog git file://. --branch main --json
                '''
            }
        }
    }
    post {
        always {
            sh '''
                echo "POST Job"
                '''
            // defectDojoPublisher(
            //     artifact: '${WORKSPACE}/trufflehog_results.json', 
            //     productName: 'Juice Shop', 
            //     scanType: 'Trufflehog Scan', 
            //     engagementName: 'dariusz.wydra@dsr.com.pl'
            // )
        }
    }
}

