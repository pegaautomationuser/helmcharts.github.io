#!/usr/bin/env groovy
node {
      stage("Init"){

            sh "curl -fsSL -o helm-v3.2.4-linux-amd64.tar.gz https://get.helm.sh/helm-v3.2.4-linux-amd64.tar.gz"
            sh "tar -zxvf helm-v3.2.4-linux-amd64.tar.gz"
            sh "mv linux-amd64/helm /usr/local/bin/helm"

      }
      stage ("Checkout and Package Charts") {

            // Checkout PR Code
            def scmVars = checkout scm
            // Publish helm charts to test-automation repository
            withCredentials([usernamePassword(credentialsId: "helmautomation",
              passwordVariable: 'AUTOMATION_APIKEY', usernameVariable: 'AUTOMATION_USERNAME')]) {
            // Perform Chart packaging
            sh "helm dependency update ./test/"
            sh "helm package --version 1.0.0 ./test/"
            sh "git clone https://pegaautomationuser:${AUTOMATION_APIKEY}@github.com/pegaautomationuser/helmcharts.git --branch=gh-pages gh-pages"
            sh "mv test-1.0.0.tgz gh-pages/"
            sh "cd gh-pages"
            sh "helm repo index --merge index.yaml --url https://pegaautomationuser.github.io/helmcharts/ ."
            sh "cat index.yaml"
            sh "git remote set-url origin https://pegaautomationuser:${AUTOMATION_APIKEY}@github.com/pegaautomationuser/helmcharts.git"
            sh "git config user.email pegaautomationuser@gmail.com"
            sh "git config user.name ${AUTOMATION_USERNAME}"
            sh "git status"
            sh "git add ."
            sh "git commit -m \"Jenkins build to publish test artefacts\""
            sh "git push -u origin gh-pages"
            } 
      }
  }