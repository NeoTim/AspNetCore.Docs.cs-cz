`LoginDisplay`Součást ( `Shared/LoginDisplay.razor` ) je vykreslena v `MainLayout` komponentě ( `Shared/MainLayout.razor` ) a spravuje následující chování:

* Pro ověřené uživatele:
  * Zobrazí aktuální uživatelské jméno.
  * Nabízí tlačítko pro odhlášení od aplikace.
* Pro anonymní uživatele nabízí možnost přihlásit se.

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginLogout">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginLogout(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```
