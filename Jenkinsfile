import org.jenkinsci.plugins.pipeline.modeldefinition.Utils
node {
    try {
        properties([
        parameters([
            credentials(credentialType: 'com.browserstack.automate.ci.jenkins.BrowserStackCredentials', defaultValue: '', description: 'Select your BrowserStack Username', name: 'BROWSERSTACK_USERNAME', required: true),
        ])
    ])

        stage('Pull code from Github') {
            dir('test') {
                git branch: 'webinar', changelog: false, poll: false, url: 'https://github.com/BrowserStackCE/browserstack-examples-appium-testng.git'
            }
        }

        stage('Run Test') {
            browserstack(credentialsId: "${params.BROWSERSTACK_USERNAME}") {
                def user = "${env.BROWSERSTACK_USERNAME}"
                if (user.contains('-')) {
                    user = user.substring(0, user.lastIndexOf('-'))
                }
                withEnv(['BROWSERSTACK_USERNAME=' + user]) {
                    sh label: '', returnStatus: true, script:'''#!/bin/bash -l
                cd test
                source ~/.bashrc
                nvm use 16
                npm install
                export PERCY_TOKEN==${PERCY_TOKEN}
                npx percy exec --verbose  --  mvn test -P bstack-parallel-regression
                '''
                }
            }
        }

        stage('Generate Report') {
            browserStackReportPublisher 'app-automate'
        }


    }
  catch (e) {
        currentBuild.result = 'FAILURE'
        echo e
        throw e
  }
}
