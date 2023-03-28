podTemplate(yaml: '''
    apiVersion: v1
    kind: Pod
    spec:
      containers:
      - name: gradle
        image: gradle:8-jdk8
        command:
        - sleep
        args:
        - 99d
        volumeMounts:
        - name: shared-storage
          mountPath: /mnt        
      - name: centos
        image: centos
        command:
        - sleep
        args:
        - 99d
      restartPolicy: Never
      volumes:
      - name: shared-storage
        persistentVolumeClaim:
          claimName: jenkins-pv-claim
      - name: kaniko-secret
        secret:
            secretName: dockercred
            items:
            - key: .dockerconfigjson
              path: config.json
''') {
node(POD_LABEL) {
    stage('pre upgrade testing'){
          try {
                sh '''
                test $(curl calculator-service:8080/sum?a=6\\&b=2) -eq 8 && echo 'pass' || 'fail'
                test $(curl calculator-service:8080/div?a=6\\&b=2) -eq 3 && echo 'pass' || 'fail'
                test $(curl calculator-service:8080/div?a=6\\&b=0) -eq 0 && echo 'pass' || 'fail'
                '''
              } catch (Exception E) {
                  echo 'Failure detected'
          }
    stage('Kubernetes on gradle container') {
    git 'https://github.com/slykmh/Continuous-Delivery-with-Docker-and-Jenkins-Second-Edition.git'
    container('gradle') {
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
    stage('post upgrade testing'){
          try {
                sh '''
                test $(curl calculator-service:8080/sum?a=6\\&b=2) -eq 8 && echo 'pass' || 'fail'
                test $(curl calculator-service:8080/div?a=6\\&b=2) -eq 3 && echo 'pass' || 'fail'
                test $(curl calculator-service:8080/div?a=6\\&b=0) -eq 0 && echo 'pass' || 'fail'
                '''
              } catch (Exception E) {
                  echo 'Failure detected'
          }
    }
  }
}
}
