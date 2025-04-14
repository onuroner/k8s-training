
# Apply komutu

Kubernetes'te obje oluşturma işlemleri için template görevi gören **yaml** dosyaları kullanılmaktadır. İlgili yaml dosyasına hangi bileşenleri oluşturacağımızı ve bu bileşenlerin hangi özellikleri barındıracağını belirtip bu dosyayı **kubectl apply** komutuyla çağırarak bileşenlerimizi oluştururuz.

YAML template'leri aşağıdaki alanlardan oluşmaktadır.

```
apiVersion:
kind:
metadata:
spec:

```
- **apiVersion:** Oluşturacağımız objenin Kubernetes içinde hangi api versiyonunda tanımlandığını gösterir. "kubectl explain {obje_adı}" komutu ile bu bilgiye ulaşabiliriz.
- **kind:** Oluşturacağımız objenin ne objesi olduğunu tanımladığımız yerdir.
- **metadata:** Obje ile ilgili unique bilgleri tanımladığımız yerdir. (name, labels, annotations vs.)
- **spec:** Her obje tipine göre tanımlayacağımız farklı özellikleri barındırır. 


Örnek biir YAML dosyası:

```
apiVersion: v1
kind: Pod
metadata:
  name: firstpod
  labels:
    app: frontend
    team: developer
spec:
  containers:
  - name: nginx
    image: nginx:latest
    ports:
    - containerPort: 80

```
Oluşturacağımız objeleri template dosyaları kullanarak oluşturmanın sağladığı bazı avantajlar şunlarıdr:

- Tek  komutla birden fazla obje oluşturabiliriz.
- Objeler üzerindeki değişiklikleri kolay bir şekilde yapabiliriz.