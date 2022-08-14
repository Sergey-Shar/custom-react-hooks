## useLocalStorage { hook 🪝 }
```typescript
import { useCallback, useEffect, useState } from 'react';

export function useLocalStorage<T>(initialValue: T, key: string) {
	const getValue = useCallback(() => {
		const storage = localStorage.getItem(key);
		if (storage) {
			try {
				return JSON.parse(storage);
			} catch (error) {
				console.warn(`Error reading localStorage value “${storage}”:`, error);
			}
		}
		return initialValue;
	}, [initialValue, key]);

	const [value, setValue] = useState(getValue);

	useEffect(() => {
		localStorage.setItem(key, JSON.stringify(value));
	}, [key, value]);

	return [value, setValue];
}
```

## useInput { hook 🪝 } 
```typescript
import React, { useCallback, useState } from 'react';

interface UseInputValue {
  clear: () => void;
  value: string;
  onChahge: (event: React.ChangeEvent<HTMLInputElement>) => void;
}

export const useInput = (initialValue: string): UseInputValue => {
  const [value, setValue] = useState(initialValue);

  const onChahge = useCallback((event: React.ChangeEvent<HTMLInputElement>) => {
    setValue(event.target.value);
  }, []);
  
    const onBlur = useCallback((event: React.ChangeEvent<HTMLInputElement>) => {
    setDirty(true);
  }, []);

  const ref = useCallback((element: HTMLInputElement) => {
    element?.focus();
  }, []);

  const clear = useCallback(() => setValue(''), []);

  return {
    clear,
    value,
    onChahge,
    onBlur,
    ref,
  };
};

## useValidation { hook 🪝 }
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

  return{isEmpty,isName,errorEmpty,errorName};
};
```

## useToggleTheme { hook 🪝 utilize for Tailwind.css }
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

	return {
		colorTheme,
		setTheme,
	};
};
```

## useToggle { hook 🪝  }
```typescript
import React from 'react';

export const useToggle = (initialState:boolean) => {

  const [state, setState] = React.useState(initialState);

  const toggle = useCallback(() => {
    setState((currentState) => !currentState);
  },[]);

  return {
    state,
    toggle,
    setState,
  };
};
```

## useActions { hook  🪝 utilize for Redux Toolkit }
```typescript
import { bindActionCreators } from '@reduxjs/toolkit';
import { actions } from 'redux/actions';

import { useAppDispatch } from './useRedux';

export const useActions = () => {
  const dispatch = useAppDispatch();
  return bindActionCreators(actions, dispatch);
};
```

## useCount { hook 🪝  }
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



