**k8s�ܹ�ͼ**

![k8s-cluster](pic/k8s-cluster.jpg)

������ʽ���:

**[Master]**
- scheduler:��Ⱥ�ֵĵ�����,����Pod�ڼ�Ⱥ�ֽڵ�ĵ��ȷ���
- controller-manager:��Ⱥ�ڲ��Ĺ����������,����ҪĿ����ʵ��Kubernetes��Ⱥ�Ĺ��ϼ��ͻָ��Զ�������
- api-server:�ṩkubernetes��Ⱥ��API����,Ϊ��Ⱥ��Դ�����Ψһ�������,�����������������ͨ�����ṩ��API��������Դ����,ͨ����������ݡ�ȫ����ѯ��+���仯������,��Щ������Ժ�ʵʱ�������ص�ҵ����.
- flannel: ʵ�ֿ����������������ͨ��
- etcd:һ���߿��õ�K/V��ֵ�Դ洢�ͷ�����ϵͳ,���ڳ־û��洢��Ⱥ�����е���Դ����,���缯Ⱥ�ֵ�Node,Service,Pod,RC,Namespace��,����master��Ⱥ,��Ⱥʹ��lease-lockƯ����ʵ��leaderѡ��(ԭ��:we are going to use a lease-lock in the API to perform master election)

**[Node]**:
- docker-daemon: docker
- proxy:ʵ��Service�Ĵ������ģʽ�ĸ��ؾ�����
- kubelet:���𱾽ڵ��ϵ�Pod�Ĵ���,�޸�,���,���ٵ�ȫ�������ڹ���,ͬ�¶�ʱ�ϱ����ڵ��״̬��Ϣ��API Server

��Ⱥ���ʽ���:

**Pod**:

��kubernetes������Ĳ�����Ԫ,һ��Pod���ܰ����������,��ͬPod�ڵ���������ͨ��localhost����ͨ��,���Ƕ˿ڲ�����ͬ,Pod��Node�ϱ�����,����������,Pod���ܿ�Node,Pod������Node֮��Ĺ�ϵ����ͼ:

 ![pod](pic/pod.png)

 һ��Pod�ֵ�Ӧ����������ͬһ����Դ:
- PID�����ռ�:Pod�ֵĲ���Ӧ�ó�����Կ�������Ӧ�ó���Ľ���ID
- ���������ռ�:Pod�ֵĶ�������ܹ�����ͬһ��IP�Ͷ˿ڷ�Χ
- IPC�����ռ�:Pod�ֵĶ��������ʹ��SystemV IPC��POSIX��Ϣ���н���ͨ��
- UTS�����ռ�:Pod�ֵĶ����������һ��������
- Volumes(����洢��):Pod�ֵĸ����������Է�����Pod�������Volumes

Pod״̬:
- Pending:Pod������ȷ,�ύ��Master,�����������������δ��ȫ����
- Running:Pod�Ѿ������䵽ĳ��Node��,����������������Ѿ��������
- Terminied:Pod������ֹ
- Failed(err &.):Pod������������������,������һ����������ʧ��״̬������.
- ����(�ο����ױȽϾ�,���油��)

ÿһ��������������֮��Ӧ��docker pod ��������,������:(ͬһ��pod�ڵ�����֮�����ͨ��localhost�໥ͨ��)

**Service**:

  Kubernetes��Ȼ���ÿһ��Pod����һ��������IP��ַ,�������IP��ַ������Pod�����ٶ���ʧ,�����һ��Pod���һ����Ⱥ���ṩ����,��ô�������������,Service�������������������.

Service���Կ�Node����;Node,Pod,Service,Container��ϵ����ͼ:
![service](pic/service.png)
��Pod����������,ϵͳ�����Service�Ķ��崴������Pod��Ӧ��Endpoint����,�Խ�����Service����Pod�Ķ�Ӧ��ϵ,����Pod�Ĵ���,����,Endpoint����Ҳ��������,Endpoint������Ҫ��Pod��IP��ַ��������Ҫ�����Ķ˿ں����,ͨ��kubectl get endpoint���Բ鿴.Service�����IP(���ļ��SVIP)ֻ�����ڲ�(��Pod֮��,Service֮��)����;���Ҫ���ⲿ����,����ֻ��Ҫ�����Service�Ķ˿ڿ��ŵ���ȥ����(ÿ���ڵ㶼��������Ӧ�Ķ˿�),KubernetesĿǰ֧��3�ֶ�������Service��type����:NodePort,LoadBalancer��ingress

**ingress**

�Ƽ��ķ�ʽ,ͨ�� Ingress �û�����ʵ��ʹ�� nginx �ȿ�Դ�ķ�������ؾ�����ʵ�ֶ��Ⱪ¶����.

ʹ�� Ingress ʱһ������������:

- ��������ؾ�����
- Ingress Controller
- Ingress

###### ��������ؾ�����

��������ؾ������ܼ򵥣�˵���˾��� nginx��apache ʲô�ģ��ڼ�Ⱥ�з�������ؾ������������ɲ��𣬿���ʹ�� Replication Controller��Deployment��DaemonSet �ȵȣ���������ϲ���� DaemonSet �ķ�ʽ���𣬸о��ȽϷ���

###### Ingress Controller

Ingress Controller ʵ���Ͽ������Ϊ�Ǹ���������Ingress Controller ͨ�����ϵظ� kubernetes API �򽻵���ʵʱ�ĸ�֪��� service��pod �ȱ仯�����������ͼ��� pod��service ��������ٵȣ����õ���Щ�仯��Ϣ��Ingress Controller �ٽ�����ĵ� Ingress �������ã�Ȼ����·�������ؾ���������ˢ�������ã��ﵽ�����ֵ�����

