### prettier in package.json

```sh
pnpm install --save-dev prettier-plugin-tailwindcss
```

```json
  "prettier": {
    "trailingComma": "es5",
    "tabWidth": 2,
    "semi": true,
    "singleQuote": true,
    "printWidth": 100,
    "arrowParens": "always",
    "plugins": [
      "prettier-plugin-tailwindcss"
    ]
  }
```

### notify.utils.ts
```ts
import toast from 'react-hot-toast';
import axios from 'axios';

export const notifySuccess = (message?: string) => {
  toast.success(message || 'Success');
};

export const notifyError = (err: unknown) => {
  if (axios.isAxiosError(err)) {
    toast.error('Error: ' + err.message);
  } else if (err instanceof Error) {
    toast.error('Error: ' + err.message);
  } else {
    toast.error('Unexpected Error');
  }
};

```

### auth-provider.tsx
```tsx
'use client';

import { clientAuth } from '@/utils/firebase-client';
import {
  GoogleAuthProvider,
  User,
  onAuthStateChanged,
  signInWithPopup,
  signOut,
} from 'firebase/auth';
import { useRouter } from 'next/navigation';
import { ReactNode, createContext, useContext, useEffect, useState } from 'react';

const provider = new GoogleAuthProvider();

interface CurrClientAuth {
  user: User | null;
  hasAuthAttempt: boolean;
}
interface AuthContextOutput {
  currClientAuth: CurrClientAuth;
  uid: string;
  email: string;
  loginWithGoogle: () => Promise<void>;
  logOut: () => Promise<void>;
}

const AuthContext = createContext<AuthContextOutput>(null!);

// const initCurrClientAuth: CurrClientAuth = { user: null, hasAuthAttempt: false };

export const AuthProvider = (props: { children: ReactNode }) => {
  const router = useRouter();
  const [currClientAuth, setCurrClientAuth] = useState<CurrClientAuth>({
    user: null,
    hasAuthAttempt: false,
  });

  useEffect(() => {
    const unsub = onAuthStateChanged(clientAuth, async (authUser) => {
      if (authUser) {
        setCurrClientAuth({ user: authUser, hasAuthAttempt: true });
      } else {
        setCurrClientAuth({ user: null, hasAuthAttempt: true });
      }
    });

    return () => {
      unsub();
    };
  }, []);

  const loginWithGoogle = async () => {
    await signInWithPopup(clientAuth, provider);
  };

  const logOut = async () => {
    await signOut(clientAuth);
    router.push('/');
  };

  const uid = currClientAuth?.user?.uid ?? '';
  const email = currClientAuth?.user?.email ?? '';

  return (
    <AuthContext.Provider
      value={{
        currClientAuth,
        loginWithGoogle,
        logOut,
        uid,
        email,
      }}
    >
      {props.children}
    </AuthContext.Provider>
  );
};

export const useAuthContext = () => useContext(AuthContext);

```

### vite react-ts
```sh
pnpm create vite@latest -- --template react-ts
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

### pnpm workspace

#### pnpm-workspace.yaml
```yaml
packages:
  - "fifo-store"
  - "fifo-manager"
  - "functions"
  - "dev"
  - "packages/**"
```

#### packages/@@utils
```
{
  "name": "@@utils",
  "version": "1.0.0",
  "description": "",
  "main": "src/index.ts",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "@@types": "workspace:*",
    "dayjs": "^1.11.10",
    "lodash": "^4.17.21",
    "zod": "^3.22.4"
  },
  "devDependencies": {
    "@types/lodash": "^4.14.199"
  }
}
```


### husky init
```sh
pnpm dlx husky-init && pnpm install
```

```sh
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

tsc
npx lint-staged
```


### pnpm install (eslint, prettier, lint-staged)

```sh
pnpm install --save-dev @typescript-eslint/eslint-plugin @typescript-eslint/parser eslint eslint-config-prettier prettier lint-staged eslint-plugin-react eslint-plugin-react-hooks
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
    "ecmaVersion": "latest",
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
    "@typescript-eslint/no-explicit-any": "off",
    "react/no-unescaped-entities": "off",
    //
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

#### firebase-admin.ts
```ts
import * as admin from "firebase-admin";
import { cert, getApp, getApps } from "firebase-admin/app";
import { getAuth } from "firebase-admin/auth";
import { getFirestore } from "firebase-admin/firestore";

const projectId = process.env.FIREBASE_PROJECT_ID;
const clientEmail = process.env.FIREBASE_CLIENT_EMAIL;
const privateKey = process.env.FIREBASE_PRIVATE_KEY!.replace(/\\n/g, "\n");

const credential = cert({ projectId, clientEmail, privateKey });

export const app = !getApps().length
  ? admin.initializeApp({ credential })
  : getApp();

export const adminDb = getFirestore(app);
export const adminAuth = getAuth(app);

```

