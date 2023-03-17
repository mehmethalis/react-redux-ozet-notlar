## <b> RTK Query </b>
API Arayüz katmanları sağlamada data fetching ve caching için güçlü ve kompakt bir araç seti sağlamaktadır.Bir web uygulamasında veri getirme deki manuel fetching ve caching logiclerini indirgemeyi sağlar. Redux mimarisinin üzerine inşa edilmiştir.

- UI Spinnerları için loading state'i takip etmek
- Aynı veriler için yinelenen isteklerden kaçınma
- Kullanıcı arayüzünün daha hızlı hissettirmesi için optimistic güncellemeler
- Kullanıcı UI ile etkileşime girerken cache yaşam sürelerini yönetmek

RTK Query, Redux Toolkit paketine dahildir ve iki farklı entrypoint'e sahiptir.


```
import { createApi } from '@reduxjs/toolkit/query'

/* React-specific entry point that automatically generates
   hooks corresponding to the defined endpoints */
import { createApi } from '@reduxjs/toolkit/query/react'

```

React ile kullanımında, createApi ile API Slice oluşturulur. Bu slice da base api url ve endpointler listelenir.

```
import { createApi, fetchBaseQuery } from '@reduxjs/toolkit/query/react'
import type { Pokemon } from './types'

// Servis base adresi ve gerekli endpopintleri burada tanımlıyoruz.
export const pokemonApi = createApi({
  reducerPath: 'pokemonApi',
  baseQuery: fetchBaseQuery({ baseUrl: 'https://pokeapi.co/api/v2/' }),
  endpoints: (builder) => ({
    getPokemonByName: builder.query<Pokemon, string>({
      query: (name) => `pokemon/${name}`,
    }),
  }),
})

// Export hooks for usage in functional components, which are
// auto-generated based on the defined endpoints
export const { useGetPokemonByNameQuery } = pokemonApi
```

API Slice auto generated reducerları ve middlewareleri içerir bu yüzden redux store'a eklememiz gerekli.

```
import { configureStore } from '@reduxjs/toolkit'
import { setupListeners } from '@reduxjs/toolkit/query'
import { pokemonApi } from './services/pokemon'

export const store = configureStore({
  reducer: {
    // Add the generated reducer as a specific top-level slice
    [pokemonApi.reducerPath]: pokemonApi.reducer,
  },
  // Adding the api middleware enables caching, invalidation, polling,
  // and other useful features of `rtk-query`.
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware().concat(pokemonApi.middleware),
})

// optional, but required for refetchOnFocus/refetchOnReconnect behaviors
// see `setupListeners` docs - takes an optional callback as the 2nd arg for customization
setupListeners(store.dispatch)
```

component içinde kullanımı aşağıdaki gibidir.

```
import * as React from 'react'
import { useGetPokemonByNameQuery } from './services/pokemon'

export default function App() {
  // Using a query hook automatically fetches data and returns query values
  const { data, error, isLoading } = useGetPokemonByNameQuery('bulbasaur')
  
  // Individual hooks are also accessible under the generated endpoints:
  // const { data, error, isLoading } = pokemonApi.endpoints.getPokemonByName.useQuery('bulbasaur')
  
  // render UI based on data and loading state
}
```

***RTK Query includes these APIs:***

- `createApi()` : Çekirdek fonksiyonelliği taşır. Datanın nasıl gösterileceği, nasıl configure edileceği ve nasıl dönüştürüleceğine dair endpointleri tanımlanmanızı sağlar. Çoğu durumda her uygulama için bir adet base API Slice kullanılır.

- `fetchBaseQuery()`: istekleri basitleştiren `fetch` i sarmalayan bir wrapperdır. Kullanıcıların çoğunluğu için `createApi` de kullanılması önerilen baseQuery olarak tasarlanmıştır.

- `<ApiProvider/>`: Hazır bir redux store'unuz yoksa kullanılır.

- `setupListeners()`: `refetchOnMount` ve `refetchOnReconnect ` davranışlarını etkinleştirmek için kullanılır.

***Genel Kullanım Nedenleri:***
- Halihazırda bir redux uygulamanız var ve data fetching mantığını basitleştirmek istiyorsunuz.

- History bilgisi için redux dev toolsu kullanmak istiyorsunuz.
- Uygulamanızın mantığının react dışında çalışması gerekiyor.

Diğer toollar ile ne gibi farklılıkları var detaylı bilgi için [***dökümana***](https://redux-toolkit.js.org/rtk-query/comparison) gidebilirsiniz.

