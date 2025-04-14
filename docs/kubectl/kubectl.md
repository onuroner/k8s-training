
# Kubectl

Bu bölümden temel kubectl komutlarına erişebilirsiniz.
***
Mevcut context'teki cluster hakkında bilgi alma:
```bash
kubectl cluster-info
```
***
Bir komut hakkında bilgi alma:
```bash
kubectl {komut} --help
```
Ör: kubectl delete --help
***
Mevcut namespace içerisindeki objeleri listeleme:
```bash
kubectl get {obje_türü}
```
Ör: kubectl get pods
***
Varsayılan dışında başka bir namespace'de bulunan objeleri listeleme (_-n "namespace_adi" ekiyle komutların belirtilen namespace üstünde çalıştırılması sağlanmaktadır_)
```
kubectl get {obje_tipi} -n {namespace_adi}
```
Ör: kubectl get pods -n kube-system
***
Tüm namespace'lerde bulunan objeleri listeleme (_--all-namespaces ya da kısaca -A eki komutun tüm namespace'leri kapsamasını sağlar_)
```
kubectl get {obje_ismi} --all-namespaces
```
Ör: kubectl get pods --all-namespaces

***

kubectl komutları varsayılan olarak kısıtlı bir bilgi dönmektedir. **-o** opsiyonuyla bu çıktı daha farklı formata ve daha detaylı bir biçime dönüştürülebilir.

- ```-o yaml``` Yaml formatında çıktı döndürür.
- ```-o json``` Json formatinda çıktı döndürür.
- ```-o wide``` Plain-text fakat daha çok bilgi içerir.
- ```-o jsonpath=<template>``` Jsonpath ifadesinde tanımlanan alanları döndürür.
- ```-o name``` Sadece isimleri döndürür. 
- ```-o custom-columns=<spec>``` Comma-seperated olarak belirtilen kolonlardan bir table döndürür.

```
kubectl get {obje_tipi} {obje_ismi} -o {opsiyon_ismi}
```
Ör: kubectl get pods pod-1 -o wide
***
Kubernetes obje tipleriyle ilgili detaylı bilgi edinmek..
```
kubectl explain {obje_tipi}
```
Ör: kubectl explain pods
