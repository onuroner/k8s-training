
# Pod kavramı

Kubernetes cluster'ı içerisinde birçok farklı objeler oluştururuz. Bunların en temelinde **pod** denilen obje türü gelmektedir. Pod, kubernetes içerisinde yönetilen çalıştırılabilir en küçük bileşendir. Yani aslında Kubernetes, container’ları değil pod’ları yönetmektedir.

Pod içerisinde tek bir container çalıştırabildiğimiz gibi birden fazla container çalıştırmamız da mümkündür. Aynı pod içerisindeki container’lar aynı storage’ı, network’ü paylaşırlar. Bir pod’daki container’ın açtığı bir sokete yine aynı pod’daki bir diğer container localhost (127.0.0.1) üzerinden ulaşabilir.

## Pod oluşturma

```
kubectl run firstpod --image=nginx 
```
- ```--image```: Pod içerisinde çalışan container içinde hangi imajın çalışacağını gösterir. 

Oluşturduğumuz pod hakkında detaylı bilgi almak istersek **describe** komutunu kullanabiliriz.

```
kubectl describe pods firstpod
```

Pod içerisindeki containerda oluşan loglara erişmek için:

```
kubectl logs firstpod
```

Bildiğiniz üzere Docker'da çalışan bir container içerisinde bir komut çalıştırmak istediğimizde **exec** komutunu kullanarak container içerisine girip çalıştırabiliyoruz. Buna benzer bir exec kullanımı pod objeleri için de geçerli.

```
kubectl exec firstpod -- hostname
```

Yukarıdaki komutta da görüldüğü gibi pod içerisinde "hostname" komutunu çalıştırabiliriz. Aynı komutu shell içerisine bağlanıp ta çalıştırabiliriz.

```
kubectl exec -it firstpod -- /bin/sh
```