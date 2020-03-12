<span data-ttu-id="642c3-101">Komponenta `App` (*App. Razor*) se podobá komponentě `App` v aplikacích serveru Blazor:</span><span class="sxs-lookup"><span data-stu-id="642c3-101">The `App` component (*App.razor*) is similar to the `App` component found in Blazor Server apps:</span></span>

* <span data-ttu-id="642c3-102">Komponenta `CascadingAuthenticationState` spravuje vystavení `AuthenticationState` do zbytku aplikace.</span><span class="sxs-lookup"><span data-stu-id="642c3-102">The `CascadingAuthenticationState` component manages exposing the `AuthenticationState` to the rest of the app.</span></span>
* <span data-ttu-id="642c3-103">Komponenta `AuthorizeRouteView` zajistí, že aktuální uživatel má oprávnění pro přístup k dané stránce, nebo jinak vykreslí `RedirectToLogin` komponentu.</span><span class="sxs-lookup"><span data-stu-id="642c3-103">The `AuthorizeRouteView` component makes sure that the current user is authorized to access a given page or otherwise renders the `RedirectToLogin` component.</span></span>
* <span data-ttu-id="642c3-104">Komponenta `RedirectToLogin` spravuje přesměrování neautorizovaných uživatelů na přihlašovací stránku.</span><span class="sxs-lookup"><span data-stu-id="642c3-104">The `RedirectToLogin` component manages redirecting unauthorized users to the login page.</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    <RedirectToLogin />
                </NotAuthorized>
            </AuthorizeRouteView>
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```
