## **Redux** | Introducción y conceptos   

- **Redux:** Es un patrón de diseño que se construye en base a Reducers para poder administrar el estado global de una aplicación. No es algo exclusivo de React, sino que es un patrón general que se puede aplicar en cualquier framework. 

- **React Redux:** Fue la primera librería que permitió la implementación del patrón Redux en aplicaciones de React. Se utilizó por mucho tiempo como la solución estándar en el ecosistema, pero depende de mucho boilerplate y es bastante verbosa su implementación.  

- **Redux Toolkit:** Vino a solucionar las incomodidades más  frecuentes de usar React Redux. Es una librería que nos brinda una serie de herramientas que ayudan a implementar el patrón de diseño Redux en aplicaciones, pero de manera más simple que las soluciones anteriores.    
- **RTK Query:** Es una herramienta para manejar data fetching y caché en aplicaciones que utilizan Redux Toolkit.  

## **Redux** | Toolkit  

Info oficial: https://redux-toolkit.js.org/  

- **Store**: Fuente única de la verdad, allí se encuentra la información que mis componente consumirán. 

- **Store Provider:** Es el componente que nos permite proveer el store a toda la aplicación. Se coloca en el punto más alto de la app que necesita tener acceso al state global. En general va en `main.tsx` o en `app.tsx`  

- **Slices:** Nos permiten manejar los reducers de cada funcionalidad de la app. Por ejemplo: auth, counter, providers, etc. Se compone en su forma más básica de un objeto con los siguientes elementos: name, initial state y reducers.  

Estructura básica de un Slice en Redux Toolkit

```js
import { createSlice } from '@reduxjs/toolkit'  
import type { PayloadAction } from '@reduxjs/toolkit'  
  
export interface CounterState {  
	value: number  
}  
  
const initialState: CounterState = {  
	value: 0,  
}

export const counterSlice = createSlice({
	name: 'counter',
	initialState,
	reducers: {
		increment: (state) => {
			state.value += 1;
		},
		decrement: (state) => {
			state.value -= 1;
		},
		incrementByAmount: (state, action: PayloadAction<number>) => {
			state.value += action.payload;
		},
	},
});

export const { increment, decrement, incrementByAmount } = counterSlice.actions;

export default counterSlice.reducer  
```

- `useAppSelector()`: Es el hook que nos permite seleccionar un slice de nuestro store. Nos permite elegir con qué datos queremos trabajar de los que maneja nuestro store.  

- `useAppDispatch()`: Es el hook que nos permite despachar acciones. Cada vez que necesitemos activar una acción, lo vamos a hacer usando este hook y el reducer de nuestro slice que queramos invocar.  

- **Thunk**: Es una acción que gestiona código asíncrono dentro de Redux. Siempre que una tarea sea asíncrona debemos utilizar los thunks para disparar nuestras acciones. Los casos más comunes de uso son las peticiones a endpoints.  

Ejemplo de uso de `createAsyncThunk` :

```js
import { createAsyncThunk, createSlice } from '@reduxjs/toolkit'  
import { userAPI } from './userAPI'  
  
// First, create the thunk  
const fetchUserById = createAsyncThunk(  
'users/fetchByIdStatus',  
	async (userId: number, thunkAPI) => {  
	const response = await userAPI.fetchById(userId) 
	return response.data  
},  
)  
  
interface UsersState {  
	entities: User[]  
	loading: 'idle' | 'pending' | 'succeeded' | 'failed'  
}  
  
const initialState = {  
	entities: [],  
	loading: 'idle',  
} satisfies UserState as UsersState  
  
// Then, handle actions in your reducers:  
const usersSlice = createSlice({  
	name: 'users',  
	initialState,  
	reducers: {  
// standard reducer logic, with auto-generated action types per reducer  
	},  
	extraReducers: (builder) => {  
// Add reducers for additional action types here, and handle loading state as needed  
	builder.addCase(fetchUserById.fulfilled, (state, action) => {  
// Add user to the state array  
	state.entities.push(action.payload)  
		})  
	},  
})  
  
// Later, dispatch the thunk as needed in the app  
dispatch(fetchUserById(123))
```

