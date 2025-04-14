
# Pod Yaşam Döngüsü

Podlar oluşturulmaya başlandıkları andan itibaren belirli bir yaşam döngüsünü takip ederler. Bir podun durumu zaman içinde değişim göstermektedir. 

- **Pending:** Pod nesnesinin yaşam döngüsündeki ilk durumudur. Pod durumu etcd'ye kaydedilmiştir fakat Pod henüz herhangi bir node üzerinde oluşturulmamıştır.

- **Creating:** Pod nesnesi bu aşamada bir node üzerinde oluşturulmaya başlanmaktadır. Pod içinde çalışan image `kubelet` ile çekilmeye başlanır.

- **ImagePullBackOff:** Pod nesnesi için tanımlanan image'in çekilemediği anlamına gelir. Büyük ihtimalle image adının yanlış yazılmasından veya image'i çekmek için için gerekli authentication'ın yapılamamasından dolayı karşımıza çıkar.

- **Running:** Pod oluşturulmuştur. Pod içindeki containerlar çalışıyor durumdadır.

- **Succeeded:** Pod'daki tüm containerlar başarıyla sonlanmış ve yeniden başlatılmayacaktır.

- **Failed:**	Pod'daki tüm containerlar sonlanmış ve en az bir container başarısızlıkla sonlanmıştır. Yani, container sıfır olmayan bir durum kodu ile çıkmış veya sistem tarafından sonlandırılmış ve otomatik olarak yeniden başlatılmak üzere ayarlanmamıştır.

- **Unknown:** Bir nedenle Pod'un durumu elde edilememiştir. Bu phase tipik olarak Pod'un çalışması gereken node ile iletişimde bir hata olduğunda ortaya çıkar.

- **CrashLoopBackOff:** Pod'da gerçekleşen bir yeniden başlatma döngüsünü temsil eder. Pod'daki bir konteyner başlatılır, ancak çöker ve ardından tekrar tekrar yeniden başlatılır.

### Restart policy

Pod içerisinde çalışan containerlara bir `restart policy` tanımı yapılır. Bu, ilgili container çalışmayı durdurduğunda tekrardan çalışıp çalıştırılmayacağını belirttiğimiz bir tanımlamadır.

- **Always:** Varsayılan değerdir. Bir container ne şekilde durdurulursa durdurulsun tekrardan başlatılacaktır.

- **OnFailure:**	Bir container yalnızca hata alırsa yeniden oluşturup başlatılır.

- **Never:** Bir container çalışmayı durdurursa tekrardan başlatılmaz.