def AGENT_LABEL = 'api_auto_aws_B'
    
def runCaseOnLinux(Node, remoteIP='') {
    node(Node) {
        timeout(time: 60, unit: 'MINUTES') {
                   
        stage('Cleanup') {
            sh "sudo rm -rf *"
        }

        stage("Job creation") {
            script {
                if (params.Environment == "prod") {
                    currentBuild.displayName = "#${BUILD_NUMBER} - Env: ${params.Environment} | Reg: ${params.Region} | Suite: ${params.Suite} | Test: ${params.Test} | Branch: ${params.Branch} | Browser: ${params.Browser}"
                } else {
                    currentBuild.displayName = "#${BUILD_NUMBER} - Env: ${params.Environment} | Suite: ${params.Suite} | Test: ${params.Test} | Branch: ${params.Branch} | Browser: ${params.Browser} | Publish Test Rail: ${params.PUBLISH_TESTRAIL}"
                }

                retry(count: 3) {
                    checkout scm
                    //sh 'docker build -t cypress:pipeline .'
                }
            }
        }
        stage("Checkout Tools repo") {
            sh 'mkdir -p Tools'
            dir("Tools"){
                checkout scmGit(branches: [[name: 'master']],
                    extensions: [[$class: 'CloneOption',
                                        shallow: true,
                                        noTags: false,
                                        reference: '',
                                        depth: 1]],
                    userRemoteConfigs: [[credentialsId: 'GitCredentials', url: 'https://github.infra.int.daas-watchguard.com/Team-QA/Tools.git']])
            }        
        }

        stage("Test and Reports") {
            script {
                try {
                    withCredentials([
                        usernamePassword(credentialsId: 'AUTHPOINT_CY_QA', usernameVariable: 'AUTHPOINT_QA_USR', passwordVariable: 'AUTHPOINT_QA_PSW'),
                        usernamePassword(credentialsId: 'Cypress_XDR_UI_Automation_QA', usernameVariable: 'XDR_QA_USR', passwordVariable: 'XDR_QA_PSW'),
                        usernamePassword(credentialsId: 'AUTHPOINT_CY_QA_REGRESSION', usernameVariable: 'AUTHPOINT_QA_USR_REGRESSION', passwordVariable: 'AUTHPOINT_QA_PSW_REGRESSION'),
                        usernamePassword(credentialsId: 'PLATFORM_CY_QA', usernameVariable: 'PLATFORM_QA_USR', passwordVariable: 'PLATFORM_QA_PSW'),
                        usernamePassword(credentialsId: 'AUTHPOINT_CY_STG', usernameVariable: 'AUTHPOINT_STG_USR', passwordVariable: 'AUTHPOINT_STG_PSW'),
                        usernamePassword(credentialsId: 'PLATFORM_CY_STG', usernameVariable: 'PLATFORM_STG_USR', passwordVariable: 'PLATFORM_STG_PSW'),
                        usernamePassword(credentialsId: 'AUTHPOINT_CY_JPN', usernameVariable: 'AUTHPOINT_JPN_USR', passwordVariable: 'AUTHPOINT_JPN_PSW'),
                        usernamePassword(credentialsId: 'AUTHPOINT_CY_DEU', usernameVariable: 'AUTHPOINT_DEU_USR', passwordVariable: 'AUTHPOINT_DEU_PSW'),
                        usernamePassword(credentialsId: 'AUTHPOINT_CY_USA', usernameVariable: 'AUTHPOINT_USA_USR', passwordVariable: 'AUTHPOINT_USA_PSW'),
                        usernamePassword(credentialsId: 'PLATFORM_CY_JPN', usernameVariable: 'PLATFORM_JPN_USR', passwordVariable: 'PLATFORM_JPN_PSW'),
                        usernamePassword(credentialsId: 'PLATFORM_CY_DEU', usernameVariable: 'PLATFORM_DEU_USR', passwordVariable: 'PLATFORM_DEU_PSW'),
                        usernamePassword(credentialsId: 'PLATFORM_CY_USA', usernameVariable: 'PLATFORM_USA_USR', passwordVariable: 'PLATFORM_USA_PSW'),
                        usernamePassword(credentialsId: 'QA_PARTNER_JPN', usernameVariable: 'QA_PARTNER_JPN_USR', passwordVariable: 'QA_PARTNER_JPN_PSW'),
                        usernamePassword(credentialsId: 'QA_PARTNER_DEU', usernameVariable: 'QA_PARTNER_DEU_USR', passwordVariable: 'QA_PARTNER_DEU_PSW'),
                        usernamePassword(credentialsId: 'QA_PARTNER_USA', usernameVariable: 'QA_PARTNER_USA_USR', passwordVariable: 'QA_PARTNER_USA_PSW'),
                        usernamePassword(credentialsId: 'FIRECLOUD_CY_QA', usernameVariable: 'FIRECLOUD_QA_USR', passwordVariable: 'FIRECLOUD_QA_PSW'),
                        usernamePassword(credentialsId: 'XDR_SP_CY_QA', usernameVariable: 'XDR_SP_QA_USR', passwordVariable: 'XDR_SP_QA_PSW'),
                        string(credentialsId: 'CONNECTWISE_COMPANY_ID', variable: 'CONNECTWISE_COMPANY_ID'),
                        string(credentialsId: 'CONNECTWISE_PUBLIC_KEY', variable: 'CONNECTWISE_PUBLIC_KEY'),
                        string(credentialsId: 'CONNECTWISE_PRIVATE_KEY', variable: 'CONNECTWISE_PRIVATE_KEY'),
                        string(credentialsId: 'CONNECTWISE_SITE', variable: 'CONNECTWISE_SITE'),
                        string(credentialsId: 'AUTOTASK_USER_NAME', variable: 'AUTOTASK_USER_NAME'),
                        string(credentialsId: 'AUTOTASK_USER_SECRET', variable: 'AUTOTASK_USER_SECRET'),
                    ]) {
                        withEnv(["Environment=${params.Environment}","Region=${params.Region}","Suite=${params.Suite}","Test=${params.Test}","Browser=${params.Browser}"]) {
                            sh '''
                                docker run --ipc=host \
                                -t --name Cypress-Tests-${BUILD_NUMBER} \
                                -w /Cypress-Tests \
                                -v $WORKSPACE:/Cypress-Tests cypress:pipeline bash /Cypress-Tests/cy.sh \
                                "$Environment" \
                                "$Region" \
                                "$Suite" \
                                "$Test" \
                                "$Browser" \
                                "$AUTHPOINT_QA_USR" "$AUTHPOINT_QA_PSW" \
                                "$AUTHPOINT_QA_USR_REGRESSION" "$AUTHPOINT_QA_PSW_REGRESSION" \
                                "$AUTHPOINT_STG_USR" "$AUTHPOINT_STG_PSW" \
                                "$AUTHPOINT_JPN_USR" "$AUTHPOINT_JPN_PSW" \
                                "$AUTHPOINT_DEU_USR" "$AUTHPOINT_DEU_PSW" \
                                "$AUTHPOINT_USA_USR" "$AUTHPOINT_USA_PSW" \
                                "$XDR_QA_USR" "$XDR_QA_PSW" \
                                "$PLATFORM_QA_USR" "$PLATFORM_QA_PSW" \
                                "$CONNECTWISE_COMPANY_ID" "$CONNECTWISE_PUBLIC_KEY" "$CONNECTWISE_PRIVATE_KEY" "$CONNECTWISE_SITE" \
                                "$AUTOTASK_USER_NAME" "$AUTOTASK_USER_SECRET" \
                                "$PLATFORM_STG_USR" "$PLATFORM_STG_PSW" \
                                "$PLATFORM_JPN_USR" "$PLATFORM_JPN_PSW" \
                                "$PLATFORM_DEU_USR" "$PLATFORM_DEU_PSW" \
                                "$PLATFORM_USA_USR" "$PLATFORM_USA_PSW" \
                                "$QA_PARTNER_JPN_USR" "$QA_PARTNER_JPN_PSW" \
                                "$QA_PARTNER_DEU_USR" "$QA_PARTNER_DEU_PSW" \
                                "$QA_PARTNER_USA_USR" "$QA_PARTNER_USA_PSW" \
                                "$FIRECLOUD_QA_USR" "$FIRECLOUD_QA_PSW" \
                                "$XDR_SP_QA_USR" "$XDR_SP_QA_PSW"

                            '''
                        }
                    }
                } finally {
                    sh "docker rm -f Cypress-Tests-${BUILD_NUMBER}"
                    try {
                        sh "if [ -d allure-results ] && [ -n \"\$(ls -A allure-results 2>/dev/null)\" ]; then npm run cypress:report; else true; fi"
                        archiveArtifacts artifacts: "target/*.xml,allure*/*.png"
                    } catch(e) {
                        println e
                    }
                    try {
                        sh 'sudo chmod -R o+xw allure-results 2>/dev/null || true'
                        sh 'sudo chmod -R o+xw target 2>/dev/null || true'
                        sh 'sudo chmod -R o+xw allure-report 2>/dev/null || true'
                        allure report: 'allure-reports', results: [[path: 'allure*']]
                    } catch(e) {
                        println e
                    }
                }
            }
        }

        stage('Publish JUnit Results') {
            junit allowEmptyResults: true, testResults: 'target/*.xml'
        }

        stage('Publish Results to TestRail') {
            script {
                if ( "${params.PUBLISH_TESTRAIL}" == "true" ) {

                    withCredentials([string(credentialsId: 'TESTRAIL_API', variable: 'TESTRAIL_API')]) {
                        def python_env = '/usr/bin/python3.11'
                        withPythonEnv(python_env) {
                            sh 'python --version'
                            sh 'pip install trcli'
                            echo "Generate testRail result"
                            sh './Tools/postman_tools/testrail_integration/summarize_junit.sh target/ testrail_results.xml CYPRESS'

                            SUITE="SMOKE_TEST"
                        //  if ( "${params.Suite}" == "platform_regression" || "${params.Suite}" == "sub_authpoint_regression" ){
                        //  SUITE="REGRESSION_TEST"}                            

                            echo "Identify Test Suite: python3 Tools/postman_tools/testrail_integration/testrail_mapping.py TESTSUITE UI ${SUITE} ${params.Environment} ${params.Region}"
                            TESTRAIL_TESTSUITE = sh (
                                script: "python3 Tools/postman_tools/testrail_integration/testrail_mapping.py TESTSUITE UI ${SUITE} ${params.Environment} ${params.Region}",
                                returnStdout: true).trim()

                            echo "Identify Test Run: python3 Tools/postman_tools/testrail_integration/testrail_mapping.py TESTRUN UI ${SUITE} ${params.Environment} ${params.Region}"
                            TESTRAIL_TESTRUN = sh (
                                script: "python3 Tools/postman_tools/testrail_integration/testrail_mapping.py TESTRUN UI ${SUITE} ${params.Environment} ${params.Region}",
                                returnStdout: true).trim()

                            echo "Identify Milestone: python3 Tools/postman_tools/testrail_integration/testrail_mapping.py MILESTONE UI ${SUITE} ${params.Environment} ${params.Region}"
                            TESTRAIL_MILESTONE = sh (
                                script: "python3 Tools/postman_tools/testrail_integration/testrail_mapping.py MILESTONE UI ${SUITE} ${params.Environment} ${params.Region}",
                                returnStdout: true).trim()

                            echo "Upload the file to TestRail with suite id ${TESTRAIL_TESTSUITE} and run id ${TESTRAIL_TESTRUN}"
                            withEnv(["TESTRAIL_TESTSUITE=${TESTRAIL_TESTSUITE}",
                                    "TESTRAIL_TESTRUN=${TESTRAIL_TESTRUN}",
                                    "TEST_SUITE=${SUITE}",
                                    "TEST_ENV=${params.Environment}",
                                    "TEST_REGION=${params.Region}",
                                    "TESTRAIL_MILESTONE=${TESTRAIL_MILESTONE}"]){
                                sh 'trcli -y \
                                         -h https://watchguardqa.testrail.io \
                                         --project "WGC Automation" \
                                         --username wats-ng-dev@watchguard.com \
                                         -k ${TESTRAIL_API} \
                                         parse_junit --title "UI - ${TEST_SUITE} - ${TEST_ENV} - ${TEST_REGION}" \
                                         -f testrail_results.xml \
                                         --suite-id "${TESTRAIL_TESTSUITE}" \
                                         --run-id "${TESTRAIL_TESTRUN}" \
                                         --milestone-id "${TESTRAIL_MILESTONE}" '
                            }
                        }
                    }
                }
            }
        }

        stage('Publish Email Report') {
            sh 'sudo chmod -R o+w target || true'
            sh """
                python3 custom_job/generate_email_template.py \\
                    --junit-dir target \\
                    --output target/email_report.html \\
                    --current-summary target/results_summary.json \\
                    --feature "Cypress Smoke ${params.Suite}-${params.Test}" \\
                    --job-name ${env.JOB_NAME} \\
                    --build-number ${env.BUILD_NUMBER} \\
                    --build-url ${env.BUILD_URL}
            """
            archiveArtifacts artifacts: "target/results_summary.json,target/email_report.html", fingerprint: true
            publishHTML([
                allowMissing: false,
                alwaysLinkToLastBuild: true,
                keepAll: true,
                reportDir: 'target',
                reportFiles: 'email_report.html',
                reportName: '📊 Cypress Summary Report 📊'
            ])
        }
    }
}
}
runCaseOnLinux(AGENT_LABEL)