Otra forma de manejar los thunks de manera manual:  

```js
export const getPatients = (params) => {
	return async(dispatch, getState) => {
		// state.status === "loading";
		dispatch(startLoadingPatients()); 
		
		// HTTP request
		try {
			const response = await fetch(endpoint);
			const data = await response.json();
			// populate with data state.patients;
			dispatch(setPatients(data));
		} catch(err) {
			console.error(err);
		}
	}
}
```


>[!tip] Organización de Thunks  
>Para mayor claridad se recomienda organizar los thunks en un nuevo archivo para centralizar todas las funciones que controlan tareas asíncronas de nuestros slices. Esto es opcional y según criterio personal pero puede ser una buena práctica para evitar que se extiendan demasiado los slices.  


### Diferencias entre `createAsyncThunk`, `manualThunks` y RTK Query   

En esencia sirven para lo mismo pero tienen distintas capas de abstracción. 

Si manejás tus thunks de manera manual puedes personalizar al máximo lo que sucede dentro de los mismos, pero necesitas encargarte de cada paso (los cambios de estado de la petición, la carga de la data en el slice/store, el manejo de caché, etc.)

Un `createAsyncThunk` es una **función utilitaria** para crear thunks asincrónicos. Es decir, código que despacha acciones mientras espera respuestas de APIs. Ofrecen una primera capa de abstracción, ya que manejan de forma simple los estados de las peticiones (fulfilled, pending, loading), pero no ofrecen una solución de manejo de caché. 

Por último, RTK Query es una **solución completa para data fetching**. Es como un mini-cliente de datos embebido en Redux (similar a React Query), con caching, invalidaciones, refetching, polling, etc. Ofrece muchas más posibilidades y se encargar de muchas cosas por nosotros (ver más abajo), pero requiere una inversión de configuración y lidiar con la fuerte abstracción que la herramienta propone a la hora de manejar los thunks. Si bien tenemos más problemas resueltos, también nos limita en el diseño y las capacidades de esos thunks.  

En definitiva no hay una opción correcta, sino que cada una sirve para situaciones distintas. Todo dependerá del proyecto y de las necesidades puntuales del thunk a resolver.  

## **Redux** | RTK Query  

### ¿Para qué sirve esta herramienta?

- Optimiza las peticiones HTTP: Evita hacer peticiones innecesarias ya que almacena la peticiones en caché

- Nos genera automáticamente custom Hooks para comunicarnos con nuestras APIs que nos ayudan mucho a manejar errores y estados de las peticiones  

- Gestiona los estados de las peticiones (loading, fulfilled, failed, idle)  

- No es obligatorio su uso pero funciona totalmente acoplado a Redux Toolkit, de hecho ya viene incorporado como una herramienta más dentro del conjunto de Toolkits y no es necesario instalar ningún paquete adicional.   

Ejemplo de implementación:  

```js
// todosApi.js 

import {createApi, fetchBaseQuery} from "@reduxjs/toolkit/query/react";

export const todosApi = createApi({
	reducerPath: "todos",
	baseQuery: fetchBaseQuery({
		baseUrl: "https://jsonplaceholder.typicode.com"
	}),
	endpoint: (builder) => ({
		getTodos: builder.query({
			query: () => "/todos"
		})
	})
})

export const { useGetTodosQuery } = todosApi;
```

```js
// store.js

import { configureStore } from "@reduxjs/toolkit";
import { todosApi } from "./apis";

export const store = configureStore({
	reducer: {
		[todosApi.reducerPath]: todosApi.reducer,
	},
	middleware: (getDefaultMiddleware) => getDefaultMiddleware()
		.concat(todosApi.middleware)
})
```

