Stránka vytvořená komponentou `Authentication` (*stránky/ověřování. Razor*) definuje trasy vyžadované pro zpracování různých fází ověřování.

Součást `RemoteAuthenticatorView`:

* Je k dispozici v balíčku `Microsoft.AspNetCore.Components.WebAssembly.Authentication`.
* Spravuje provádění příslušných akcí v každé fázi ověřování.

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
