
# ConfigMap

`ConfigMap` nesneleri, konfigürasyon ile ilgili detayları container image'inden ayrı tutmamıza olanak sağlayan Kubernetes nesneleridir. Pod'lar ConfigMap nesnelerini environment variable, komut satırı argümanları veya bir volume olarak bağlanan yapılandırma dosyaları olarak kullanabilir. 

ConfigMap oluşturmak için kullanılan örnek bir YAML şu şekildedir:

```bash
apiVersion: v1
kind: ConfigMap
metadata:
  name: customer1
data:
  TEXT1: Customer1_Company
  TEXT2: Welcomes You
  COMPANY: Customer1 Company Technology Pct. Ltd.
```

### Pod İçerisinde ConfigMap Değerlerinin Kullanılması

#### Environment Variable Olarak Kullanılması

Pod tanımı içerisinde bir ConfigMap değerini environment variable olarak aşağıdaki şekilde kullanabiliriz:

```bash
...
  containers:
  - name: myapp-specific-container
    image: myapp
    env:
    - name: SPECIFIC_ENV_VAR1
      valueFrom:
        configMapKeyRef:
          name: config-map-1
          key: SPECIFIC_DATA
    - name: SPECIFIC_ENV_VAR2
      valueFrom:
        configMapKeyRef:
          name: config-map-2
          key: SPECIFIC_INFO
...
```

Yukarıda sunulan yapılandırmayla, `config-map-1` adlı ConfigMap nesnesinde `SPECIFIC_DATA` anahtarıyla eşlenen değer Pod içerisinde `SPECIFIC_ENV_VAR1` değerine, `config-map-2` adlı ConfigMap nesnesinde `SPECIFIC_INFO` anahtarıyla eşlenen değer Pod içerisinde `SPECIFIC_ENV_VAR2` değerine atanmaktadır.

#### Volume Olarak Kullanılması

ConfigMap nesnelerimizi Pod tanımlarımız içinde volume olarak vererek konfigürasyon değerlerinin oluşmasını sağlayabiliriz. configMap volume eklentisi ConfigMap nesnesini mount edilebilir bir kaynağa dönüştürür. 

```bash
...
  containers:
  - name: myapp-vol-container
    image: myapp
    volumeMounts:
    - name: config-volume
      mountPath: /etc/config
  volumes:
  - name: config-volume
    configMap:
      name: vol-config-map
...
```