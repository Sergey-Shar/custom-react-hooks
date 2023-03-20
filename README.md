
<h1 align='center'> React Custom Hooks Using Typescript</h1>

## In this repository, I decided to collect hooks that I somehow use in my React projects.
Maybe someone will find them useful too. Or some of you want to share your experience in this regard. 
I welcome it.

<details>
<summary>useDebounce { hook 🪝  }</summary>

```typescript
import { useEffect } from 'react';
import { useTimeout } from "./timeout";

export function useDebounce<T>(
 callback:Function,
 delay: number,
 dependencies: T[]
):void {

 // the useTimeout hook is described below...
 const { resetTimeout, clearTimeout } = useTimeout(callback, delay)
 
 useEffect(resetTimeout, [...dependencies, resetTimeout])
 
 useEffect(clearTimeout, [])
}
```
</details>

<details>
<summary>useTimeout{ hook 🪝 }</summary>

```typescript
import { useCallback, useEffect, useRef } from 'react'

interface IUseTimeout {
 clearTimeout: () => void
 resetTimeout: () => void
}

export function useTimeout<S>(callback: Function, delay: number): IUseTimeout {
 const callbackRef = useRef(callback)
 const timeoutId = useRef<number | null>(null)

 useEffect(() => {
  callbackRef.current = callback
 }, [callback])

 const startTimeout = useCallback(() => {
  timeoutId.current = setTimeout(() => callbackRef.current(), delay)
 }, [delay])

 const clearTimeout = useCallback(() => {
  if (timeoutId.current) {
  clearInterval(timeoutId.current)
  }
 }, [])

 const resetTimeout = useCallback(() => {
  clearTimeout()
  startTimeout()
 }, [startTimeout, clearTimeout])

 useEffect(() => {
  startTimeout()
  return clearTimeout
 }, [delay, clearTimeout, startTimeout])

 return {
  clearTimeout,
  resetTimeout
 }
}
```
</details>
	
<details>
<summary>useThrottle{ hook 🪝 }</summary>

```typescript
import { useState, useRef, useEffect } from 'react'

export function useThrottle<T>(value: T, interval = 250):T {
 const [throttleValue, setThrottleValue] = useState(value)
 const lastExecuted = useRef(Date.now())

 useEffect(() => {
  if (Date.now() >= lastExecuted.current + interval) {
   lastExecuted.current = Date.now()
   setThrottleValue(value)
  } else {
    const id = setTimeout(() => {
     lastExecuted.current = Date.now()
     setThrottleValue(value)
    }, interval)
    return () => clearInterval(id)
   }
  }, [value, interval])

  return throttleValue
}
```
</details>
	
<details>
<summary>useFetch{ hook 🪝 }</summary>

```typescript
import { useCallback, useEffect, useState } from 'react'
import axios, { AxiosError } from 'axios'

interface IFetch<T> {
 data: T[]
 isLoading: boolean
 error: AxiosError | null
 refetch: (options: {}) => Promise<void>
}

export function useFetch<T>(url: string, options = {}): IFetch<T> {
const [data, setData] = useState<T[]>([])
const [isLoading, setLoading] = useState(false)
const [error, setError] = useState<AxiosError | null>(null)

useEffect(() => {
 getFetch(options)
 }, [])

const getFetch = useCallback(async (opt = options) => {
 setLoading(true)
  try {
   const { data } = await axios.get<T[]>(url, { ...opt })
   setData(data)
  } catch (error) {
   if (axios.isAxiosError(error)) {
   setError(error)
  }
  } finally {
   setLoading(false)
  }
 }, [])

 return { data, isLoading, error, refetch: getFetch }
}
```
</details>

<details>
<summary>useLocalStorage { hook 🪝 }</summary>

