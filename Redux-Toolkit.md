## <b> Redux Toolkit </b>

Daha iyi ve standart Redux kodu yazmamızı sağlayan bir paket aynı zamanda içinde opsiyonel olarak RTK Query paketini içermektedir. RTK ile manuel data fetching logiclerini minimize ediyoruz. Redux toolkit aslında 3 ortak konuda yardım ediyor. Bunlar;

- Redux Store Konfigüre etmek karmaşık
- Kullanışlı bir şeyler yapmak için bağımlılıkları fazla
- Çok fazla boilerplate koda ihtiyaç duyuyor
- Async işlemler redux-thunk kullanır (redux-saga ile yapılandırılabilir)

<br/>

### **`configureStore()` ile Store Kurulumunu Basitleştirme**

configureStore yardımcı olduğu konular;

- Okunması daha kolay olabilen "named" parametrelere sahip bir seçenekler objesine sahip olmak
- Store'a eklemek istediğiniz middleware, enhancersları `applyMiddleware()`'i ve `compose()`u çağırarak sizin için otomatik olarak oluşturur
- Redux DevTools Extension'ı otomatik etkinleştirir.

bunlara ek olarak bazı default middlewarları vardır.

- **redux-thunk** : popüler ve sık kullanılan component dışında ki async ve sync logiclerini çalışması için bir middleware.
- Geliştirme aşamasında, durumu değiştirmek veya serileştirilemeyen değerler kullanmak gibi yaygın hataları kontrol eden bir middleware.

<br/>

### **Store Yapısı**

```
import { combineReducers } from '@reduxjs/toolkit'

import subreddit from 'features/subreddit/SubredditSliceAsync'

const rootReducer = combineReducers({ subreddit })

const store = configureStore({
    reducer: rootReducer,
    middleware: (getDefaultMiddleware) =>
      getDefaultMiddleware().concat(loggerMiddleware),
    enhancers: [monitorReducersEnhancer],
})

return store
```

<br/>

### **`createReducer()` ile reducer oluşturmayı Basitleştirme**

<br/>

```
// Before

function todosReducer(state=[],action){
    switch(action.type){
        case 'ADD_TODO': {
            return state.concat(action.payload)
        }
        default:
            return state
    }
}

```

```
//after with redux toolkit

const todosReducer = createReducer([], (builder)=>{
    builder
        .addCase("ADD_TODO",(state,action)=>{
            state.push(action.payload) // kopyalamaya gerek yok
        })
})

```

State Mutating yeteneği, derinden iç içe geçmiş state'i güncellemeye çalışırken özellikle yararlıdır.

```
// Sancılı kod

case "UPDATE_VALUE":
  return {
    ...state,
    first: {
      ...state.first,
      second: {
        ...state.first.second,
        [action.someId]: {
          ...state.first.second[action.someId],
          fourth: action.someValue
        }
      }
    }
  }

```

create reducer ile daha basit ve daha iyi

```
//better

.addCase("UPDATE_VALUE",(state,action)=> {
        const {someId, someValue} = action.payload;
        state.first.second[someId].fourth = someValue;
    })
```

<br/>

### **`createAction()` ile daha basit action creator**

```
// BEFORE
function addTodo(text) {
  return {
    type: 'ADD_TODO',
    payload: { text },
  }
}

//AFTER
const addTodo = createAction('ADD_TODO')
addTodo({ text: 'Buy milk' })
// {type : "ADD_TODO", payload : {text : "Buy milk"}})


console.log(addTodo.toString())
// "ADD_TODO"
```

<br/>

### **`createSlice()` Slice oluşturmayı basitleştirin**

- Redux mantıksal ifadeleriniz oluşturmak için kullanabileceğiniz standart yöntem createSlice’ı kullanmaktır.

- Redux Toolkit, bu işlemi basitleştirmek için, sağladığınız REDUCER adlarına göre action types ve action creatorsları sizin için otomatik olarak oluşturacak bir createSlice işlevi içerir.

```
const postsSlice = createSlice({
  name: 'posts',
  initialState: [],
  reducers: {
    createPost(state, action) {},
    updatePost(state, action) {},
    deletePost(state, action) {},
  },
})
```

```
console.log(postsSlice)
/*
{
    name: 'posts',
    actions : {
        createPost,
        updatePost,
        deletePost,
    },
    reducer
}
*/
```

```
const { createPost } = postsSlice.actions

console.log(createPost({ id: 123, title: 'Hello World' }))
// {type : "posts/createPost", payload : {id : 123, title : "Hello World"}}
```

<br/>

### **Exports reducer and actions from Slice**

Slice export veya import tanımı [`Redux Ducks`](https://github.com/erikras/ducks-modular-redux) Patternına benzer.

```
const postsSlice = createSlice({
  name: 'posts',
  initialState: [],
  reducers: {
    createPost(state, action) {},
    updatePost(state, action) {},
    deletePost(state, action) {},
  },
})

// Extract the action creators object and the reducer
const { actions, reducer } = postsSlice

// Extract and export each action creator by name
export const { createPost, updatePost, deletePost } = actions

// Export the reducer, either as a default or named export
export default reducer

```

Slice import yada export işleminin birkaç dezavantajı vardır.

1. **Birincisi, Redux action typeların tek bir dilime özel olması amaçlanmamıştır.**

2. **İkinci olarak, iki modül birbirini içe aktarmaya çalışırsa JS modüllerinde "circular reference" sorunları olabilir.**

<br/>

### **`useDispatch()`**

dispatch fonksiyonu döner ve parametre ile yine bir fonksiyon alır.Slice ile oluşturulan actionları dispatch ile tetikleyip state’i güncelleyebiliriz.

```
const dispatch = useDispatch();

dispatch(getPostsStart);
```

### **`useSelector(state:state.reducerName)`**

**reducerName** reducerını state bilgisine erişebiliriz.State değiştiğinde selector otomatik olarak algılayacaktır.

```
import React from 'react'
import { useSelector } from 'react-redux'

export const CounterComponent = () => {
  const counter = useSelector((state) => state.counter)
  return <div>{counter}</div>
}
```

<br/>

### **Asynchronous Logic**

Redux storeları async işlemler ile ilgili hiç birşeyi bilmezler. Sadece sync olan action ve state update gibi root reducer fonksiyonlarını bilir ve ui'a bildirir. Ancak, mevcut store state'i göndererek veya kontrol ederek async store etkileşime girmesini istiyorsanız ne olur? Redux middlewarelerinin devreye girdiği yer burasıdır. Store'u genişletirler ve şunları yapmanıza izin verirler:

- Bir action dispatch edildiğinde exta logic execute edebilir.
- Gönderilen actionları duraklatın, değiştirin, geciktirin veya durdurun.
- Extra code ile dispatch ve getState e erişebilirsiniz.
- dispatce, gibi düz nesnelerinin yanı sıra diğer değerlerin örneğin functions ve promisler gibi nasıl kabul edileceğini öğretin.

Redux ile kullanılan bir çok middleware vardır. Bunlardan bazıları ;

- **redux-thunk** : doğrudan async logic içerebilecek düz fonksiyonlar yazmanıza izin verir.
- **redux-saga**: middleware tarafından yürütülebilmeleri için davranış açıklamalarını döndüren generator functionlar kullanır.

redux toolkit `configureStore` fonksiyonu default olarak thunk middlewareni içerir.

<br/>
