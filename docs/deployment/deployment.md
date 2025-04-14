
# Deployments

Deployment objeleri, uygulamanın mevcut durumunun istenilen durumla aynı olmasını sağlar. Buna yönelik olarak bir veya birden fazla pod oluşturur ve bu istenilen durumu sürekli olarak mevcut durumla karşılaştırır. Bu işlem, uygulamanın istenilen durumda kalmasını sağlar ve gerektiğinde düzeltmeler yapar.

`DeploymentController`, control plane içerisinde yer alan controller manager'ın bir parçasıdır ve bir controller olarak mevcut durumun her zaman çalışan konteynerli uygulamamızın istenen durumuyla eşleşmesini sağlar. 

Örnek bir Deployment template'i şu şekildedir:

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx-deployment
  template:
    metadata:
      labels:
        app: nginx-deployment
    spec:
      containers:
      - name: nginx
        image: nginx:1.20.2
        ports:
        - containerPort: 80
```

Bu YAML tanımlamasında en üst seviyedeki `spec` kısmından sonra uygulamamızın içinde çalışmasını istediğimiz durumu tanımlamaya başlıyoruz. Örneğimizde `replicas` parametresi ile 3 adet pod oluşturmak istediğimizi belirtiyoruz. `template` kısmında ise oluşacak her bir pod nesnesinin hangiz özelliklere sahip olacağını tanımlıyoruz. 

### Deployment Oluşturma

```bash
kubectl create -f def-deploy.yaml
```

Imperative olarak şu şekilde oluşturabiliriz:
```bash
kubectl create deployment nginx-deployment \
--image=nginx:1.20.2 --port=80 --replicas=3
```
### Deployment Güncelleme

Deploymentta image değişikliği yapmak için:

```bash
kubectl set image deployment/<deployment_name> <container_name>=<new_image>
```

Replika sayısını değiştirmek için:
```bash
kubectl scale deployment <isim> --replicas=<yeni sayı>
```
