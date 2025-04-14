# Namepace

Birden fazla kullanıcı ve ekip aynı Kubernetes clusterını kullanıyorsa, clusterı `namespace` kullanarak sanal alt clusterlara bölebiliriz. Bir namespace içinde oluşturulan kaynakların/nesnelerin adları benzersizdir, ancak clusterdaki namespace'ler arasında benzersiz değildir.

Tüm Namespace'leri listelemek için aşağıdaki komutu çalıştırabiliriz:

```bash
 kubectl get namespaces
```

Genel olarak, Kubernetes varsayılan olarak 4 namespace oluşturur: `kube-system`, `kube-public`, `kube-node-lease` ve `default`. kube-system, çoğunlukla control plane elemanları olmak üzere Kubernetes sistemi tarafından oluşturulan nesneleri içerir. Default, yöneticiler ve geliştiriciler tarafından oluşturulan nesneleri ve kaynakları içerir ve kullanıcı tarafından başka bir namespace adı verilmediği sürece nesneler varsayılan olarak bu alana atanır. kube-public, cluster hakkında genel (hassas olmayan) bilgileri açığa çıkarmak gibi özel amaçlar için kullanılan, güvenli olmayan ve herkes tarafından okunabilen özel bir namespacedır. kube-node-lease, her node ile ilişkili Lease nesnelerini tutar. Node lease'leri, kubelet'in heartbeat göndermesine izin verir, böylece control plane node arızasını tespit edebilir.. Bununla birlikte, kümeyi sanallaştırmak ve kullanıcıları, geliştirici ekiplerini, uygulamaları veya katmanları izole etmek için istenildiği gibi ek namespace'ler oluşturmak iyi bir uygulamadır:

```bash
kubectl create namespace new-namespace-name
```