#### firebase-client.ts
```ts
'use client';

// Import the functions you need from the SDKs you need
import { getApp, getApps, initializeApp } from 'firebase/app';
import { getAuth } from 'firebase/auth';
// import { getFirestore } from 'firebase/firestore';
// TODO: Add SDKs for Firebase products that you want to use
// https://firebase.google.com/docs/web/setup#available-libraries

// Your web app's Firebase configuration
const firebaseConfig = {
  // ...
};

// Initialize Firebase
const app = !getApps().length ? initializeApp(firebaseConfig) : getApp();

// export const clientDb = getFirestore(app);
export const clientAuth = getAuth(app);

```

#### Vitest related
https://vitest.dev/guide/#trying-vitest-online

```sh
# with pnpm
pnpm install -D vitest
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
pnpm i vite-tsconfig-paths
```
https://www.pnpmjs.com/package/vite-tsconfig-paths

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
pnpm install three @react-three/fiber @types/three @react-three/drei
```

#### redux-toolkit
```sh
pnpm install @reduxjs/toolkit react-redux
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

#### react-query-provider
```tsx
"use client";

import { QueryClient, QueryClientProvider } from "@tanstack/react-query";
import { ReactNode } from "react";

export const queryClient = new QueryClient({
  defaultOptions: { queries: { retry: false, refetchOnWindowFocus: false } },
});

export const ReactQueryProvider = (props: { children: ReactNode }) => {
  return (
    <QueryClientProvider client={queryClient}>
      {props.children}
    </QueryClientProvider>
  );
};

```

#### react-query
```sh
pnpm i @tanstack/react-query @tanstack/react-query-devtools
```

```ts
import { QueryClient } from '@tanstack/react-query';

export const queryClient = new QueryClient({
  defaultOptions: { queries: { retry: false, refetchOnWindowFocus: false } },
});
```

#### mantine
```sh
pnpm install @mantine/core @mantine/hooks @mantine/dates dayjs @mantine/notifications
```

#### zustand
```sh
pnpm install zustand
```

```ts
import { create } from 'zustand';

interface TempStoreState {
  state: string;
  setState: (state: string) => void;
}

export const useStateStore = create<TempStoreState>()((set) => ({
  state: '',
  setState: (state) => set({ state }),
}));
```

#### zod
```sh
pnpm add zod
```

- examples
```ts
export const phoneNumberSchema = z.string().refine(
  (phone) => {
    // 234-234-2344
    return /^\d{3}-\d{3}-\d{4}$/.test(phone);
  },
  { message: 'Invalid phone number. Format must be ###-###-####.' }
);

export const ssnSchema = z.string().refine(
  (ssn) => {
    // 123-45-6789
    return /^\d{3}-\d{2}-\d{4}$/.test(ssn);
  },
  { message: 'Invalid ssn. Format must be ###-##-####.' }
);

export const validDateSchema = z.string().refine(
  (date) => {
    // 2022-09-09
    return ddjs(date, 'YYYY-MM-DD', true).isValid();
  },
  { message: 'Invalid date. Format must be YYYY-MM-DD and a valid date.' }
);

export const empDetailSchema = employeeSchema.omit({ embedding_infos: true }).extend({
  // gather these 4 from register and create emp detail
  start_date: validDateSchema,
  phone_number: phoneNumberSchema,
  ssn: z.union([z.literal(''), ssnSchema]),
  date_of_birth: z.union([z.literal(''), validDateSchema]),
  //
  uid: z.string(),
  curr_pay_type: z.enum(['HOURLY', 'SALARY']),
  hourly_pay_histories: z.array(hourlyPayHistorySchema),
  yearly_salary_histories: z.array(yearlySalaryHistorySchema),
  termination_date: z.union([z.literal(''), validDateSchema]),
  deduction: deductionSchema,
  status: z.enum(['ACTIVE', 'INACTIVE']),
  is_approved: z.boolean(), // when register false then manager has to approve
});
```

### react-router
```sh
pnpm i react-router react-router-dom
```

### normalized css
```sh
pnpm install normalize.css
```

### ROBOTO MONO FONT FROM GOOGLE FONT
```html
    <!-- GOOGLE FONT  -->
    <link rel="preconnect" href="https://fonts.googleapis.com" />
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
    <!-- ROBOTO MONO ==> font-family: 'Roboto Mono', monospace; -->
    <link
      href="https://fonts.googleapis.com/css2?family=Roboto+Mono&display=swap"
      rel="stylesheet"
    />
```

#### main.tsx
```tsx
import { MantineProvider } from '@mantine/core';
import { QueryClientProvider } from '@tanstack/react-query';
import { ReactQueryDevtools } from '@tanstack/react-query-devtools';
import React from 'react';
import ReactDOM from 'react-dom/client';
import { App } from './App';
import { queryClient } from './utils/query-client';

import 'normalize.css';
import '@mantine/core/styles.css';

ReactDOM.createRoot(document.getElementById('root')!).render(
  <React.StrictMode>
    <QueryClientProvider client={queryClient}>
      <MantineProvider>
        <ReactQueryDevtools />
        <App />
      </MantineProvider>
    </QueryClientProvider>
  </React.StrictMode>
);

```
