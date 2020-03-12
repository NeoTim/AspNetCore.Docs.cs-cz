Komponenta `App` (*App. Razor*) se podobá komponentě `App` v aplikacích serveru Blazor:

* Komponenta `CascadingAuthenticationState` spravuje vystavení `AuthenticationState` do zbytku aplikace.
* Komponenta `AuthorizeRouteView` zajistí, že aktuální uživatel má oprávnění pro přístup k dané stránce, nebo jinak vykreslí `RedirectToLogin` komponentu.
* Komponenta `RedirectToLogin` spravuje přesměrování neautorizovaných uživatelů na přihlašovací stránku.

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
