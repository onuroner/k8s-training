
# Kubectl config

Kubernetes içerisinde bir clustera kubectl ile ulaşmak istediğimizde bağlanacağımız cluster bilgilerine erişebileceğimiz bir yer olması lazım. kubeconfig dosyası tam olarak bu görevi görmektedir. Varsayılan olarak **home** dizininin altındaki **.kube** klasöründe bulunmaktadır. Minikube kullanarak kurduğum geliştirme ortamına ait config dosyası şu şekilde:

```bash
apiVersion: v1
clusters:
- cluster:
    certificate-authority: /home/onur/.minikube/ca.crt
    extensions:
    - extension:
        last-update: Thu, 03 Apr 2025 15:08:44 +03
        provider: minikube.sigs.k8s.io
        version: v1.35.0
      name: cluster_info
    server: https://127.0.0.1:60401
  name: minikube
contexts:
- context:
    cluster: minikube
    extensions:
    - extension:
        last-update: Thu, 03 Apr 2025 15:08:44 +03
        provider: minikube.sigs.k8s.io
        version: v1.35.0
      name: context_info
    namespace: default
    user: minikube
  name: minikube
current-context: minikube
kind: Config
preferences: {}
users:
- name: minikube
  user:
    client-certificate: /home/onur/.minikube/profiles/minikube/client.crt
    client-key: /home/onur/.minikube/profiles/minikube/client.key
```
Burada clustera bağlanırken kullandığımız cluster bilgilerini ve kullanıcıları görebiliriz. **Clusters** kısmında tanımlı clusterlara ait ip bilgisi, cluster adı gibi bilgiler yer almakta. **current-context** bilgisi halihazırda kubectl komutlarının hangi context içerisinde çalışacağını göstermektedir. 

Config dosyası içerisinde birden fazla context tanımı olması durumunda farklı contextler üzerinde işlem yapmak isteyebiliriz. Kubectl komutlarını çalıştıracağımız contexti değiştirmek için aşağıdaki komutu kullanabiliriz:

```bash
kubectl config use-context {context_adı}
```
Aynı zamanda mevcuttaki contextleri de listeleyebilmemiz mümkün:

```bash
kubectl config get-contexts
```

Yukarıdaki komutun çıktısında yıldız işareti, şu anda içerisinde bulunduğumuz context'i işaret etmektedir.