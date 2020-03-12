Součást `RedirectToLogin` (*Shared/RedirectToLogin. Razor*):

* Spravuje přesměrování neautorizovaných uživatelů na přihlašovací stránku.
* Zachová aktuální adresu URL, ke které se uživatel pokouší získat přístup, aby se mohla vrátit na tuto stránku, pokud je ověření úspěšné.

```razor
@inject NavigationManager Navigation
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo($"authentication/login?returnUrl={Navigation.Uri}");
    }
}
```
