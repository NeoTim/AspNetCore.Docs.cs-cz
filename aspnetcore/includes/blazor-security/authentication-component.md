<span data-ttu-id="4e172-101">Stránka vytvořená `Authentication` komponentou ( `Pages/Authentication.razor` ) definuje trasy vyžadované pro zpracování různých fází ověřování.</span><span class="sxs-lookup"><span data-stu-id="4e172-101">The page produced by the `Authentication` component (`Pages/Authentication.razor`) defines the routes required for handling different authentication stages.</span></span>

<span data-ttu-id="4e172-102"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>Součást:</span><span class="sxs-lookup"><span data-stu-id="4e172-102">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> component:</span></span>

* <span data-ttu-id="4e172-103">Je součástí [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) balíčku.</span><span class="sxs-lookup"><span data-stu-id="4e172-103">Is provided by the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span>
* <span data-ttu-id="4e172-104">Spravuje provádění příslušných akcí v každé fázi ověřování.</span><span class="sxs-lookup"><span data-stu-id="4e172-104">Manages performing the appropriate actions at each stage of authentication.</span></span>

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
