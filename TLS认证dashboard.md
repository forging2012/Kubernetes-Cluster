��������yaml�ļ�,��˵�����Ŀ�
[��ת������yaml�ļ�](yaml/dashboard)
#### ��һ�� ����RBRC��Ȩ,����Ҫ��һ������,��Ȼ����Ҳ����

    [root@k8s-1 dashboard]# cat rbac.yml
    apiVersion: v1
    kind: ServiceAccount
    metadata:
      labels:
        k8s-app: kubernetes-dashboard
      name: dashboard
      namespace: kube-system
    ---
    apiVersion: rbac.authorization.k8s.io/v1beta1
    kind: ClusterRoleBinding
    metadata:
      name: dashboard
      labels:
        k8s-app: dashboard
    roleRef:
      apiGroup: rbac.authorization.k8s.io
      kind: ClusterRole
      name: dashboard
    subjects:
    - kind: ServiceAccount
      name: dashboard
      #����һ����Ϊdashboard��ServiceAccount,Ȼ���ClusterRole��,���򱨴�����"ERROR-001"
      namespace: kube-system


##### "ERROR-001":

    "kube-system/kubernetes-dashboard-2689983591" failed with unable to create pods: pods "kubernetes-dashboard-2689983591-" is forbidden: service account kube-system/dashboard was not found, retry after the service account is created


#### �ڶ��� ����service

    [root@k8s-1 dashboard]# cat dashboard-service.yaml
    apiVersion: v1
    kind: Service
    metadata:
      name: kubernetes-dashboard
      namespace: kube-system
      labels:
        k8s-app: kubernetes-dashboard
        kubernetes.io/cluster-service: "true"
        addonmanager.kubernetes.io/mode: Reconcile
    spec:
      type: NodePort
      selector:
        k8s-app: kubernetes-dashboard
      ports:
      - port: 80
        targetPort: 9090
        nodePort: 31100


#### ������ ����dashboard-Deployment

* �ο�https://raw.githubusercontent.com/kubernetes/dashboard/master/src/deploy/kubernetes-dashboard.yaml


    [root@k8s-1 dashboard]# cat dashboard-controller.yaml
    apiVersion: extensions/v1beta1
    kind: Deployment
    metadata:
      name: kubernetes-dashboard
      namespace: kube-system
      labels:
        k8s-app: kubernetes-dashboard
        kubernetes.io/cluster-service: "true"
        addonmanager.kubernetes.io/mode: Reconcile
    spec:
      selector:
        matchLabels:
          k8s-app: kubernetes-dashboard
      template:
        metadata:
          labels:
            k8s-app: kubernetes-dashboard
          annotations:
            scheduler.alpha.kubernetes.io/critical-pod: ''
        spec:
          volumes:
          - hostPath:
             path: /etc/kubernetes
            name: ssl-certs-kubernetess
            #����������Ŀ¼ӳ��Ϊ��,����ĸ�·������,ֻҪ·�����������config2�ļ�����
          - hostPath:
             path: /etc/hosts
            name: ssl-certs-hosts
            #��hosts�ļ�ӳ�䵽������,������ܻ��Ҳ���apiserver�ĵ�ַ
          serviceAccountName: dashboard
          containers:
          - name: kubernetes-dashboard
            image: index.tenxcloud.com/jimmy/kubernetes-dashboard-amd64:v1.6.0
            imagePullPolicy: IfNotPresent
            args:
             - --kubeconfig=/etc/kubernetes/config2
             #��ӵľ��������,���Ϻܶ೭Ϯ������,������������û��,�治֪����զ������,��ǰ��http��ʱ��ʹ�õ�--apiserver-host����,�˴���Ϊʹ����https,���Ը���--kubeconfig����,����ļ������ɷ�ʽ������"METHOD001"
            volumeMounts:
             - mountPath: /etc/kubernetes
               name: ssl-certs-kubernetess
               readOnly: true
               #���ؾ�
             - mountPath: /etc/hosts
               name: ssl-certs-hosts
               readOnly: true
               #����hosts
            resources:
              limits:
                cpu: 100m
                memory: 50Mi
              requests:
                cpu: 100m
                memory: 50Mi
            ports:
            - containerPort: 9090
            livenessProbe:
              httpGet:
                path: /
                port: 9090
              initialDelaySeconds: 30
              timeoutSeconds: 30
          tolerations:
          - key: "CriticalAddonsOnly"
            operator: "Exists"

##### ����kubectl kubeconfig�ļ�,���ļ�����kubectl �������,

* Ĭ������·��Ϊ~/.kube/config,Ҳ��������dashboard��https��֤,ֱ�ӿ���ʹ��,����ֱ�ӿ�����/etc/kubernetes/config2,Ȼ����ص�����������Ϊdashboard����������ʹ�õ�. �� --kubeconfig=/etc/kubernetes/config2


    export KUBE_APISERVER="https://k8s-2:6443"
    $ # ���ü�Ⱥ����
    $ kubectl config set-cluster kubernetes \
    --certificate-authority=/etc/kubernetes/ssl/ca.pem \
    --embed-certs=true \
    --server=${KUBE_APISERVER}
    $ # ���ÿͻ�����֤����
    $ kubectl config set-credentials admin \
    --client-certificate=/etc/kubernetes/ssl/admin.pem \
    --embed-certs=true \
    --client-key=/etc/kubernetes/ssl/admin-key.pem
    $ # ���������Ĳ���
    $ kubectl config set-context kubernetes \
    --cluster=kubernetes \
    --user=admin
    $ # ����Ĭ��������
    $ kubectl config use-context kubernetes
