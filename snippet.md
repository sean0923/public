

```json
{
  "Print to console": {
    "prefix": "log",
    "body": ["console.log('$1');", "$2"],
    "description": "Log output to console"
  },
  "Import ": {
    "prefix": "imp",
    "body": ["import ${2:lib} from '$1'"]
  },
  "React Basic Functional Component": {
    "prefix": "rfc",
    "body": [
      "export const ${0:TEMP} = () => {",
      "  return (",
      "    <div>",
      "      <div>${0:TEMP}</div>",
      "    </div>",
      "  );",
      "};",
      ""
    ]
  },
  "Legacy React Basic Functional Component": {
    "prefix": "legacy_rfc",
    "body": [
      "import React from 'react'",
      "",
      "export const ${0:TEMP}: React.FC = () => {",
      "  return (",
      "    <div>",
      "      <div>${0:TEMP}</div>",
      "    </div>",
      "  );",
      "};",
      ""
    ]
  }
}

```
