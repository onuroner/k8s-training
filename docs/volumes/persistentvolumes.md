
# Persistent Volume

Volume nesnelerinin Pod'lar üzerinde veri tutmaya yaradığını biliyoruz. Pod yaşam döngüsü sonlandığında emptyDir türünde bir Volume nesnesinin içerisindeki nesneler de silinmektedir. Bu durumu hostPath türünde bir Volume oluşturarak ilgili Pod'un üzerinde çalıştığı node üzerinde bir dizini Pod'a bağlayarak çözebiliriz.

Tabi bunu yaptığımızda ortaya başka bir problem ortaya çıkmakta. Diyelim ki Pod'umuz çalışmayı durdurdu ve tekrardan yeni bir Pod nesnesi oluşturuldu. Yeni oluşan Pod bir önceki worker node üzerinde oluşturulmadığında Volume üerindeki verilere nasıl ulaşabileceğiz?

`PersistentVolume` nesneleri bu noktada yardımımıza koşmaktadır. Volume’lar Pod’un bir parçası iken PV’lar cluster’ın bir parçasıdır. PV’lar ise cluster var olduğu var olmaya devam edecektir.

Kubernetes içerisinde bir PV oluşturmak için öncelikle bir veri depolama kaynağına ihtiyacımı vardır. Örneğin, bir bulut sağlayıcısında (AWS, Google Cloud, vb.) bir depolama servisi veya fiziksel bir sunucuda bir disk olabilir.

Sonrasında aşağıdaki gibi bir tanım yaparak PV nesnesi oluşturabiliriz:

```bash
apiVersion: v1
kind: PersistentVolume
metadata:
  name: example-pv
spec:
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"
  persistentVolumeReclaimPolicy: Retain
```

Yukarıda hostPath tabanlı bir PV nesnesi tanımı yer almaktadır. *spec* altında PV nesnemize ait bazı parametreler bulunmaktadır.
- `capacity.storage`: PV'nin 10 Gigabayt depolama kapasitesi olduğunu belirtir.
- `accessModes`: PV'nin nasıl erişilebileceğini belirtir:
  - ReadWriteOnce: Volume tek bir node tarafından okunabilir ve yazılabilir.
  - Diğer seçenekler: *ReadOnlyMany* (birden fazla node tarafından okunabilir) veya *ReadWriteMany* (birden fazla node tarafından okunabilir ve yazılabilir).
- `hostPath.path`: PV'nin host makinede hangi dizine bağlanacağını belirtir.
- `persistentVolumeReclaimPolicy`: PV'ye ait PVC (Persistent Volume Claim) silindiğinde verinin ne olacağını belirtir. "Retain" seçeneği, verilerin korunacağı anlamına gelir.

### PersistentVolumeClaim

PV nesnelerini direkt olarak Pod içerisinde kullanamayız. Kulanıcılar storage ihtiyaçlarını karşılamak için `PersistentVolumeClaim (PVC)` nesnelerini kullanarak PV nesneleri ile etkileşime geçerler.

PVC'ler, PV'leri talep eder ve uygun olan bir PV ile eşleşirse, PVC'ye bağlanır ve kullanılabilir hale gelir.PVC'ler, PV'lerden bağımsız olarak oluşturulur ve yönetilir.

```bash
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: example-pvc
  namespace: default
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```
![Persistent Volume Claims](https://github.com/onuroner/k8s-training/blob/main/images/PersistentVolumeClaimUsedInaPod.png?raw=true)

