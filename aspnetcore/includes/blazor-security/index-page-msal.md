Stránka index page ( `wwwroot/index.html` ) obsahuje skript, který definuje `AuthenticationService` v jazyce JavaScript. `AuthenticationService` zpracovává podrobnosti nízké úrovně protokolu OIDC. Aplikace interně volá metody definované ve skriptu k provedení operací ověřování.

```html
<script src="_content/Microsoft.Authentication.WebAssembly.Msal/
    AuthenticationService.js"></script>
```
