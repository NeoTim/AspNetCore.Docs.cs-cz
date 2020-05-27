Stránka vytvořená `Authentication` komponentou (*stránky/ověřování. Razor*) definuje trasy vyžadované pro zpracování různých fází ověřování.

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>Součást:

* Poskytuje balíček [Microsoft. AspNetCore. Components. WebAssembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) .
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
