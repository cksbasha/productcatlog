pipeline {

  environment {
    PROJECT = "my-project-600-339318"
    APP_NAME = "product"
    FE_SVC_NAME = "${APP_NAME}-frontend"
    CLUSTER = "iphone"
    CLUSTER_ZONE = "us-central1-c"
    IMAGE_TAG = "gcr.io/${PROJECT}/${APP_NAME}:${env.BRANCH_NAME}.${env.BUILD_NUMBER}"
    JENKINS_CRED = "${PROJECT}"
  }

  agent {
    kubernetes {
      label 'sample-app'
      defaultContainer 'jnlp'
      yaml """
apiVersion: v1
kind: Pod
metadata:
labels:
  component: ci
spec:
  # Use service account that can deploy to all namespaces
  # serviceAccountName: cd-jenkins
  containers:
  - name: golang
    image: golang:1.10
    command:
    - cat
    tty: true
  - name: gcloud
    image: gcr.io/cloud-builders/gcloud
    command:
    - cat
    tty: true
  - name: kubectl
    image: gcr.io/cloud-builders/kubectl
    command:
    - cat
    tty: true
"""
}
  }
  stages {
    stage('build') {
      steps {
        container('golang') {
          sh """
            ln -s `pwd` 
          """
        }
      }
    }
    stage('Build and push image with Container Builder') {
      steps {
        container('gcloud') {
          sh "PYTHONUNBUFFERED=1 gcloud builds submit -t ${IMAGE_TAG} ."
        }
      }
    }
    stage('Deploy Dev') {
      steps {
        container('kubectl') {
          sh "gcloud container clusters get-credentials iphone --zone us-central1-c --project my-project-600-339318"
          sh "kubectl apply -f Deployment.yaml"
          sh "kubectl apply -f Service.yaml"
        }
      }
    }
  }
}
