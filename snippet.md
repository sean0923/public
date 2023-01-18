
#### typescriptreact.json
```json
{
  "import": {
    "key": "import",
    "prefix": "imp",
    "body": ["import ${2:second} from '${1:first}'"],
    "scope": "typescript,typescriptreact,javascript,javascriptreact"
  },
  "typescriptReactArrowFunctionComponent": {
    "key": "typescriptReactArrowFunctionComponent",
    "prefix": "rfc",
    "body": [
      "export const ${1:TEMP} = () => {",
      "  return (",
      "    <div>",
      "      <div>${1:first}</div>",
      "    </div>",
      "  )",
      "}",
      ""
    ],
    "description": "Creates a React Arrow Function Component with ES7 module system and TypeScript interface",
    "scope": "typescript,typescriptreact,javascript,javascriptreact"
  }
}
```


