### `useRef()`

Render edilmesine gerek duymadan bir değere referans vermenizi sağlayan bir hookdur.Bir componentin bazı bilgileri "hatırlamasını" istediğinizde, ancak bu bilgilerin yeni renderı tetiklemesini istemiyorsanız, bir ref kullanabilirsiniz.

```
const ref = useRef(initialValue)
```

<img src="https://react.dev/images/docs/illustrations/i_ref.png" width="150" alt="ref"/>

useRef aşağıdaki gibi bir obje return eder:

```
const ref = useRef(0)
console.log(ref)

{
  current: 0 // The value you passed to useRef
}
```

current değere erişebilirsiniz ayrıca bu değer mutable'dır yani okuyup,tekrardan yazabilirsiniz.

**_React da tek yönlü veri akışından kaçmanın bir yoludur, reactın izlemediği gizli bir cep gibidir._**

Render etmede kullanılacaksa datayı state'te tutmak, eğer sadece event handlerlarda kullanılıp değiştirilecekse ve rendera gerek yoksa ref de tutmak daha efektif olacaktır.

<br/>

**_WRONG_** (not works)

```
const ref = useRef(0)
return
  <button onClick={handleClick}>
    You clicked {countRef.current} times
  </button>
```

**_CORRECT_**

```
const [time,setTime] = useState(0)
return
  <button onClick={handleClick}>
    You clicked {time} times
  </button>
```

### **Ne zaman kullanılır**

- JSX ile hesaplanmasına gerek olmayan objeleri saklamak
- DOM Manipülasyonlarında , DOM elementini saklamak

### **DOM ve Ref**

React otomatik olarak DOM'u günceller bu yüzden DOM manipülasyonuna çok sık ihtiyaç duymayız.Ancak bazen React tarafından yönetilen DOM öğelerine erişmeniz gerekebilir; örneğin, bir düğüme odaklanmak, ona kaydırmak veya boyutunu ve konumunu ölçmek için.

React'te bunları yapmanın yerleşik(built-in) bir yolu yoktur, bu nedenle DOM düğümü için bir referansa ihtiyacınız olacaktır.

```
const myRef = useRef(null);
return
  <div ref={myRef}>
```

```
// You can use any browser APIs, for example:
myRef.current.scrollIntoView();
```

### `forwardRef()`

Componentinizin DOM düğümünü ref ile bir üst componente geçirmenize izin verir.

```
const MyInput(props, ref) => {
  return
      //...
}
export default forwardRef(MyInput)
```

**_Example_**

```
// Child Component

import { forwardRef } from 'react';

const MyInput = forwardRef(function MyInput(props, ref) {
  const { label, ...otherProps } = props;
  return (
    <label>
      {label}
      <input {...otherProps} ref={ref} />
    </label>
  );
});

```

```
// Parent Component

function Form() {
  const ref = useRef(null);

  function handleClick() {
    ref.current.focus();
  }

  return (
    <form>
      <MyInput label="Enter your name:" ref={ref} />
      <button type="button" onClick={handleClick}>
        Edit
      </button>
    </form>
  );
}
```

Child Componentin ref'ine bir üst parentden erişebiliyoruz.

### `useImperativeHandle(ref, createHandle, dependencies?) `
useImperativeHandle, bir ref'i expose ederken özelleştirmenizi sağlayan bir React Hook'tur.

Dom düğümünün tamamını ref ile çıkartmak yerine imparative handle ile istediğimiz şekilde expose ediyoruz.

```
import { forwardRef, useRef, useImperativeHandle } from 'react';

const MyInput = forwardRef(function MyInput(props, ref) {
  const inputRef = useRef(null);

  useImperativeHandle(ref, () => {
    return {
      focus() {
        inputRef.current.focus();
      },
      scrollIntoView() {
        inputRef.current.scrollIntoView();
      },
    };
  }, []);

  return <input {...props} ref={inputRef} />;
});
```
