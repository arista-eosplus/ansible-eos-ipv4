#!/usr/bin/env groovy

/*
 * Jenkinsfile for ansible-eos-ipv4 role
 *
 * Run the Ansible-Role-Test job against a commit or
 * pull request in the ansible-eos-ipv4 repo
 */

node('vagrant') {

    currentBuild.result = "SUCCESS"

    try {

        stage ('Run tests for ansible-eos-ipv4 role') {

            build job: 'Ansible-Role-Test',
                  parameters: [string(name: 'ROLE_NAME', value: 'ansible-eos-ipv4')]

        }

        stage ('Generate email report') {

           mail body: "${env.BUILD_URL} build successful.\n" +
                      "Started by ${env.BUILD_CAUSE}",
                from: 'grybak@arista.com',
                replyTo: 'grybak@arista.com',
                subject: "ansible role test ${env.JOB_NAME} (${env.BUILD_NUMBER}) build successful",
                to: 'grybak@arista.com'

        }

    }

    catch (err) {

        currentBuild.result = "FAILURE"

            mail body: "${env.JOB_NAME} (${env.BUILD_NUMBER}) cookbook build error " +
                       "is here: ${env.BUILD_URL}\nStarted by ${env.BUILD_CAUSE}" ,
                 from: 'grybak@arista.com',
                 replyTo: 'grybak@arista.com',
                 subject: "ansible role test ${env.JOB_NAME} (${env.BUILD_NUMBER}) build failed",
                 to: 'grybak@arista.com'

            throw err
    }

}