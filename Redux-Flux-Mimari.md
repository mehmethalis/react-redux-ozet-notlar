## <b> Flux Architecture </b>

<br/>
Flux, Facebook'un istemci tarafı web uygulamaları oluşturmak için kullandığı uygulama mimarisidir. Tek yönlü bir veri akışı kullanarak React'in birleştirilebilir görünüm bileşenlerini tamamlar. Resmi bir çerçeveden ziyade bir modeldir.

[Flux ile ilgili video](https://facebook.github.io/flux/docs/in-depth-overview)

Uygulama 4 parçaya bölünür bunlar ;

- Stores
  - Storlar uygulama arayüzündeki verileri sakladığımız ve yönettiğimiz yerdir.
- Dispatcher
  - Dispatchers Arayüzden gelen actionları stora iletir
- Views
  - Kullanıcı arayüzüdür componentlerin dallandığı katmandır
- Action / Action Creators
  - Action, o actionı gerçekleştirmek için gerekli tüm bilgileri içeren düz bir nesnedir.
  - Type özelliğine sahiptir
  - Actionlar farklı yerlerden tetiklenebilir.

<br/>

### **Unidirectional data flow**

<div style="text-align:center">
<img src="https://cdn-media-1.freecodecamp.org/images/B3swRnUORvq-CH8yZO-Pgy9ZiAmN5LPlgMM3" class="kg-image" alt="B3swRnUORvq-CH8yZO-Pgy9ZiAmN5LPlgMM3" width="601" height="85" loading="lazy">
</div>
<br/>

- Views, aldıkları verileri değiştirmez. Bu verilerdeki değişiklikleri dinlerler, yeni değerlerle actions oluştururlar, ancak verileri güncellemezler.

- Stores, views ve diğer herhangi bir işlem, (diğer) storedaki durumu doğrudan değiştiremez. dispatcher aracılığıyla bir action göndermeleri gerekir

- Store okumalarındaki veri akışı, yazma işlemlerine göre daha kısadır. Store yazmalarındaki veri akışı, async ve sync eylemler arasında farklılık gösterir.

<br/>

### **Artıları**

- Flux mimarisi, views doğrudan etki alanı store ile eşleşmediği bir uygulamada daha iyidir. Başka bir deyişle, views birçok storeu güncelleyecek eylemler oluşturabildiğinde ve storelar birçok view'ı güncelleyecek değişiklikleri tetikleyebilir.

- Actions kalıcı hale getirilebilir ve ardından tekrar oynatılabilir.

<br/>

### **Eksileri**

- Flux, her viewın bir store ile eşleştiği bir uygulamaya gereksiz karmaşıklık ekleyebilir. Bu tür bir uygulamada, view ve store arasında bir ayrım yeterlidir.

<br/>

## <b> Redux </b>

Açık kaynaklı kodlu bir state management kütüphanesidir. Flux mimarisine benzer. Genel olarak state’i merkezileştirip uygulamanın karmaşıklığını azaltarak daha yönetilebilir bir state yapmaya çalışır. React ile çok uyumlu çalışır aynı zamanda diğer JS kütüphaneleri ile de kullanabilirsiniz.

<br/>

### **Redux Kullanıldığı Durumlar (use cases)**

Veri görselleştirme için bir pano oluşturduğunuzu hayal edin. Pano, bir dizi widget içerir ve kullanıcılara bunları özelleştirmek için birçok seçenek sunar. Bir kullanıcı, widget'ları ihtiyaçlarına göre ayarlayacak ve uygulama durumu, kullanıcı girişine göre değişecektir.

Redux kullanmıyorsanız, durumu yönetme ve paylaşma sorumluluğu tüm widget'lar arasında dağıtılır. Bu durumda, veri tutarsızlığı hatalarını düzeltmekte zorlanabilirsiniz. Ancak Redux'u eklerseniz, tüm widget'lar tek bir global mağaza aracılığıyla iletişim kurmaya başlar ve bu, tüm widget'ların durumunu senkronize tutmayı basitleştirir.

- Uygulamanın farklı bölümlerine yansıyan birden çok bileşen arasında paylaşılmak için aynı duruma ihtiyacınız var.
- Uygulama durumları sık sık değişir ve farklı bileşenler aynı anda değişiklikleri başlatabilir.
- Uygulama, üzerinde çalışan birkaç React geliştiricisi ile orta ila büyük bir kod tabanına sahiptir.
- Uygulama davranışınızı net bir şekilde anlamak için uygulama durumunun zaman içinde nasıl değiştiğini izlemeniz ve kaydetmeniz gerekir.
- Durum yönetimi hatalarını hızlı bir şekilde düzeltmek için uygun araçlara ihtiyacınız var ve test için Redux DevTools'tan yararlanmak istiyorsunuz.

<br/>

### **Redux kullanılmadığı durumlar**

Redux, durum yönetimi için harika bir araç olsa da, onu aşırı kullanmamak önemlidir. Redux'u basit ve küçük uygulamalarda kullanmak, uygulama mimarisine gerekçesiz bir karmaşıklık ekleyebilir, boşa giden bellek kullanımına yol açabilir ve uygulama durumunu yedeklemek için bir önbelleğe alma çözümü eklemeyi gerektirebilir. Uygulama durum yönetiminiz aşağıdaki durumlarda Redux'a ihtiyacınız olmayabilir:

- Düz mantığı izleyen basit UI değişikliklerini ima eder.
- Görünüm başına tek bir kaynaktan gelen verileri işler ve veri tutarsızlığı hatalarına sahip olma riski yoktur veya çok azdır.
- React Context bağımlılık enjeksiyonu kullanılarak yapılabilen, uygulama yapınızdaki bir alt ağaçtaki bileşenlere destek olarak veri göndermeyi gerektirir.

Örneğin, modal açık/kapalı durumunu göz önünde bulundurun. modalları kökte işlemek önemlidir, ancak durumlarını Redux'ta tutmak gerekli değildir. Bu durumda daha iyi bir yaklaşım, modal için bir Portal oluşturmak ve durumlarını yalnızca değiştirildikleri yerde ele almak olacaktır.

<br/>

### **Action**

Bir javascript objesidir. Store’da hangi state’in değişeceği bilgisini(type) ve state verisini taşır(payload).

<br/>

### **Reducer**

State ve action’ı parametre alan ve yeni state’i dönen pure bir fonksiyondur. Action’ın type’ına göre state’i değiştirir ve yeni state’i geri döner. Mutlaka state’in güncel bir kopyası geri dönmesi gerekiyor. Aksi halde, componentler kendini render etmez. Bunu yapmaktaki amaç state’in referansını değiştirmektir. Bunun için Object.assign() veya Object spread opretaor yöntemlerini kullanabiliriz.

```
type Reducer<S, A> = (state: S, action: A) => S
```

Reducer birikimli state ve yeni action kabul eden bir fonksiyondur. State güncellendikten sonra yeni state değerini döndürür.

<br/>

### **useReducer()**

useState() kullanmak yerine, kompleks state geçişleri dahil olmak üzere bunu redux mekanizmasına benzer şekilde dispatch yardımı ile yapar. İsterseniz redux yapısına benzetebilirsiniz. Özelikle obje ve array'in tutulduğu durumlarda useState() yerine useReducer() kullanılmalıdır.

[Detaylı dökümana bakabilirsiniz](https://beta.reactjs.org/reference/react/useReducer)
<br/>
<br/>

```
//.tsx file

  interface User {
    name: string;
  }

  const [state, setState] = useReducer(
    (state: User, newState: Partial<User>) => ({
      ...state,
      ...newState,
    }),
    {
      name:""
    }
  );
```

<div style="text-align:center">
<img src="https://i.ibb.co/VTPj4Vn/Screenshot-2023-03-08-at-22-18-25.png" width="400" alt="useReducerOrnekCode"/>
</div>

<br/>

### **Neden useReducer?**

Birden fazla alt değer içeren karmaşık durum mantığınız olduğunda veya bir sonraki durum bir öncekine bağlı olduğunda, useReducer genellikle useState'e tercih edilir. useReducer ayrıca, callback yerine gönderimi iletebilir ve derin güncellemeleri tetikleyen bileşenler için performansı optimize etmenize olanak tanır.

<br/>

### **Asynchronous (Eş Zamansız) State Güncelleme Ve Callback**

React'de state güncelleme async yapıdadır. Yeni başlayanların sıkça karşılaştığı bir sorun state'i güncelledikten sonra hemen state'in çağrıldığında güncellenmiş state değil mevcut olan state döner.Bunun nedeni ise state güncellendiği anda bekleyen bir state geçiş sürecinin olmasıdır. State güncelelemede senkron bir işlem garantisi yoktur. Ayrıca setState() bir promise dönmediği için async/await gibi yapılarında bir faydası yoktur.

```
state = {value: 5}

this.setState({value: this.state.value + 1})
console.log(this.state.value) // Prints 5 and not 6
```

```
// with-callback  //

this.setState(
   (prevState) => ({value: prevState.value +1}),
   () => {console.log(this.state.value)}
);
```

<br/>

### **React neden this.state‘i senkron olarak güncellemez?**

React, tüm bileşenler yeniden render edilmeye başlamadan önce olay yöneticilerinde setState()‘i çağırıncaya kadar kasten “bekler”. Gereksiz yeniden render etme işlemlerinden kaçınarak performansı artırır.

Bununla birlikte, React’in neden yeniden render etmeden hemen this.state‘i güncellemediğini merak ediyor olabilirsiniz.

İki ana sebep var:

- Bu, props ile state arasındaki tutarlılığı bozar ve hata ayıklaması zor olan sorunlara neden olur.
- Bu, üzerinde çalıştığımız bazı yeni özelliklerin uygulanmasını imkansız kılar.

<br/>

### **Store**

State’in tutulduğu yerdir. Store’u oluşturmak için createStore fonksiyonunu kullanırız. Parametre olarak reducer’ları alır. Store oluşturulduktan sonra geriye 3 tane fonksiyon döner;

- <strong>`dispatch(action)` </strong>

  - Action’ı parametre alarak reducer’ı tetikler. Bunun sonucunda state değiştirilir. Bu fonksiyon her çalıştırıldığında abone olan tüm componen’tler tekrar render edilir.Tabii ki state değiştikten sonra, render işlemi yapılır.

  - Bununla birlikte, createStore'u ApplyMiddleware ile sararsanız, ara katman yazılımı eylemleri farklı şekilde yorumlayabilir ve async eylemlerini göndermek için destek sağlayabilir. Async actionlar genellikle Promises, Observables veya Thunks gibi async ilkellerdir.

  - Middleware topluluk tarafından oluşturulur ve varsayılan olarak Redux ile gönderilmez. Kullanmak için Redux-Thunk veya Redux-Promise gibi paketleri açıkça yüklemeniz gerekir. Kendi ara katman yazılımınızı da oluşturabilirsiniz.

- <strong> `getState()`</strong>

  - Güncel state bilgisini döner

- <strong>`subscribe(listener)` </strong>

  - Component’ler bu fonksiyonu kullanarak Store’a abone olurlar. Store, abone olan tüm componentleri içinde saklar.

- <strong>`replaceReducer(nextReducer)` </strong>

  - Durumu hesaplamak için şu anda mağaza tarafından kullanılan redüktörün yerini alır.

  - Gelişmiş bir API'dır. Uygulamanız code splitting ve bazı reducerları dinamik olarak yüklemek istiyorsanız buna ihtiyacınız olabilir.

  - Redux için reloading mekanizması uygularsanız buna da ihtiyacınız olabilir.

- <strong>`createStore(reducer, [preloadedState], [enhancer])` </strong>
  - Uygulamanızın statelerini tutan bir Redux store oluşturur. Uygulamanızda yalnızca tek bir store olmalıdır.
    - **reducer (Function)**: Güncel state'i ve işlenecek bir action göz önüne alındığında, bir sonraki state'i döndüren indirgeyici bir işlev.
    - **preloadedState:** İlk state. İsteğe bağlı olarak, state'i evrensel uygulamalarda sunucudan hydrate veya daha önce serileştirilmiş bir kullanıcı oturumunu geri yüklemek için belirtebilirsiniz.
    - **enhancer(Function):** Store enhancer. Store için, ara katman yazılımı, kalıcılık, vb. Gibi üçüncü taraf özellikleri ile geliştirmek için isteğe bağlı olarak belirleyebilirsiniz. Redux ile birlikte gönderilen tek store güçlendiricisi ApplyMiddleware() dir.
- <strong> Bazı İpucular!! </strong>
  - Uygulamanızda birden fazla store yaratmayın onun yerine birden fazla reducerları `combineReducers()` ile birleştirip root bir reducer oluşturabilirsiniz.
  - Redux stateleri düz JS obje ve dizileridir.
  - Eğer state obje türünde ise değiştirmediğinizden(mutate) emin olun. State güncellemelerinin her seviyesinde spread operatörü ile bir kopyasını oluşturun. `return { ...state, ...newData }`
  - Sunucuda çalışan evrensel uygulamalar için, izole edilecek şekilde her istekle bir store örneği oluşturun. Bir store örneğine birkaç veri getirme actionı gönderin ve uygulamayı sunucuda oluşturmadan önce tamamlamalarını bekleyin.
  - Bir store oluşturulduğunda, Redux store'a başlangıç durumuyla doldurmak için reducerınıza initial state gönderin.
  - Birden fazla store enhancers kullanmak için `compose()` kullanın.

<br/>

### **Redux vs Relay**

- Relay Facebook tarafından oluşturuldu, Tek bir store olmasından dolayı flux architecture veya reduxa benzer.
- Ana fark Relay'in yalnızca sunucudan kaynaklanan durumu yönetmesi ve duruma tüm erişimin GraphQL sorguları ve mutasyonlar aracılığıyla kullanılmasıdır.
- Relay sizin için çekilen datayı cache alır ve sadece değişen verileri getirerek süreci optimize eder.
- Ayrıca sunucudan sonucu almadan önce state'i değiştirmeyi de destekler.
- Reduxın daha iyi bir mekanizması vardır ve esnektir.
- Typescript ile implementasyonu Relayin daha zordur.
- Redux ile daha fazla şeyi kontrol edebilirsiniz.
  <br/>

***<small>
<b>*GraphQL</b>,
bildirime dayalı ve birleştirilebilir sorgular kullanan bir web hizmeti çerçevesi ve protokolüdür ve over fetching ve under fetching gibi sorunları çözer. REST'in yerini almak için geçerli bir aday olduğuna inanılır.</small>\***

<br/>

### **Data Flow**

1. Kullanıcı view’de, action’u tetikleyecek bir işlem yapar.
2. Dispatch, gelen action’ı store’da mevcut state ile birlikte reducer’a parametre geçer.
3. Bu işlem sonucunda state’in ilgili kısımları değişir ve yeni bir state oluşur. Burada reducer için Object.assign() veya Object spread opretaor yöntemlerinin önemi anlaşılıyor, eğer state birden fazla veri tutuyorsa.
4. State güncellendiği gibi abone olan tüm component’ler tekrar render edilir.
5. En sonunda bu state tekrar view’e yansır.

<br/>
<div style="text-align:center"><img src="https://d33wubrfki0l68.cloudfront.net/01cc198232551a7e180f4e9e327b5ab22d9d14e7/b33f4/assets/images/reduxdataflowdiagram-49fa8c3968371d9ef6f2a1486bd40a26.gif" width="400" alt=""/></div>

<br/>
<br/>