```js 
// component.js 
export const TodoApp = () => {
	const { data: todos = [], isLoading } = useGetTodosQuery();

	return (
		<>
			<p>isLoading: {isLoading ? "Loading todos" : "Loaded"} </p>

			<ul>
				{ todos.map(todo => (
					// renderizado de todos
				))
				}
			</ul>
		</>
	)
}
```

Como muy positivo del uso de RTK Query obtenemos estos custom Hooks basados en las distintas APIs que configuremos y que hacen el trabajo en los componentes mucho más sencillo y nos ahorran bastante tiempo para no tener que manejar todo con lógica propia.  

> [!TIP]
> Se puede crear un snippet de código para generar el boilerplate inicial para trabajar con RTK Query. Aquí va un ejemplo de cómo se podría manejar 👇 

```json
{  
  "RTK Query API": {  
    "prefix": "rtk-api",  
    "body": [  
      "import { createApi, fetchBaseQuery } from '@reduxjs/toolkit/query/react';",  
      "",  
      "export const ${1:apiName}Api = createApi({",  
      "  reducerPath: '${1:apiName}',",  
      "  baseQuery: fetchBaseQuery({",  
      "    baseUrl: '${2:https://api.example.com}'",  
      "  }),",  
      "  endpoints: (builder) => ({",  
      "    get${3:Resource}: builder.query({",  
      "      query: () => '/${4:endpoint}'",  
      "    }),",  
      "    get${5:ResourceById}: builder.query({",  
      "      query: (${6:id}) => `/${4:endpoint}/${${6:id}}`",  
      "    }),",  
      "    add${3:Resource}: builder.mutation({",  
      "      query: (${7:body}) => ({",  
      "        url: '/${4:endpoint}',",  
      "        method: 'POST',",  
      "        body: ${7:body}",  
      "      })",  
      "    }),",  
      "    update${3:Resource}: builder.mutation({",  
      "      query: ({ ${6:id}, ...${7:body} }) => ({",  
      "        url: `/${4:endpoint}/${${6:id}}`,",  
      "        method: 'PUT',",  
      "        body: ${7:body}",  
      "      })",  
      "    }),",  
      "    delete${3:Resource}: builder.mutation({",  
      "      query: (${6:id}) => ({",  
      "        url: `/${4:endpoint}/${${6:id}}`,",  
      "        method: 'DELETE'",  
      "      })",  
      "    })",  
      "  })",  
      "});",  
      "",  
      "export const {",  
      "  useGet${3:Resource}Query,",  
      "  useGet${5:ResourceById}Query,",  
      "  useAdd${3:Resource}Mutation,",  
      "  useUpdate${3:Resource}Mutation,",  
      "  useDelete${3:Resource}Mutation",  
      "} = ${1:apiName}Api;",  
      ""  
    ],  
    "description": "Create a Redux Toolkit Query API with common CRUD endpoints"  
  }  
}
```

## **Redux** | Herramientas adicionales  

- **Redux DevTools**: Es una extensión de Chrome que nos permite visualizar de forma súper interactiva lo que va pasando con nuestro estado global en cada momento dentro de la app. ([Link](https://chromewebstore.google.com/detail/redux-devtools/lmhkpmbekcpmknklioeibfkpmmfibljd?hl=es))  

- **Redux logger:** Es una librería que permite mostrar en consola el flujo de acciones y cambios de estado que tiene una app que utiliza Redux. Se utiliza únicamente en modo de desarrollo. Se recomienda usar Redux DevTools y no tanto redux logger, ya que no carga una dependencia extra al proyecto y no hay posibilidad de que se filtre data en producción.

- **Redux persist**: Es una librería que nos permite guardar y restaurar el estado en almacenamiento persistente entre sesiones. Utiliza `localStorage` (en Web) o `AsyncStorage` (en React Native) para almacenar la información del estado y lo restaura automáticamente al reiniciar la app.  