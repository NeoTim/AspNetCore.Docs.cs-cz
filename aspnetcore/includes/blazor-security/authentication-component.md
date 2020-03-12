<span data-ttu-id="2421e-101">Stránka vytvořená komponentou `Authentication` (*stránky/ověřování. Razor*) definuje trasy vyžadované pro zpracování různých fází ověřování.</span><span class="sxs-lookup"><span data-stu-id="2421e-101">The page produced by the `Authentication` component (*Pages/Authentication.razor*) defines the routes required for handling different authentication stages.</span></span>

<span data-ttu-id="2421e-102">Součást `RemoteAuthenticatorView`:</span><span class="sxs-lookup"><span data-stu-id="2421e-102">The `RemoteAuthenticatorView` component:</span></span>

* <span data-ttu-id="2421e-103">Je k dispozici v balíčku `Microsoft.AspNetCore.Components.WebAssembly.Authentication`.</span><span class="sxs-lookup"><span data-stu-id="2421e-103">Is provided by the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span>
* <span data-ttu-id="2421e-104">Spravuje provádění příslušných akcí v každé fázi ověřování.</span><span class="sxs-lookup"><span data-stu-id="2421e-104">Manages performing the appropriate actions at each stage of authentication.</span></span>

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
