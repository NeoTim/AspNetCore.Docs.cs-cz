<span data-ttu-id="d07e9-101">`App`Součást (*App. Razor*) je podobná `App` komponentě, kterou najdete v Blazor serverových aplikacích:</span><span class="sxs-lookup"><span data-stu-id="d07e9-101">The `App` component (*App.razor*) is similar to the `App` component found in Blazor Server apps:</span></span>

* <span data-ttu-id="d07e9-102">Komponenta spravuje vystavení <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> do zbytku aplikace.</span><span class="sxs-lookup"><span data-stu-id="d07e9-102">The <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> component manages exposing the <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> to the rest of the app.</span></span>
* <span data-ttu-id="d07e9-103"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView>Komponenta zajistí, že aktuální uživatel má oprávnění pro přístup k dané stránce nebo jinak vykreslí `RedirectToLogin` součást.</span><span class="sxs-lookup"><span data-stu-id="d07e9-103">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> component makes sure that the current user is authorized to access a given page or otherwise renders the `RedirectToLogin` component.</span></span>
* <span data-ttu-id="d07e9-104">`RedirectToLogin`Komponenta spravuje přesměrování neautorizovaných uživatelů na přihlašovací stránku.</span><span class="sxs-lookup"><span data-stu-id="d07e9-104">The `RedirectToLogin` component manages redirecting unauthorized users to the login page.</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    @if (!context.User.Identity.IsAuthenticated)
                    {
                        <RedirectToLogin />
                    }
                    else
                    {
                        <p>
                            You are not authorized to access 
                            this resource.
                        </p>
                    }
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
