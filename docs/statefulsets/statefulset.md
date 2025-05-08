
# StatefulSet

Bir `StatefulSet` bir grup Pod'u çalıştırır ve bu Pod'ların her biri için ayrı bir kimlik tutar. Kalıcı depolama veya benzersiz bir ağ kimliğine ihtiyaç duyan uygulamaları yönetmek için kullanışlıdır.

Deployment nesneleriyle benzerlik gösteren bir Kubernetes nesnesidir. Bir StatefulSet aynı container özelliklerine dayanan Pod'ları yönetir. Deployment'ın aksine StatefulSet tarafından oluşturulan Pod'lar birbirlerinin yerine kullanılamazlar. Aynı spec ile oluşturulurlar fakat her biri ayrı bir kimlik taşımaktadır. StatefulSet, Pod'ları sıralı bir şekilde ve düzenli sayılardan oluşan isimlerden oluşturur.

StatefulSet, her bir pod için benzersiz bir isim ve ağ kimliği (FQDN) atayarak podların durumunu korur ve yönetir. Bu özellik, her bir podun kendi benzersiz durumunu sürdürmesini ve verilerin tutarlılığını sağlamasını mümkün kılar. Veritabanları gibi durumsal uygulamaların verilerini tutması gereken durumlar için bu büyük bir avantajdır.

Deployment nesnelerinde scale down işlemi yapıldığında mevcuttaki Pod'lardan rastgele seçim yaplarak silme işlemi yapılır. StatefulSet nesnelerinde ise en son oluşturulan Pod'lar ilk önce silinir.

Deployment ile oluşturulan Pod'lar rastgele isimlerle adlandırılırken StatefulSet ile oluşturulan Pod'lar belirli bir sıralamayı takip ederek adlandırılır. (statefulSetAdı-1,statefulSetAdı-2, statefulSetAdı-3, ...)

Bir Deployment ile oluşturulan Pod'lardan biri kapandığında rastgele bir isimle Pod oluşurken, StatefulSet kapanan pod'un aynı ismiyle yeni bir pod oluşturur.

Örnek bir StatefulSet tanımı şu şekildedir:

```bash
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: postgres
spec:
  serviceName: "postgres"
  replicas: 3
  selector:
    matchLabels:
      app: postgres
  template:
    metadata:
      labels:
        app: postgres
    spec:
      containers:
      - name: postgres
        image: postgres:14
        ports:
        - containerPort: 5432
          name: postgredb
        env:
        - name: POSTGRES_PASSWORD
          valueFrom:
            secretKeyRef:
              name: postgres-secrets
              key: password
        - name: PGDATA
          value: /var/lib/postgresql/data/pgdata
        volumeMounts:
        - name: postgres-data
          mountPath: /var/lib/postgresql/data
  volumeClaimTemplates:
  - metadata:
      name: postgres-data
    spec:
      accessModes: ["ReadWriteOnce"]
      storageClassName: "standard"
      resources:
        requests:
          storage: 10Gi
```
- Podlar `postgres-0`, `postgres-1`, `postgres-2` gibi düzenli isimlerle oluşturulur. Bu isimlendirme şekli **<statefulset-adı>-<sıra-numarası>** formatındadır.
- Her pod, `volumeClaimTemplates` bölümünde tanımlanan kalıcı bir depolama birimine sahiptir. Bu örnekte her pod için 10GB'lık bir kalıcı disk oluşturulur ve bu disk her zaman aynı pod'a bağlı kalır.
- Podlar, `app: postgres` etiketiyle etiketlenmiştir, bu da diğer Kubernetes kaynaklarının (örneğin Service) bu podları hedeflemesini sağlar.

