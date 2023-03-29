podTemplate(yaml: '''
    apiVersion: v1
      kind: Pod
      spec:
        containers:
           - name: centos
             image: centos
             command:
             - sleep
             args:
                - 99d
           - name: gradle
              image: gradle:jdk8
              command:
               - sleep
              args:
                 - 99d
            - name: cloud-sdk
              image: google/cloud-sdk
              command:
                - sleep
              args:
                - 9999999
              volumeMounts:
                 - name: shared-storage
                  mountPath: /mnt
                - name: google-cloud-key
                   mountPath: /var/secrets/google
              env:
               - name: GOOGLE_APPLICATION_CREDENTIALS
                 value: /var/secrets/google/uml-devops-03fc6f57f6c6.json
                 restartPolicy: Never
                 volumes:
                - name: shared-storage
                    persistentVolumeClaim:
                      claimName: jenkins-pv-claim
                - name: google-cloud-key
                    secret:
                      secretName: sdk-key
''') {
    node(POD_LABEL) {
    stage('Kubernetes on Centos container') {
    git 'https://github.com/slykmh/Continuous-Delivery-with-Docker-and-Jenkins-Second-Edition.git'
    container('centos') {
      stage('start calculator') {
        sh '''
        cd Chapter08/sample1
        curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
        chmod +x ./kubectl
        ./kubectl get pods -n staging
        ./kubectl get pods
        ./kubectl apply -f calculator.yaml -n staging
        ./kubectl apply -f hazelcast.yaml -n staging
        sleep 120
        ./kubectl get pods -n staging
        ./kubectl get pods
        '''
        }
      }
    }
  }
}
