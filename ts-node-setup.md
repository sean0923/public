##### from: https://khalilstemmler.com/blogs/typescript/node-starter-project/

```sh
npm init -y
```

```sh
npm install typescript @types/node ts-node --save-dev
```

```sh
npx tsc --init --rootDir src --outDir build \
--esModuleInterop --resolveJsonModule --lib es6 \
--module commonjs --allowJs true --noImplicitAny true
```

for global install
```sh
npm install -g ts-node
```

```sh
npm install -g typscript
```