###### Ingress

Ingress �������Ǹ������壻����˵ĳ��������Ӧĳ�� service������ĳ���������������ʱת����ĳ�� service;��������� Ingress Controller ��ϣ�Ȼ�� Ingress Controller ���䶯̬д�뵽���ؾ����������У��Ӷ�ʵ������ķ����ֺ͸��ؾ���

��ͼ:

![](pic/ingress.jpg)

����ͼ�п��Ժ������Ŀ�����ʵ��������������Ǳ����ؾ��������أ����� nginx��Ȼ�� Ingress Controller ͨ���� Ingress ������֪ĳ��������Ӧ�ĸ� service����ͨ���� kubernetes API ������֪ service ��ַ����Ϣ���ۺ��Ժ����������ļ�ʵʱд�븺�ؾ�������Ȼ���ؾ����� reload �ù�����ʵ�ַ����֣�����̬ӳ��

�˽������������Ժ���Ҳ�ͺܺõ�˵������Ϊʲôϲ���Ѹ��ؾ���������Ϊ Daemon Set����Ϊ����������������Ǳ����ؾ��������صģ�������ÿ�� node �϶�����һ�£�ͬʱ hostport ��ʽ���� 80 �˿ڣ���ô�ͽ����������ʽ����ȷ�� ���ؾ��������ĵ����⣬ͬʱ����ÿ�� node �� 80 ������ȷ�����������ǰ���� �Ÿ� nginx ����ʵ����һ�㸺�ؾ���

[����ʹ�õ�ingress�����ļ�](yaml/)

[ingress���ֲο��ĵ�](https://mritd.me/2016/12/06/try-traefik-on-kubernetes/#132ingress-controller)

**NodePort**:

�ڶ���Serviceʱָ��spec.type=NodePort,��ָ��spec.ports.nodePortֵ,ϵͳ���ڼ�Ⱥ�ֵ�ÿ��Node�ϴ�һ�������ϵ���ʵ�˿ں�,�����ܷ���Node�Ŀͻ��˶���ͨ������һ��Node����������˿�,���������ڲ���Service.

**LoadBalancer**:
����Ʒ�����֧����Ӹ��ؾ�����,�����ͨ��spec.type=LoadBalancer����Service,ͬʱ��Ҫָ�����ؾ�������IP,ͬ�»���Ҫָ��Service��NodePort��clusterIP.

**Replication Controller(RC)**:

RC(��д)���ڶ���Pod�ĸ�������,��Master��,Controller Manager����ͨ��RC�Ķ��������Pod�Ĵ���,���,���ٵȲ���(PodҲ���Ե�������);  Kubernetes��ȷ��������ʱ�̶��������û�ָ���ġ�������(Replica)����,����й����Pod����������.ϵͳ�ͻ�ͣ��һЩ;���Pod��������ָ������,ϵͳ�ͻ�������һЩPod

**Label**:

Label��Kubernetesϵͳ�е�һ�����ĸ���;Label��key/value�ļ�ֵ�Ե���ʽ���ӵ����ֶ�����,��Pod,Service,RC,Node��,Label��������Щ����Ŀ�ʶ������,���������ǽ��й����ѡ��,Label�����ڴ�������ʱ���ӵ�������,Ҳ�����ڶ��󴴽���ͨ��API����,��Ϊ�������Label��,�����������ʹ��Label Selector�����������ö���.(��ϸ�ο�KubernetesȨ��ָ�ϵ�1���21ҳ)

**Volume(�洢��)**:

Volume��Pod���ܹ�������������ʵĹ���Ŀ¼,Kubernetes�ֵ�Volume��Pod������������ͬ,���������������ڲ����,��������ֹ������ʱ,Volume�ֵ����ݲ��ᶪʧ.

**Namespace(�����ռ�)**:

Namespace��Kubernetesϵͳ��һ������Ҫ�ĸ���,ͨ��ϵͳ�ڲ��Ķ�����䵽���ݵ�Namespace��,�γ��߼��Ϸ��鲻ͨ����Ŀ,С����û���,���ڲ�ͬ�ķ����ڹ���ʹ��������Ⱥ��Դ��ͬʱ���ܱ��ֱ����

**Endpoint**:

����:��ʱ��֪����ô����

��Ⱥ�е�һЩ��д:
  * componentstatuses (aka 'cs')  
  * configmaps (aka 'cm')  
  * daemonsets (aka 'ds')  
  * deployments (aka 'deploy')  
  * endpoints (aka 'ep')  
  * events (aka 'ev')  
  * horizontalpodautoscalers (aka 'hpa')  
  * ingresses (aka 'ing')   
  * limitranges (aka 'limits')  
  * namespaces (aka 'ns')  
  * nodes (aka 'no')  
  * persistentvolumeclaims (aka 'pvc')  
  * persistentvolumes (aka 'pv')  
  * pods (aka 'po')  
  * podsecuritypolicies (aka 'psp')  
  * replicasets (aka 'rs')  
  * replicationcontrollers (aka 'rc')  
  * resourcequotas (aka 'quota')  
  * serviceaccounts (aka 'sa')  
  * services (aka 'svc')

���� kubectl get nodes ���Լ�дΪkubectl get no

��Ⱥ����

[Cluster.md](Cluster.md)

�ο�����:<<KubernetesȨ��ָ��>>��һ��
