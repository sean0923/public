### vite react-ts
```sh
npm create vite@latest -- --template react-ts
```

vite.config.ts
```ts
import { defineConfig } from 'vitest/config';
import react from '@vitejs/plugin-react';
import tsconfigPaths from 'vite-tsconfig-paths';

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [react(), tsconfigPaths()],
  define: { 'import.meta.vitest': 'undefined' },
  test: { includeSource: ['src/**/*.{js,ts}'] },
});

```

### next-ts
```sh
npx create-next-app@latest --ts
```

```sh
npm install env-cmd
```

pacakge.json
```json
    "dev": "env-cmd -f .env.staging next",
    "dev-production": "env-cmd -f .env.production next",
```

vite.config.ts
```ts
// vite.config.ts
import { defineConfig } from 'vitest/config';
import tsconfigPaths from 'vite-tsconfig-paths';

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [tsconfigPaths()],
  define: { 'import.meta.vitest': 'undefined' },
  test: { includeSource: ['src/**/*.{js,ts}'] },
});

```

### husky init
```sh
npx husky-init && npm install
```

```sh
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

tsc
npx lint-staged
```


### npm install (eslint, prettier, lint-staged)

```sh
npm install --save-dev @typescript-eslint/eslint-plugin @typescript-eslint/parser eslint eslint-config-prettier prettier lint-staged eslint-plugin-react eslint-plugin-react-hooks
```

#### end of package.json
```json
  "lint-staged": {
    "*.{js,jsx,ts,tsx,md,html,css}": "prettier --write",
    "**/*.{js,jsx,ts,tsx}": [
      "eslint"
    ]
  }
```

#### .husky/pre-commit
```sh
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

tsc
npx lint-staged
```

#### .eslintrc.json
```json
{
  "parser": "@typescript-eslint/parser",
  "parserOptions": {
    "ecmaVersion": 2018,
    "sourceType": "module"
  },
  "plugins": ["@typescript-eslint", "react-hooks"],
  "extends": [
    "plugin:react/recommended",
    "plugin:@typescript-eslint/recommended",
    "prettier",
    "plugin:react-hooks/recommended"
  ],
  "rules": {
    "@typescript-eslint/no-namespace": "off",
    "react/no-unknown-property": "off",
    "react/react-in-jsx-scope": "off",
    "no-use-before-define": "off",
    "react/prop-types": "off",
    "import/no-anonymous-default-export": "off",
    "no-unused-vars": "off",
    "@typescript-eslint/no-unused-vars": "error"
  }
}

```

#### .prettierrc.json
```json
{
  "trailingComma": "es5",
  "tabWidth": 2,
  "semi": true,
  "singleQuote": true,
  "printWidth": 100,
  "arrowParens": "always"
}
```

#### Vitest related
https://vitest.dev/guide/#trying-vitest-online

```sh
# with npm
npm install -D vitest
```

```json
{
  "scripts": {
    "test": "vitest",
    "coverage": "vitest run --coverage",
  }
}
```

```ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    // ...
  },
})
```

https://vitest.dev/guide/in-source.html
```ts
// src/index.ts

// the implementation
export function add(...args: number[]) {
  return args.reduce((a, b) => a + b, 0)
}

// in-source test suites
if (import.meta.vitest) {
  const { it, expect } = import.meta.vitest
  it('add', () => {
    expect(add()).toBe(0)
    expect(add(1)).toBe(1)
    expect(add(1, 2, 3)).toBe(6)
  })
}
```

```ts
// vite.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  define: {
    'import.meta.vitest': 'undefined',
  },
  test: {
    includeSource: ['src/**/*.{js,ts}']
  },
})
```

```ts
// tsconfig.json
{
  "compilerOptions": {
    "types": [
      "vitest/importMeta"
    ]
  }
}
```

#### vist absolute import
```sh
npm i vite-tsconfig-paths
```
https://www.npmjs.com/package/vite-tsconfig-paths

```ts
// vite.config.ts
import tsconfigPaths from 'vite-tsconfig-paths'

export default defineConfig({
  plugins: [tsconfigPaths()],
})
```

```ts
// tsconfig.json
{
  "compilerOptions": {
    ...
    "baseUrl": "src",
    ...
  }
}
```
or
```ts
"baseUrl": "./",
"paths": {
  "@@app/*": ["src/*"]
}
```

#### three fiber related
```sh
npm install three @react-three/fiber @types/three @react-three/drei
```

#### redux-toolkit
```sh
npm install @reduxjs/toolkit react-redux
```

store.ts
```ts
// * https://redux-toolkit.js.org/tutorials/typescript

import { configureStore } from '@reduxjs/toolkit';
import { TypedUseSelectorHook, useDispatch, useSelector } from 'react-redux';
import { appReducer } from './app/app.slice';

export const store = configureStore({
  reducer: {
    app: appReducer,
  },
});

// Infer the `RootState` and `AppDispatch` types from the store itself
export type RootState = ReturnType<typeof store.getState>;
// Inferred type: {posts: PostsState, comments: CommentsState, users: UsersState}
export type AppDispatch = typeof store.dispatch;

// Use throughout your app instead of plain `useDispatch` and `useSelector`
export const useAppDispatch: () => AppDispatch = useDispatch;
export const useAppSelector: TypedUseSelectorHook<RootState> = useSelector;
```

app.slice.ts
```ts
import { createSlice } from '@reduxjs/toolkit';
import type { PayloadAction } from '@reduxjs/toolkit';
import { RootState } from '../store';

interface AppState {
  value: number;
}

const initialState: AppState = {
  value: 0,
};

export const appSlice = createSlice({
  name: 'app',
  initialState,
  reducers: {
    increment: (state) => {
      state.value += 1;
    },
    decrement: (state) => {
      state.value -= 1;
    },
    // Use the PayloadAction type to declare the contents of `action.payload`
    incrementByAmount: (state, action: PayloadAction<number>) => {
      state.value += action.payload;
    },
  },
});

export const { increment, decrement, incrementByAmount } = appSlice.actions;

// selectors
export const selectAppValue = (state: RootState) => state.app.value;

// reducer
export const appReducer = appSlice.reducer;

```

#### react-query
```sh
npm i @tanstack/react-query @tanstack/react-query-devtools
```

```ts
import { QueryClient } from '@tanstack/react-query';

export const queryClient = new QueryClient({
  defaultOptions: { queries: { retry: false, refetchOnWindowFocus: false } },
});
```

#### mantine
```sh
npm install @mantine/core @mantine/hooks @mantine/dates dayjs @mantine/notifications
```

### react-router
```sh
npm i react-router react-router-dom
```

### lodash, styled-components, axios
```sh
npm i lodash styled-components axios
```

#### main.tsx
```tsx
import { ReactQueryDevtools } from '@tanstack/react-query-devtools'

const Component = () => {
  return (
    <>
      <Provider store={store}>
        <QueryClientProvider client={queryClient}>
          <MantineProvider withGlobalStyles withNormalizeCSS>
            <ReactQueryDevtools />
            <App />
          </MantineProvider>
        </QueryClientProvider>
      </Provider>
    </>
  );
};
```
