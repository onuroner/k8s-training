
# CronJob

Kubernetes `CronJob` nesnesi, periyodik işlemleri otomatik olarak gerçekleştirmemizi sağlamaktadır. Örneğin her gün mesai başlangıcında email gönderen bir uygulamayı Kubernetes içerisinde CronJob olarak tanımlayabiliriz.

Buna benzer olarak Kubernetes `Job` nesneleri, tek seferlik işlemlerimizi gerçekleştirmemize olanak sağlamaktadır. CronJob'un Job nesnesinden farkı yapacağımız işlemi periyodik bir hale getiriyor olmasıdır. Bu sayede manuel olarak her seferinde Job nesnesini tetiklememiş oluyoruz.

CronJob nesnesi, belirlenen zaman geldiğinde, bir Job oluşturur ve geri kalan işlemleri tıpkı bir önceki yazıda gördüğümüz gibi tamamen Job devralır.

```bash
apiVersion: batch/v1
kind: CronJob
metadata:
  name: hello
spec:
  schedule: "* * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hello
            image: busybox:1.28
            imagePullPolicy: IfNotPresent
            command:
            - /bin/sh
            - -c
            - date; echo Hello from the Kubernetes cluster
          restartPolicy: OnFailure

```

CronJob tanımında yer alan `schedule` parametresi ile çalışma periyodunu belirtmiş oluyoruz. `jobTemplate` altında ise çalışacak Job ile ilgili detaylar yer almaktadır.

