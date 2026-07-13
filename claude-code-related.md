### settings.json
```json
{
  "sandbox": { "enabled": true, "autoAllowBashIfSandboxed": true },
  "permissions": {
    "allow": ["*"],
    "deny": ["Read(./.env)", "Read(./.env.*)", "Edit(./.env)", "Edit(./.env.*)"]
  }
}
```
