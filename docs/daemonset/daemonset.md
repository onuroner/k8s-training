
# DaemonSet

Kubernetes DaemonSet, cluster'daki her bir node üzerinde veya istenilen özelliklerdeki node'lar üzerinde belirli bir pod'un bir replikasını çalıştıran sistemdir. DaemonSet ile cluster'daki her bir node üzerinde belirli bir görevi yerine getirmesi gereken pod'lar otomatik olarak çalıştırılabilir ve yönetilebilir

ReplicationController ve ReplicaSet nesneleri, bir pod'un belirlenen sayı kadar replikasının çalışmasını garanti ederler. Fakat hangi node üzerinde kaç tanesinin yer alacağına dair herhangi bir garanti sunmazlar. DaemonSet ise her bir node üzerinde bir replikanın çalışacağının garantisini size sunar.

DaemonSet operatörleri, tüm node'lardan monitoring verileri toplamamız veya tüm node'larda depolama, ağ veya proxy daemonları çalıştırmamız gereken durumlarda, tüm node'larda her zaman çalışan belirli bir Pod türüne sahip olduğumuzdan emin olmak için yaygın olarak kullanılır. Bunlar multi-node Kubernetes cluster'larında kritik API kaynaklarıdır. 

Kubernetes DaemonSet nesnesinin amacı her node üzerinde bir pod çalıştırmak olduğu için Scheduler bileşenine ihtiyaç duymaz. Bu nedenle unschedulable olarak işaretlenmiş node'larınız varsa bile DaemonSet bunu önemsemeden pod'u ilgili node üzerinde çalıştırır.

Cluster'a yeni bir node eklendiğinde, DaemonSet, o yeni node üzerinde ilgili pod'u oluşturur. Node silindiğinde ise yine ilgili pod'u kaldırır.

ReplicationController ve ReplicaSet nesnelerinin aksine, DaemonSet herhangi bir replica sayısı parametresi içermez. Çünkü DaemonSet nesnesinin amacı tüm node'lar üzerinde ilgili pod'un bir replikasını çalıştırmaktır.

Örnek bir DaemonSet tanımı aşağıdaki gibidir:


```bash
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd-agent
  namespace: default
  labels:
    k8s-app: fluentd-agent
spec:
  selector:
    matchLabels:
      k8s-app: fluentd-agent
  template:
    metadata:
      labels:
        k8s-app: fluentd-agent
    spec:
      containers:
      - name: fluentd
        image: quay.io/fluentd_elasticsearch/fluentd:v4.5.2
```