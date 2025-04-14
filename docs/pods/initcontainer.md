
# Init Container

Init container, ana uygulama container'ı çalışmadan önce çalışan ve ana uygulama container'ı içerisinde olması gereken düzenlemeleri yapan containerdır. InitContainer kullanımına örnek olarak uygulamanın ayağa kalkması için gereken config ve secretlerı Pod'un dosya sistemine yazmamıza yardımcı olacak bir uygulama ya da bazı kurulum scriptleri barındıran bir uygulama olarak düşünebiliriz.

Bir pod içerisinde birden fazla container olabildiği gibi birden fazla initContainer da olabilir. Pod içerisinde 'initContainers' fieldında dizi olarak tanımlanırlar.

InitContainerlar sıralı olarak çalışırlar. InitContainer içerisindeki process çalışıp bitmelidir. Bir initContainer içerisindeki process başarılı bir şekilde bitmeden diğer initContainer veya containerlar çalışmaz.

Örnek bir init container tanımlaması:

```bash
apiVersion: v1
kind: Pod
metadata:
  name: initcontainerpod
spec:
  containers:
  - name: appcontainer
    image: busybox
    command: ['sh', '-c', 'echo The app is running! && sleep 3600']
  initContainers:
  - name: initcontainer
    image: busybox
    command: ['sh', '-c', "until nslookup myservice; do echo waiting for myservice; sleep 2; done"]

```
Yukarıdaki init container içerisinde `nslookup` komutu ile `myservice` adlı servisin çalışıp çalışmadığı kontrol edilmektedir. İlgili servis çalışmaya başladıktan sonra uygulama containerı çalışmaya başlayacaktır.