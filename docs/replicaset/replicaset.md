
# ReplicaSet

ReplicaSet nesneleri, herhangi bir zamanda çalışan replika Pod'ların istenilen durumda (desired state) çalışmasını sağlar. Bir Deployment nesnesi otomatik olarak bir ReplicaSet oluşturur ve bu da replika Pod'ları oluşturur. ReplicaSet'leri ve Pod'ları ayrı ayrı yönetmeye gerek yoktur, Deployment bunları bizim adımıza yönetecektir.

Bir Deployment nesnesi oluşturduğumuzda bu deployment otomatik olarak bir ReplicaSet oluşturmaktadır. İstediğimiz sayıda replika Pod'ların oluşturulmasını ve sürdürülmesini bu ReplicaSet sürdürmektedir. Çalışan replika Pod'lar üzerinde bir değişiklik yapmak istediğimizde (Ör: kullanılan image'i değiştirmek) Deployment nesnesi yeni bir ReplicaSet yaratır. Eski olan ReplicaSet yavaş yavaş kendi Pod'larını silmeye başlar, yeni ReplicaSet ise yeni Pod'ları çalıştırmaya başlar.

```bash
kubectl set image deployment/<deployment_name> <container_name>=<new_image>
```

Replika sayısını değiştirmek için:
```bash
kubectl scale deployment <isim> --replicas=<yeni sayı>
```
