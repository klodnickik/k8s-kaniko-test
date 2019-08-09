/**
 * This pipeline will build and deploy a Docker image with Kaniko
 * https://github.com/GoogleContainerTools/kaniko
 * without needing a Docker host
 *
 * You need to create a secret with your registry credentials as described in
 * https://github.com/GoogleContainerTools/kaniko#kubernetes-secret
 * kubectl create secret generic kaniko-secret --from-file=kaniko-secret.json
 */

pipeline {
  agent {
    kubernetes {
      label 'k8s-kaniko'
      yaml """
kind: Pod
metadata:
  name: kaniko
spec:
  containers:
  - name: kaniko
    image: gcr.io/kaniko-project/executor:debug
    imagePullPolicy: Always
    command: ["cat"]
    tty: true
    volumeMounts:
      - name: kaniko-secret
        mountPath: /secret
    env:
      - name: GOOGLE_APPLICATION_CREDENTIALS
        value: /secret/kaniko-secret.json
  volumes:
    - name: kaniko-secret
      secret:
        secretName: kaniko-secret
"""
    }
  }
  stages {
    stage('Create kaniko job') {
      steps {
        container(name: 'kaniko', shell: '/busybox/sh'){
          sh '''#!/busybox/sh -xe
          /kaniko/executor -f `pwd`/Dockerfile -c `pwd` --cache=true --destination=eu.gcr.io/krzysiek-master-project/kaniko-test-jenkins:20190809
          '''
        }
      }
    }
  }
}
