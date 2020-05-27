<span data-ttu-id="98308-101">Stránka index stránky (*wwwroot/index.html*) obsahuje skript, který definuje `AuthenticationService` v jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="98308-101">The Index page (*wwwroot/index.html*) page includes a script that defines the `AuthenticationService` in JavaScript.</span></span> <span data-ttu-id="98308-102">`AuthenticationService`zpracovává podrobnosti nízké úrovně protokolu OIDC.</span><span class="sxs-lookup"><span data-stu-id="98308-102">`AuthenticationService` handles the low-level details of the the OIDC protocol.</span></span> <span data-ttu-id="98308-103">Aplikace interně volá metody definované ve skriptu k provedení operací ověřování.</span><span class="sxs-lookup"><span data-stu-id="98308-103">The app internally calls methods defined in the script to perform the authentication operations.</span></span>

```html
<script src="_content/Microsoft.Authentication.WebAssembly.Msal/
    AuthenticationService.js"></script>
```
