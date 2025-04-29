
# Volumes

Bildiğimiz gibi, bir container içinde depolanan tüm veriler, container çökerse silinir. Bununla birlikte, bir Pod çöktüğünde kubelet ilgili Pod'u yeniden başlatacaktır, bu da eski verilerin hiçbirine sahip olmayacağı anlamına gelir.

Bu sorunun üstesinden gelmek için Kubernetes, çeşitli depolama teknolojilerinin Kubernetes tarafından kullanılmasına ve Pod'lardaki container'lara depolama ortamı olarak sunulmasına olanak tanıyan `Volume`'ları kullanır. Bir Volume, esasen container'ın dosya sistemi üzerindeki bir mount noktasıdır. Depolama ortamı, içerik ve erişim modu Volume türü tarafından belirlenir.

Kubernetes'te geçici bir Volume bir Pod'a bağlıdır ve bu Pod'un container'ları arasında paylaşılabilir. Geçici Volume, Pod içerisindeki container'lardan daha uzun ömürlüdür - bu da verilerin yeniden başlatmalar boyunca korunmasını sağlar.

### Volume Türleri

- **emptyDir:** İlk olarak bir Pod bir node'a atandığında oluşturulur ve ilgili Pod çalıştığı sürece var olur. Pod sonlandırılırsa emptyDir'in içeriği sonsuza kadar silinir.

```bash
apiVersion: v1
kind: Pod
metadata:
  name: test-pd
spec:
  containers:
  - image: registry.k8s.io/test-webserver
    name: test-container
    volumeMounts:
    - mountPath: /cache
      name: cache-volume
  volumes:
  - name: cache-volume
    emptyDir:
      sizeLimit: 500Mi
```

- **hostPath:** Bu Volume türü worker node'un dosya dizininden bir kısmı Pod'unuza bağlama imkanı sağlar. Pod sonlandırılırsa, Volume içeriği host üzerinde hala kullanılabilir.

```bash
apiVersion: v1
kind: Pod
metadata:
  name: hostpath-example-linux
spec:
  os: { name: linux }
  nodeSelector:
    kubernetes.io/os: linux
  containers:
  - name: example-container
    image: registry.k8s.io/test-webserver
    volumeMounts:
    - mountPath: /foo
      name: example-volume
      readOnly: true
  volumes:
  - name: example-volume
    # mount /data/foo, but only if that directory already exists
    hostPath:
      path: /data/foo # directory location on host
      type: Directory # this field is optional
```

Kubernetes içerisinde var olan diğer Volume türleri şu şekildedir:

- gcePersistentDisk
- awsElasticBlockStore
- azureDisk
- azureFile
- cephfs
- nfs
- iscsi
- secret
- configMap
- persistentVolumeClaim
