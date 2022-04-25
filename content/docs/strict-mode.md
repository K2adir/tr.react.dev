---
id: strict-mode
title: Strict Modu
permalink: docs/strict-mode.html
---

`Strict Modu` (StrictMode) uygulamadaki potansiyel sorunları vurgulayan bir araçtır. `Fragment` gibi `Strict Modu` de herhangi bir görünür UI render etmez. `Strict Modu` aynı zamanda alt öğeler için ek kontrol ve uyarıları etkinleştirir.

> Not:
>
> Strict Modu kontrolleri sadece geliştirme modunda yapılır.; _canlıda herhangi bir etkisi yoktur_.

Strict Modunu uygulamanızın herhangi bir parçası için aktif hale getirebilirsiniz. Örneğin:
`embed:strict-mode/enabling-strict-mode.js`

Yukarıdaki örnekte strict mod kontrolleri `Header` ve `Footer` bileşenleri için *yapılmayacaktır*. Ancak `ComponentOne` ve `ComponentTwo` ve onların tüm alt öğeleri için kontroller yapılacaktır.

<<<<<<< HEAD
`Strict Modu` şu konularda yardımcı olur:
* [Güvenli olmayan yaşam döngülerine sahip bileşenleri tespit etme](#identifying-unsafe-lifecycles)
* [Eski string ref API kullanımı hakkında uyarma](#warning-about-legacy-string-ref-api-usage)
* [Kullanımdan kaldırılmış findDOMNode kullanımı hakkında uyarma](#warning-about-deprecated-finddomnode-usage)
* [Beklenmeyen yan etkileri tespit etme](#detecting-unexpected-side-effects)
* [Eski context API tespit etme](#detecting-legacy-context-api)
=======
`StrictMode` currently helps with:
* [Identifying components with unsafe lifecycles](#identifying-unsafe-lifecycles)
* [Warning about legacy string ref API usage](#warning-about-legacy-string-ref-api-usage)
* [Warning about deprecated findDOMNode usage](#warning-about-deprecated-finddomnode-usage)
* [Detecting unexpected side effects](#detecting-unexpected-side-effects)
* [Detecting legacy context API](#detecting-legacy-context-api)
* [Detecting unsafe effects](#detecting-unsafe-effects)
>>>>>>> 1d21630e126af0f4c04ff392934dcee80fc54892

React'in gelecek sürümlerinde yeni özellikler eklenecektir.

### Güvenli olmayan yaşam döngülerine sahip bileşenleri tespit etme {#identifying-unsafe-lifecycles}

[Bu blog yazısında](/blog/2018/03/27/update-on-async-rendering.html) açıklandığı gibi, bazı eski yaşam döngüsü metodlarını asenkron React uygulamalarında kullanmak güvenli değildir. Ancak uygulamanız üçüncü parti kütüphaneler kullanıyorsa bu yaşam döngüsü metodlarının kullanılmadığından emin olmak oldukça zordur. Neyse ki, Strict Modu bize bu konuda yardımcı olabilir!

Strict modu etkinleştirildiğinde, React güvenli olmayan yaşam döngüsü kullanan sınıf bileşenlerinin bir listesini toplar ve bu bileşenler hakkında aşağıdaki gibi bir uyarı verir.

![](../images/blog/strict-mode-unsafe-lifecycles-warning.png)

Strict modu sorunlarına çözüm bulmak, gelecekte React sürümlerinde eşzamanlı render etme işleminden yararlanmanızı kolaylaştıracaktır.

### Eski string ref API kullanımı hakkında uyarma {#warning-about-legacy-string-ref-api-usage}

React, daha önce ref'leri yönetmek için iki yol sunuyordu: Eski string ref API ve callback API. String Ref API daha uygun olmasına rağmen [birkaç dezavantajı](https://github.com/facebook/react/issues/1373) vardı ve resmi önerimiz [bunun yerine callback kullanmaktı](/docs/refs-and-the-dom.html#legacy-api-string-refs).

React 16.3, herhangi bir dezavantajı olmadan string ref'in rahatlığını sunan üçüncü bir seçenek getirdi:
`embed:16-3-release-blog-post/create-ref-example.js`

Nesne ref'leri büyük ölçüde string ref'lerinin yerine geldiğinden beri strict modu artık string ref kullanımları konusunda uyarıyor.

> **Not:**
>
> Yeni `createRef` API'sine ek olarak Callback ref'leri de desteklenmeye devam edecektir.
>
> Bileşenlerinizdeki callback ref'leri değiştirmeniz gerekmez. Biraz daha esnek bir yapıda oldukları için gelişmiş bir özellik olarak kalacaklar.

[Yeni `createRef` API hakkında daha fazla bilgiyi buradan öğrenebilirsiniz.](/docs/refs-and-the-dom.html)

### Kullanımdan kaldırılmış findDOMNode kullanımı hakkında uyarma {#warning-about-deprecated-finddomnode-usage}

React, sınıf nesne örneği verilen bir DOM düğümünü ağaçta aramak için `findDOMNode`'u destekliyordu. Normalde buna ihtiyaç yoktur çünkü [doğrudan bir DOM düğümüne ref ekleyebilirsiniz](/docs/refs-and-the-dom.html#creating-refs).

`findDOMNode` ayrıca sınıf bileşenlerinde de kullanılabilir ancak bu, bir üst öğenin belirli alt öğelerin render edilmesine izin vererek soyutlama düzeylerini bozuyordu. Bir üst öğe DOM düğümüne erişebileceği için bir bileşenin uygulama ayrıntılarını değiştiremeyeceğiniz bir kodun yeniden düzenlenmesi (refactoring) tehlikesi oluşturur. `findDOMNode` sadece ilk alt öğeyi döndürür fakat Fragment'ler kullanılarak bir bileşenin birden fazla alt öğe render etmesi mümkündür. `findDOMNode` tek seferlik okuma API'sidir. Sadece istendiğinde cevap verir. Bir alt bileşen farklı bir farklı bir düğüm render ediyorsa, bu değişikliği ele almanın bir yolu yoktur. Bu nedenle `findDOMNode` sadece bileşenler asla değişmeyen tek bir DOM düğümü döndürürse işe yarar.

Bunun yerine, bunu özel bileşeninize bir ref geçerek ve [ref yönlendirme](/docs/forwarding-refs.html#forwarding-refs-to-dom-components) ile DOM boyunca ileterek yapabilirsiniz.

Ayrıca bileşeninize bir sarıcı (wrapper) DOM düğümü ekleyebilir ve doğrudan ona bir ref ekleyebilirsiniz.

```javascript{4,7}
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.wrapper = React.createRef();
  }
  render() {
    return <div ref={this.wrapper}>{this.props.children}</div>;
  }
}
```

> Not:
>
> CSS'te, düğümün tasarımın bir parçası olmasını istemiyorsanız [`display: contents`](https://developer.mozilla.org/en-US/docs/Web/CSS/display#display_contents) özelliğini kullanabilirsiniz.

### Beklenmeyen yan etkileri tespit etme {#detecting-unexpected-side-effects}

Kavramsal olarak, React iki aşamada çalışır:
* **Render** aşaması, örneğin DOM'da hangi değişikliklerin yapılması gerektiğini belirler. Bu aşamada React, render'ı çağırır ve sonucu bir önceki render çağrısının sonucuyla karşılaştırır. 
* **Commit** aşaması, React'ın değişiklikleri uyguladığı aşamadır. React DOM durumunda, React bu DOM düğümlerini ekler, günceller ve kaldırır. React ayrıca bu aşamada `componentDidMount` ve `componentDidUpdate` gibi yaşam döngülerini çağırır.

Commit aşaması genellikle hızlıdır fakat render aşaması yavaş olabilir. Bu nedenle, yakında gelecek eşzamanlı (concurrent) mod (henüz varsayılan olarak etkin değil) render etme işini parçalara ayırır, tarayıcıyı engellememek için işi duraklatır ve devam ettirir. Bu, React'ın render aşaması yaşam döngülerini commit aşamasına hiç geçmeden (bir hata veya daha yüksek öncelikli kesinti nedeniyle) çağırabileceği ya da commit aşamasından önce bir kereden fazla çağırabileceği anlamına gelir. 

Render aşaması yaşam döngüleri aşağıdaki sınıf bileşeni metodlarını içerir:
* `constructor`
* `componentWillMount` (veya `UNSAFE_componentWillMount`)
* `componentWillReceiveProps` (veya `UNSAFE_componentWillReceiveProps`)
* `componentWillUpdate` (veya `UNSAFE_componentWillUpdate`)
* `getDerivedStateFromProps`
* `shouldComponentUpdate`
* `render`
* `setState` güncelleyen fonksiyonlar (ilk argüman)

Yukarıdaki metodlar bir kereden fazla çağrılabileceğinden, yan etkiler içermemesi önemlidir. Bu kuralı göz ardı etmek, bellek sızıntıları (memory leak) ve geçersiz uygulama durumu (invalid application state) gibi çeşitli sorunlara yol açabilir. Ne yazık ki bu sorunları tespit etmek zor olabilir çünkü genellikle [belirlenebilir olmayabilirler](https://en.wikipedia.org/wiki/Deterministic_algorithm).

Strict modu, yan etkileri otomatik olarak tespit edemez ancak onları daha belirgin hale getirerek tespit etmenize yardımcı olabilir. Bu, aşağıdaki fonksiyonları bilinçli bir şekilde iki kere çağırarak (double-invoking) yapılır:

* Sınıf bileşeni `constructor`, `render`, ve `shouldComponentUpdate` metodları
* Sınıf bileşeni statik `getDerivedStateFromProps` metodu
* Fonksiyon bileşen gövdeleri
* State güncelleyen fonksiyonlar (`setState`in ilk argümanı)
* `useState`, `useMemo`, veya `useReducer`'a aktarılan fonksiyonlar

> Not:
>
> Bu sadece geliştirme modu için geçerlidir. _Yaşam döngüleri canlıda iki defa çağırılmayacaktır._

Örneğin, aşağıdaki kodu ele alalım:
`embed:strict-mode/side-effects-in-constructor.js`

İlk bakışta bu kod sorunlu görünmeyebilir. Ancak `SharedApplicationState.recordEvent` [etkisiz](https://en.wikipedia.org/wiki/Idempotence#Computer_science_meaning) değilse, bu bileşeni birden çok kez başlatmak geçersiz uygulama durumuna yol açabilir. Bu tür ince bir hata geliştirme sırasında ortaya çıkmayabilir veya bunu tutarsız bir şekilde yaparak gözden kaçabilir.

Strict modu, `constructor` gibi metodları kasıtlı olarak iki kere çağırarak, bu gibi desenlerin fark edilmesini sağlar.

> Not:
>
<<<<<<< HEAD
> React, 17. versiyonu ile birlikte, yaşam-döngüsü fonksiyonlarına yapılan ikinci çağrılardaki mesajları susturmak için `console.log()` gibi konsol metotlarını otomatik olarak değiştirmektedir. Ancak bu, bazı durumlarda istenmeyen davranışlara sebep olabilir. Bu durumda [geçici bir çözüm kullanılabilir](https://github.com/facebook/react/issues/20090#issuecomment-715927125).
=======
> In React 17, React automatically modifies the console methods like `console.log()` to silence the logs in the second call to lifecycle functions. However, it may cause undesired behavior in certain cases where [a workaround can be used](https://github.com/facebook/react/issues/20090#issuecomment-715927125).
>
> Starting from React 18, React does not suppress any logs. However, if you have React DevTools installed, the logs from the second call will appear slightly dimmed. React DevTools also offers a setting (off by default) to suppress them completely.
>>>>>>> 1d21630e126af0f4c04ff392934dcee80fc54892

### Eski context API tespit etme {#detecting-legacy-context-api}

Eski context API hataya açıktır ve gelecekteki bir ana sürümde kaldırılacaktır. Hala tüm 16.x sürümleri için çalışır, ancak strict modunda şu uyarı mesajını gösterecektir:

![](../images/blog/warn-legacy-context-in-strict-mode.png)

<<<<<<< HEAD
Yeni sürüme geçmeye yardımcı olması için [yeni context API](/docs/context.html) dökümanını okuyun.
=======
Read the [new context API documentation](/docs/context.html) to help migrate to the new version.


### Ensuring reusable state {#ensuring-reusable-state}

In the future, we’d like to add a feature that allows React to add and remove sections of the UI while preserving state. For example, when a user tabs away from a screen and back, React should be able to immediately show the previous screen. To do this, React support remounting trees using the same component state used before unmounting.

This feature will give React better performance out-of-the-box, but requires components to be resilient to effects being mounted and destroyed multiple times. Most effects will work without any changes, but some effects do not properly clean up subscriptions in the destroy callback, or implicitly assume they are only mounted or destroyed once.

To help surface these issues, React 18 introduces a new development-only check to Strict Mode. This new check will automatically unmount and remount every component, whenever a component mounts for the first time, restoring the previous state on the second mount.

To demonstrate the development behavior you'll see in Strict Mode with this feature, consider what happens when React mounts a new component. Without this change, when a component mounts, React creates the effects:

```
* React mounts the component.
  * Layout effects are created.
  * Effects are created.
```

With Strict Mode starting in React 18, whenever a component mounts in development, React will simulate immediately unmounting and remounting the component:

```
* React mounts the component.
    * Layout effects are created.
    * Effect effects are created.
* React simulates effects being destroyed on a mounted component.
    * Layout effects are destroyed.
    * Effects are destroyed.
* React simulates effects being re-created on a mounted component.
    * Layout effects are created
    * Effect setup code runs
```

On the second mount, React will restore the state from the first mount. This feature simulates user behavior such as a user tabbing away from a screen and back, ensuring that code will properly handle state restoration.

When the component unmounts, effects are destroyed as normal:

```
* React unmounts the component.
  * Layout effects are destroyed.
  * Effect effects are destroyed.
```

> Note:
>
> This only applies to development mode, _production behavior is unchanged_.

For help supporting common issues, see:
  - [How to support Reusable State in Effects](https://github.com/reactwg/react-18/discussions/18)
>>>>>>> 1d21630e126af0f4c04ff392934dcee80fc54892
