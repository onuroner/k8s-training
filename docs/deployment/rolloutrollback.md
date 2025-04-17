
# Rollout-Rollback

Zaman içinde, Deployment nesnesi tarafından yönetilen uygulamaya güncellemeler göndermemiz gerekir. Pod'lar üzerinde çalışan image'in versiyonunu nginx:1.20.2'den nginx:1.21.5'e güncelleyelim. Deployment, 1.21.5 sürümlü yeni container image'i için yeni bir ReplicaSet B'yi tetikler ve bunun yanında yeni bir `Revision` oluşturulmuş olur. İki ReplicaSet arasında, 1.20.2 sürümlü üç Pod içeren ReplicaSet A'dan 1.21.5 sürümlü üç yeni Pod içeren yeni ReplicaSet B'ye veya Revision 1'den Revision 2'ye sorunsuz geçiş, bir `rolling update` olayıdır.

![Replica A Created](https://github.com/onuroner/k8s-training/blob/main/images/DeploymentReplicaSetACreated2023.png?raw=true)


![Replica B Created](https://github.com/onuroner/k8s-training/blob/main/images/DeploymentReplicaSetBCreated.png?raw=true)

![Replica B Pointed](https://github.com/onuroner/k8s-training/blob/main/images/DeploymentPointstoReplicaSetB.png?raw=true)

ReplicaSet B ve 1.21.5 sürümlü 3 Pod'u hazır olduğunda, Deployment bunları aktif olarak yönetmeye başlar. Ancak Deployment, önceki yapılandırma durumlarını Revision olarak kaydeder ve bu da Deployment'ın rollback özelliğinde önemli bir faktördür. Örneğimizde, yeni nginx:1.21.5'in performansı tatmin edici değilse, Deployment önceki bir Revision'a, bu durumda Revision 2'den tekrar nginx:1.20.2 çalıştıran Revision 1'e geri alınabilir.

Deployment oluşturma aşamasında `--record` parametresini vererek oluşturduğumuz Revision objelerinin bir kaydını tutmuş oluruz.

```bash
kubectl apply -f nginx-deploy.yaml --record
```

Belli bir Revision içerisinde hangi değişikliklerin yapıldığını görmek için aşağıdaki komutu çalıştırabiliriz:
```bash
kubectl rollout history deploy nginx-deployment --revision=2
```
Belli bir Revision'a geri dönmek istiyorsak:
```bash
kubectl rollout undo deploy nginx-deployment --to-revision=1
```
Bir deployment üzerinde yapılan değişiklikleri canlı olarak gözlemlemek istiyorsak:
```bash
kubectl rollout status deploy nginx-deployment
```