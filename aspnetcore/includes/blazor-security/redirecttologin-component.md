<span data-ttu-id="789ed-101">`RedirectToLogin`Součást (*Shared/RedirectToLogin. Razor*):</span><span class="sxs-lookup"><span data-stu-id="789ed-101">The `RedirectToLogin` component (*Shared/RedirectToLogin.razor*):</span></span>

* <span data-ttu-id="789ed-102">Spravuje přesměrování neautorizovaných uživatelů na přihlašovací stránku.</span><span class="sxs-lookup"><span data-stu-id="789ed-102">Manages redirecting unauthorized users to the login page.</span></span>
* <span data-ttu-id="789ed-103">Zachová aktuální adresu URL, ke které se uživatel pokouší získat přístup, aby se mohla vrátit na tuto stránku, pokud je ověření úspěšné.</span><span class="sxs-lookup"><span data-stu-id="789ed-103">Preserves the current URL that the user is attempting to access so that they can be returned to that page if authentication is successful.</span></span>

```razor
@inject NavigationManager Navigation
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo($"authentication/login?returnUrl=" +
            Uri.EscapeDataString(Navigation.Uri));
    }
}
```
