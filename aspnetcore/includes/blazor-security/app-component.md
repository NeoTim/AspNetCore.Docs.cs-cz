`App`Součást (*App. Razor*) je podobná `App` komponentě, kterou najdete v Blazor serverových aplikacích:

* Komponenta spravuje vystavení <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> do zbytku aplikace.
* <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView>Komponenta zajistí, že aktuální uživatel má oprávnění pro přístup k dané stránce nebo jinak vykreslí `RedirectToLogin` součást.
* `RedirectToLogin`Komponenta spravuje přesměrování neautorizovaných uživatelů na přihlašovací stránku.

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
