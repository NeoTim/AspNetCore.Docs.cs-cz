Stránka vytvořená `Authentication` komponentou ( `Pages/Authentication.razor` ) definuje trasy vyžadované pro zpracování různých fází ověřování.

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>Součást:

* Je součástí [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) balíčku.
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
