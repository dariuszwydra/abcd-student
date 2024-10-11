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
        stage('ZAP Scan') {
            steps {
                sh '''
                    docker run --name zap \
                    -v C:/Users/dnw/Desktop/ABCDevSecOps/abcd-student/.zap:/zap/wrk:rw \
                    -v C:/Users/dnw/Desktop/ABCDevSecOps/reports:/zap/wrk/reports \
                    -t ghcr.io/zaproxy/zaproxy:stable bash -c \\
                    "zap.sh -cmd -addonupdate && \\
                    zap.sh -cmd -addoninstall communityScripts && \\
                    zap.sh -cmd -addoninstall pscanrulesAlpha && \\
                    zap.sh -cmd -addoninstall pscanrulesBeta && \\
                    zap.sh -cmd -autorun /zap/wrk/passive.yaml"
                '''
            }
        }
    }
    post {
        always {
            sh '''
                mkdir -p ${WORKSPACE}/results/
                docker cp zap:/zap/wrk/reports/zap_html_report.html ${WORKSPACE}/results/zap_html_report.html
                docker cp zap:/zap/wrk/reports/zap_xml_report.xml ${WORKSPACE}/results/zap_xml_report.xml
                docker stop zap juice-shop
                docker rm zap
                '''
            defectDojoPublisher(
                artifact: '${WORKSPACE}/results/zap_xml_report.xml', 
                productName: 'Juice Shop', 
                scanType: 'ZAP Scan', 
                engagementName: 'dariusz.wydra@dsr.com.pl'
            )
        }
    }
}

