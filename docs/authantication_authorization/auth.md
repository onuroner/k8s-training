
# Authentication ve Authorization

Kubernetes API'sine kubectl komutuyla, client kütüphaneleriyle veya REST istekleri yapılarak erişilebilmektedir. Hem kullanıcılar hem de ServiceAccount nesneleri API erişimi için authentication işleminden geçebilmektedirler.

Bir istek API Server'a eriştiğinde bazı aşamalardan geçmektedir.
- **Authentication:** API isteğinde sağlanan kimlik bilgilerine göre kullanıcı doğrulanır.
- **Authorization:** Kubernetes'te yetkilendirme, kullanıcıların veya sistemlerin "ne yapabileceklerini" belirler
- **Admission Control:** Admission controller'ları, yalnızca geçerli ve uyumlu API isteklerinin yürütülmesini sağlamak için gelen istekler üzerinde özel ilkeler uygular. Bu, bir kümenin bütünlüğünü ve güvenliğini korumak için gereklidir.

![Auth Mechanism](https://github.com/onuroner/k8s-training/blob/main/images/9qc8l9vt06dc-ControllingAccesstotheAPI.png?raw=true
)

## Authentication

Kubernetes, API çağrıları ile kullanıcı oluşturmayı desteklemez. Bununla birlikte user adında bir nesne tipi de barındırmamaktadır. 

Normal bir kullanıcı API çağrısı yoluyla eklenemese de, clusterın sertifika yetkilisi (CA) tarafından imzalanmış geçerli bir sertifika sunan herhangi bir kullanıcının kimliğinin doğrulandığı kabul edilir. Bu yapılandırmada Kubernetes, kullanıcı adını sertifikanın `subject` kısmındaki ortak ad alanından belirler (örneğin, "/CN=onur"). Buradan, `rol tabanlı erişim kontrolü (RBAC)` alt sistemi, kullanıcının bir kaynak üzerinde belirli bir işlemi gerçekleştirmeye yetkili olup olmadığını belirleyecektir.

Kubernetes içerisinde desteklenen authentication yöntemleri şu şekildedir:
- **X.509 İstemci Sertifikaları:** İstemci sertifikası kimlik doğrulamasını etkinleştirmek için, API sunucusuna `--client-ca-file=SOMEFILE` seçeneğini ileterek bir veya daha fazla sertifika yetkilisini içeren bir dosya kullanmamız gerekir. Dosyada belirtilen sertifika yetkilileri, kullanıcılar tarafından API sunucusuna sunulan istemci sertifikalarını doğrulayacaktır.
- **Statik Token Dosyası:** API sunucusu, komut satırında `--token-auth-file=SOMEFILE` seçeneği verildiğinde bearer token'ları bir dosyadan okur. Şu anda tokenlar süresiz olarak kullanılmaktadır ve API sunucusu yeniden başlatılmadan token listesi değiştirilemez.
- **Bootstrap Tokens:** Yeni node'lar eklenmesi gibi ilk kurulum işlemleri için geçici kimlik doğrulama mekanizmasıdır.
- **ServiceAccount Tokenları:** İstekleri doğrulamak için imzalı bearer tokenları kullanan otomatik olarak etkinleştirilmiş kimlik doğrulayıcılardır. Bu tokenlar, cluster içi işlemlerin API sunucusuyla konuşmasını sağlayan Service Account Admission Controller kullanılarak Pod'lara eklenir.
- **OpenID Connect (OIDC) Tokenleri:** Kurumsal kimlik sağlayıcılarıyla entegrasyon için ideal bir yöntemdir. Harici bir kimlik sağlayıcı (Google, Azure AD, Okta, Keycloak vb.) tarafından verilen JWT token'lar kullanılır.
- **Webhook Token Authentication:** Webhook tabanlı kimlik doğrulama ile, tokenların doğrulanması uzak bir hizmete yüklenebilir.
- **Authentication Proxy:** API sunucusu önünde çalışan bir proxy ile kimlik doğrulama sağlanır.

## Authorization

Başarılı bir kimlik doğrulamasından sonra, kullanıcılar farklı işlemler gerçekleştirmek için API istekleri gönderebilir. Bu aşamada, bu API istekleri, isteklere izin veren veya reddeden çeşitli yetkilendirme modülleri kullanılarak Kubernetes tarafından yetkilendirilir.

### RBAC Authorization

RBAC authorization, temelde 4 adet nesneyi barındırmaktadır:
- Role
- ClusterRole
- RoleBinding
- ClusterRoleBinding

`Role` ve `ClusterRole` nesneleri bir takım izinler barındıran kuralları içermektedir. Bir Role her zaman belirli bir namespace içindeki izinleri belirler; bir Role oluşturduğunuzda, ait olduğu namespace bilgisini belirtmeniz gerekir. Buna karşın ClusterRole, namespace tabanlı olmayan bir kaynaktır. Kaynakların adları farklıdır çünkü bir Kubernetes nesnesi ya namespace tabanlı olmalı ya da namespace tabanlı olmamalıdır; ikisi birden olamaz. Özetle Role ile oluşturulan roller tek bir namespace için geçerliyken ClusterRole ile tanımlanan roller bütün cluster içinde geçerlidir.

Örnek bir Role nesnesi tanımı aşağıdaki gibidir.

```bash
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
```

Örnek bir ClusterRole nesnesi tanımı aşağıdaki gibidir.

```bash
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: secret-reader
rules:
- apiGroups: [""]
  resources: ["secrets"]
  verbs: ["get", "watch", "list"]
```

Rol oluşturulduktan sonra, onu bir `RoleBinding` nesnesi ile kullanıcılara bağlayabiliriz. Rol oluşturmada olduğu gibi rol bağlamak için de `RoleBinding` ve `ClusterRoleBinding` olmak üzere 2 çeşit nesne vardır.

Bir RoleBinding belirli bir ad alanı içinde izinler verirken, bir ClusterRoleBinding bu erişimi cluster çapında verir. Bir RoleBinding aynı namespace içindeki herhangi bir Role'e referans verebilir. Alternatif olarak, bir RoleBinding bir ClusterRole'a başvurabilir ve bu ClusterRole'u RoleBinding'in namespace'ine bağlayabilir. Bir ClusterRole'u clusterdaki tüm namspace'lere bağlamak istiyorsanız, bir ClusterRoleBinding nesnesi kullanırsınız.

Örnek RoleBinding ve ClusterRoleBinding tanımları aşağıdaki gibidir:

```bash
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: default
subjects:
- kind: User
  name: jane
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader 
  apiGroup: rbac.authorization.k8s.io
```

```bash
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
 name: read-secrets
 namespace: development
subjects:
- kind: User
 name: dave
 apiGroup: rbac.authorization.k8s.io
roleRef:
 kind: ClusterRole
 name: secret-reader
 apiGroup: rbac.authorization.k8s.io
```

```bash
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
 name: read-secrets-global
subjects:
- kind: Group
 name: manager
 apiGroup: rbac.authorization.k8s.io
roleRef:
 kind: ClusterRole
 name: secret-reader
 apiGroup: rbac.authorization.k8s.io
```