## useLocalStorage hook
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

## useInput hook
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

  const clear = useCallback(() => setValue(''), []);

  return {
    clear,
    value,
    onChahge,
  };
};
```


