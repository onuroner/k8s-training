
# Service

Kubernetes Service nesnesi, bir Pod topluluğuna erişim sağlayan soyut bir katmandır. Service, belirli bir uygulamaya ait Pod'ları gruplayarak tek bir ağ hizmeti olarak tanımlar.

Örneğin, bir operatörün bir dizi Pod'u yönettiği ve bir istemcinin doğrudan IP adreslerini kullanarak Pod'lara eriştiği bir senaryoyu ele alalım. Bu erişim yöntemi, istemcinin hedef Pod'ların IP adreslerini önceden almasını gerektirir ve istemci için gereksiz bir ek yük getirir.

![Service 1](https://github.com/onuroner/k8s-training/blob/main/images/AScenarioWhereaUserIsAccessingPodsviatheirIPAddresses.png?raw=true)

Beklenmedik bir şekilde, istemcinin erişmekte olduğu Pod'lardan biri sonlandırılırsa denetleyici tarafından yeni bir Pod oluşturulur. Yeni Pod'a, istemci tarafından hemen bilinemeyecek yeni bir IP adresi atanacaktır. İstemci, herhangi bir değişiklik ve güncelleme için hedef Pod'ların IP adreslerini izlemeye çalışırsa, bu yalnızca istemcinin ek yükünü arttıracaktır.

![Service 2](https://github.com/onuroner/k8s-training/blob/main/images/pwbpa4aswxia-2.ANewPodIsCreatedAfteranOldOneTerminatedUnexpectedly.png?raw=true)

Bu durumun üstesinden gelmek için Kubernetes, Pod'ları gruplandıran ve bunlara erişmek için bir politika tanımlayan `Service` adı verilen daha üst düzey bir soyutlama sağlar. Bu gruplama Label ve Selector aracılığıyla gerçekleştirilir. Bu mantıksal gruplama stratejisi, ReplicaSets, Deployments ve hatta DaemonSets gibi Pod denetleyicileri tarafından kullanılır.

![Service 3](https://github.com/onuroner/k8s-training/blob/main/images/7kqcw9bc0v2u-GroupingofPodsusingLabelsandSelectors.png?raw=true)

Bir Service objesi oluşturan örnek bir YAML şu şekildedir:


```bash
apiVersion: v1
kind: Service
metadata:
  name: frontend-svc
spec:
  selector:
    app: frontend
  ports:
  - protocol: TCP
    port: 80
    targetPort: 5000
```

Yukarıda tanımlanan Service nesnesi `app: frontend` etiketine sahip tüm Pod'ları otomatik olarak tespit eder ve bunlara erişim sağlar. Kubernetes cluster içindeki diğer Pod ve servisler, `frontend-svc` adını kullanarak (veya tam DNS adı ile frontend-svc.default.svc.cluster.local) bu servise erişebilir. Servis, 80 numaralı port üzerinden gelen istekleri, hedef Pod'ların 5000 numaralı portuna yönlendirir. Service tipi belirtilmediği için varsayılan olarak `ClusterIP` tipi kullanılır, yani bu servis sadece cluster içerisinden erişilebilir.

### Service Türleri

- **ClusterIP:** Varsayılan Service türüdür. Bu tipteki bir Service, ClusterIP olarak bilinen bir sanal IP adresi alır. Bu sanal IP adresi Service ile iletişim kurmak için kullanılır ve **yalnızca cluster içinden** erişilebilir. Eğer Service tanımı oluştururken herhangi bir tip tanımı yapılmazsa, Service otomatik olarak ClusterIP tipinde üretilecektir.

- **NodePort:** NodePort, Service'lerimizi dış dünyadan erişilebilir kılmak istediğimizde kullanışlıdır. Son kullanıcı, belirtilen porttaki herhangi bir worker node'una bağlanır, bu da isteği dahili olarak Service'in ClusterIP'sine proxy'ler, ardından istek cluster içinde çalışan uygulamalara iletilir.

![NodePort](https://github.com/onuroner/k8s-training/blob/main/images/nodeport.png?raw=true)

- **Load Balancer:** Bu servis, uygulamaların yük dengeleme yapması gerektiğinde kullanılır. LoadBalancer servisi, bir IP adresi ve ilgili Pod grubuyla eşleştirilir ve gelen trafiği yönlendirmek için bir yük dengeleyici kullanır. Varsayılan olarak Round Robin algoritmasını kullanır. Bu Servis, ağ dışından Pod’lara erişim sağlamak için kullanılabilir.

*LoadBalancer tipinden bir servis oluşturulduğu zaman NodePort ve ClusterIP servisleri otomatik olarak oluşturulur.*

