
# Secret

Bir veritabanına istekler atan bir frontend uygulamamız olduğunu düşünelim. Bu uygulama için Deployment nesnesi oluştururken, veritabanı parolasını Deployment YAML tanımına  dahil edebiliriz, ancak bu yöntemle parola korunmayacaktır. Parola, YAML dosyasına erişimi olan herkes tarafından erişilebilir olacaktır.

Bu senaryoda `Secret` nesnesi, hassas bilgileri paylaşmadan önce kodlamamıza izin vererek yardımcı olabilir. Parolalar, token'lar veya anahtarlar gibi hassas bilgileri anahtar-değer çiftleri biçiminde paylaşabiliriz; böylece bir Secret'taki bilgilerin kazara açığa çıkma riskini azaltabiliriz. 

*Varsayılan olarak Secret verilerinin `etcd` içinde düz metin olarak depolandığını unutmamak önemlidir, bu nedenle yöneticilerin API sunucusuna ve etcd'ye erişimi sınırlaması gerekir. Bununla birlikte, Secret verileri etcd'de saklanırken şifrelenebilir, ancak bu özelliğin Kubernetes cluster yöneticisi tarafından API sunucusu düzeyinde etkinleştirilmesi gerekir.*

Örnek bir Secret tanımı aşağıdaki gibidir:
```bash
apiVersion: v1
kind: Secret
metadata:
  name: my-password
type: Opaque
data:
  password: bXlzcWxwYXNzd29yZAo=
```

Bir Secret içindeki hassas bilgileri eklemek için iki tür yöntem vardır: `data` ve ,,`stringData`.

`data` alanı ile, hassas bilgi alanının her değeri `base64` kullanılarak kodlanmalıdır. Secret'ımız için bir tanıma sahip olmak istiyorsak, önce parolamızın base64 kodlamasını oluşturmalıyız:

```bash
$ echo mysqlpassword | base64
```
`stringData` ile her bir hassas bilgi alanının değerini kodlamaya gerek yoktur. Hassas alanın değeri, Secret oluşturulduğunda kodlanacaktır:
```bash
apiVersion: v1
kind: Secret
metadata:
  name: my-password
type: Opaque
stringData:
  password: mysqlpassword
```

Oluşturduğumuz Secret içerisindeki hassas verilerimizi bir Pod üzerinde aşağıdaki gibi kullanabiliriz:

```bash
....
spec:
  containers:
  - image: wordpress:4.7.3-apache
    name: wordpress
    env:
    - name: WORDPRESS_DB_PASSWORD
      valueFrom:
        secretKeyRef:
          name: my-password
          key: password
....
```