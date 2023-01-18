
#### typescriptreact.json
```json
{
  // Place your snippets for typescriptreact here. Each snippet is defined under a snippet name and has a prefix, body and
  // description. The prefix is what is used to trigger the snippet and the body will be expanded and inserted. Possible variables are:
  // $1, $2 for tab stops, $0 for the final cursor position, and ${1:label}, ${2:another} for placeholders. Placeholders with the
  // same ids are connected.
  // Example:
  // "Print to console": {
  // 	"prefix": "log",
  // 	"body": [
  // 		"console.log('$1');",
  // 		"$2"
  // 	],
  // 	"description": "Log output to console"
  // }
  // "typescriptReactArrowFunctionComponent": {
  //   "key": "typescriptReactArrowFunctionComponent",
  //   "prefix": "rfc",
  //   "body": [
  //     "import React from 'react'",
  //     "",
  //     "type Props = {}",
  //     "",
  //     "const ${1:${TM_FILENAME_BASE}} = (props: Props) => {",
  //     "  return (",
  //     "    <div>${1:first}</div>",
  //     "  )",
  //     "}"
  //   ],
  //   "description": "Creates a React Arrow Function Component with ES7 module system and TypeScript interface",
  //   "scope": "typescript,typescriptreact,javascript,javascriptreact"
  // }
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