```typescript
import { useEffect, useState } from 'react'

const getValue = <T>(initialValue: T, key: string):T => {
const storage = localStorage.getItem(key)
	
if (storage !== null) {
 return JSON.parse(storage)
  }
 if (initialValue instanceof Function) {
 return initialValue()
  }
 return initialValue
}

export function useLocalStorage<T>(initialValue: T, key: string) {
 const [value, setValue] = useState(() => getValue(initialValue,key))
	
 useEffect(() => {
  localStorage.setItem(key, JSON.stringify(value))
   }, [value])
   
 return { value, setValue }
}
```
</details>

<details>	
<summary>useInput { hook 🪝 }</summary> 

```typescript
import { useCallback, useState } from 'react'

export const useInput = (initialValue: string) => {
 const [value, setValue] = useState(initialValue)
 
 const onChange: ChangeEventHandler<HTMLInputElement> = useCallback(
  ({ target: { value } }) => {
  setValue(value)},[])

 const ref = useCallback((element: HTMLInputElement): void => element?.focus(), [])

 const clear = useCallback(() => setValue(''), [])

 return { clear, value, onChange, ref }
}
```
</details>

<details>
<summary>useValidation { hook 🪝 }</summary>

```typescript
export const useValidation = (value , validations) => {
    
 const [errorEmpty, setErrorEmpty] = useState('');
 const [errorName, setErrorName] = useState('');

 const [isEmpty, setEmpty] = useState(true);
 const [isName, setName] = useState(true);

 const regEX = useMemo(() => /^[a-zA-Z][a-zA-Z0-9-_.]{3,20}$/, []);
    
 useEffect(() => {
  for (const validation in validations) {
   switch (validation) {
   case 'isEmpty':
   if (value.trim().length) {
    setEmpty(false);
   }
   else {
    setEmpty(true);
    setErrorEmpty('The field cannot be empty');
   }
    break;
    case 'isName':     
     if (regEX.test(value)) {
      setName(false);
   } else {
      setName(true);
      setErrorName('Enter the correct name');
   }
    break;
      }
    }
  }, [regEX, validations, value]);

  return{ isEmpty, isName, errorEmpty, errorName };
};
```
</details>
	
<details>
<summary>useToggleTheme { hook 🪝 utilize for Tailwind.css }</summary>

```typescript
import { useLayoutEffect, useState } from 'react';

export const useDarkMode = () => {
 const [theme, setTheme] = useState<string>(localStorage.theme);
 const colorTheme = theme === 'dark' ? 'light' : 'dark';
 
 useLayoutEffect(() => {
   const root = window.document.documentElement;
   root.classList.remove(colorTheme);
   root.classList.add(theme);
   localStorage.setItem('theme', theme);
   }, [colorTheme, theme]);

   return { colorTheme, setTheme };
};
```
</details>

<details>
<summary>useToggle { hook 🪝  }</summary>

```typescript
import { useState, useCallback } from 'react';

export const useToggle = (initialState: boolean) => {
 const [isState, setState] = useState(initialState)

 const toggleValue = useCallback(() => {
  setState((currentState) => !currentState)
 }, [])

 return { isState, toggleValue }
}
```
</details>
	
<details>	
<summary>useActions { hook  🪝 utilize for Redux Toolkit }</summary>

```typescript
import { bindActionCreators } from '@reduxjs/toolkit';
import { actions } from 'redux/actions';

import { useAppDispatch } from './useRedux';

export const useActions = () => {
 const dispatch = useAppDispatch();
 return bindActionCreators(actions, dispatch);
};
```
</details>
	
<details>	
<summary>useCount { hook 🪝  }</summary>

```typescript
import { useState, useCallback, useEffect } from 'react';

export const useCount = (initialValue: number) => {
  const [count, setCount] = useState(initialValue);
  useEffect(() => {
    if (count < 0) {
      setCount(0);
    }
  }, [count]);

  const increment = useCallback(() => {
    setCount((currentValue) => currentValue + 1);
  }, []);

  const decrement = useCallback(() => {
    setCount((currentValue) => currentValue - 1);
  }, []);
    
  return { count, increment, decrement };
};
}
```
</details>



