---
title: ' ASP.NET Core Blazor ověřování a autorizace ' Autor: Popis: ' informace o Blazor scénářích ověřování a autorizace. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a>ASP.NET Core Blazor ověřování a autorizace

Pomocí [Steve Sanderson](https://github.com/SteveSandersonMS) a [Luke Latham](https://github.com/guardrex)

ASP.NET Core podporuje konfiguraci a správu zabezpečení v Blazor aplikacích.

Scénáře zabezpečení se liší mezi Blazor serverem a Blazor aplikacemi WebAssembly. Vzhledem Blazor k tomu, že serverové aplikace běží na serveru, kontroly autorizace můžou určit:

* Možnosti uživatelského rozhraní prezentované uživateli (například položky nabídky, které jsou k dispozici uživateli).
* Pravidla přístupu pro oblasti aplikace a součástí

BlazorAplikace WebAssembly běží na klientovi. Autorizace se používá *jenom* k určení možností uživatelského rozhraní, které se mají zobrazit. Vzhledem k tomu, že kontroly na straně klienta může uživatel upravit nebo obejít, Blazor nemůže aplikace typu WebAssembly vyhovět autorizačním pravidlům.

[ Razor Konvence autorizace stránek](xref:security/authorization/razor-pages-authorization) se nevztahují na směrovatelné Razor součásti. Pokud Razor je na [stránce vložena](xref:blazor/integrate-components#render-components-from-a-page-or-view)Nesměrovatelné komponenty, zásady autorizace stránky nepřímo ovlivňují Razor komponentu spolu se zbytkem obsahu stránky.

> [!NOTE]
> <xref:Microsoft.AspNetCore.Identity.SignInManager%601>a <xref:Microsoft.AspNetCore.Identity.UserManager%601> nejsou podporovány v Razor součástech.

## <a name="authentication"></a>Authentication

Blazorpoužívá k vytvoření identity uživatele existující mechanismy ověřování ASP.NET Core. Přesný mechanismus závisí na tom, jak Blazor je aplikace hostována, Blazor WebAssembly nebo Blazor Server.

### <a name="blazor-webassembly-authentication"></a>BlazorOvěřování pro WebAssembly

V Blazor aplikacích pro WebAssembly lze kontroly ověřování obejít, protože všechny kódy na straně klienta mohou být změněny uživateli. Totéž platí pro všechny technologie aplikací na straně klienta, včetně rozhraní JavaScript SPA nebo nativních aplikací pro libovolný operační systém.

Přidejte následující:

* Odkaz na balíček pro [Microsoft. AspNetCore. Components. Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) do souboru projektu aplikace.
* `Microsoft.AspNetCore.Components.Authorization`Obor názvů pro soubor *_Imports. Razor* aplikace

Pro zpracování ověřování se implementace předdefinované nebo vlastní <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> služby zabývá následujícími oddíly.

Další informace o vytváření aplikací a konfigurace najdete v tématu <xref:security/blazor/webassembly/index> .

### <a name="blazor-server-authentication"></a>BlazorOvěřování serveru

BlazorServerové aplikace fungují prostřednictvím připojení v reálném čase vytvořeného pomocí SignalR . [Ověřování v SignalR aplikacích založených na ověřování](xref:signalr/authn-and-authz) se zpracovává při navázání připojení. Ověřování může být založené na souboru cookie nebo nějakém jiném nosným tokenu.

Další informace o vytváření aplikací a konfigurace najdete v tématu <xref:security/blazor/server/index> .

## <a name="authenticationstateprovider-service"></a>Služba AuthenticationStateProvider

Integrovaná <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> služba získá data stavu ověřování z ASP.NET Core `HttpContext.User` . To je způsob, jakým se stav ověřování integruje se stávajícími mechanismy ověřování ASP.NET Core.

<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider>je základní službou, kterou <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> Komponenta a komponenta používá <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> k získání stavu ověřování.

Obvykle přímo nepoužíváte <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> . Použijte [komponentu AuthorizeView](#authorizeview-component) nebo [úlohu \< AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) přístupy popsané dále v tomto článku. Hlavní nevýhodou použití <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> přímo je, že součást není automaticky oznámena v případě, že dojde ke změně podkladových dat stavu ověřování.

<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider>Služba může poskytnout data aktuálního uživatele <xref:System.Security.Claims.ClaimsPrincipal> , jak je znázorněno v následujícím příkladu:

```razor
@page "/"
@using System.Security.Claims
@using Microsoft.AspNetCore.Components.Authorization
@inject AuthenticationStateProvider AuthenticationStateProvider

<h3>ClaimsPrincipal Data</h3>

<button @onclick="GetClaimsPrincipalData">Get ClaimsPrincipal Data</button>

<p>@_authMessage</p>

@if (_claims.Count() > 0)
{
    <ul>
        @foreach (var claim in _claims)
        {
            <li>@claim.Type &ndash; @claim.Value</li>
        }
    </ul>
}

<p>@_surnameMessage</p>

@code {
    private string _authMessage;
    private string _surnameMessage;
    private IEnumerable<Claim> _claims = Enumerable.Empty<Claim>();

    private async Task GetClaimsPrincipalData()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
            _claims = user.Claims;
            _surnameMessage = 
                $"Surname: {user.FindFirst(c => c.Type == ClaimTypes.Surname)?.Value}";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

Pokud `user.Identity.IsAuthenticated` je `true` a vzhledem k tomu, že je uživatel a <xref:System.Security.Claims.ClaimsPrincipal> , mohou být deklarace identity vyhodnoceny a ve vyhodnocování členů.

Další informace o vkládání závislostí (DI) a službách naleznete v tématu <xref:blazor/dependency-injection> a <xref:fundamentals/dependency-injection> .

## <a name="implement-a-custom-authenticationstateprovider"></a>Implementace vlastního AuthenticationStateProvider

Pokud aplikace vyžaduje vlastního poskytovatele, implementujte <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> a přepište `GetAuthenticationStateAsync` :

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Authorization;

public class CustomAuthStateProvider : AuthenticationStateProvider
{
    public override Task<AuthenticationState> GetAuthenticationStateAsync()
    {
        var identity = new ClaimsIdentity(new[]
        {
            new Claim(ClaimTypes.Name, "mrfibuli"),
        }, "Fake authentication type");

        var user = new ClaimsPrincipal(identity);

        return Task.FromResult(new AuthenticationState(user));
    }
}
```

V Blazor aplikaci WebAssembly `CustomAuthStateProvider` je služba zaregistrovaná v `Main` *program.cs*:

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

V Blazor serverové aplikaci `CustomAuthStateProvider` je služba zaregistrovaná v `Startup.ConfigureServices` :

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

Pomocí v `CustomAuthStateProvider` předchozím příkladu se všechny uživatele ověřují pomocí uživatelského jména `mrfibuli` .

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a>Zveřejnit stav ověřování jako kaskádový parametr

Pokud jsou v procedurální logice požadovány údaje o stavu ověřování, například při provádění akce aktivované uživatelem, Získejte údaje o stavu ověřování definováním kaskádového parametru typu `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` :

```razor
@page "/"

<button @onclick="LogUsername">Log username</button>

<p>@_authMessage</p>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private string _authMessage;

    private async Task LogUsername()
    {
        var authState = await authenticationStateTask;
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

V takovém případě `user.Identity.IsAuthenticated` `true` můžou být deklarace identity výčtové a ve vyhodnocených rolích.

Nastavte `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` kaskádový parametr pomocí <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> komponent a v `App` součásti (*App. Razor*):

```razor
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <CascadingAuthenticationState>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </CascadingAuthenticationState>
    </NotFound>
</Router>
```

V Blazor aplikaci WebAssembly přidejte služby pro možnosti a autorizaci do `Program.Main` :

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

V Blazor serverové aplikaci jsou již nainstalovány služby pro možnosti a autorizaci, takže není nutné provádět žádnou další akci.

## <a name="authorization"></a>Autorizace

Po ověření uživatele se uplatní *autorizační* pravidla, která řídí, co může uživatel dělat.

Přístup je obvykle udělen nebo odepřen na základě toho, zda:

* Uživatel je ověřený (přihlášený).
* Uživatel je v *roli*.
* Uživatel má *deklaraci identity*.
* Byla splněna *zásada* .

Každá z těchto konceptů je stejná jako v aplikaci ASP.NET Core MVC nebo Razor Pages. Další informace o ASP.NET Core zabezpečení najdete v článcích [ASP.NET Core Security a Identity ](xref:security/index).

## <a name="authorizeview-component"></a>Komponenta AuthorizeView

<xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>Komponenta selektivně zobrazuje uživatelské rozhraní v závislosti na tom, zda je uživatel autorizován pro jeho zobrazení. Tento přístup je užitečný, když potřebujete jenom *Zobrazit* data pro uživatele a nemusíte používat identitu uživatele v procedurální logice.

Komponenta zveřejňuje `context` proměnnou typu <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> , kterou můžete použít pro přístup k informacím o přihlášeném uživateli:

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

V případě, že se uživatel neověřuje, můžete také Dodejte jiný obsah, který se zobrazí:

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <NotAuthorized>
        <h1>Authentication Failure!</h1>
        <p>You're not signed in.</p>
    </NotAuthorized>
</AuthorizeView>
```

<xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>Komponentu lze použít v `NavMenu` komponentě (*Shared/NavMenu. Razor*) k zobrazení položky seznamu ( `<li>...</li>` ) pro [komponentu NavLink](xref:blazor/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), ale Upozorňujeme, že tento přístup odstraní pouze položku seznamu z vykresleného výstupu. Nebrání uživateli přejít na součást.

Obsah `<Authorized>` `<NotAuthorized>` značek a může obsahovat libovolné položky, jako jsou například jiné interaktivní komponenty.

Podmínky autorizace, jako jsou role nebo zásady, které řídí možnosti uživatelského rozhraní nebo přístup, jsou uvedené v části [autorizace](#authorization) .

Pokud nejsou zadané autorizační podmínky, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> použije se výchozí zásada a bude se považovat za:

* Ověření uživatelé (přihlášeni) jako autorizované.
* Neověřené (odhlášené) uživatelé jako neautorizované.

### <a name="role-based-and-policy-based-authorization"></a>Ověřování na základě rolí a na základě zásad

<xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>Komponenta podporuje autorizaci na základě *rolí* nebo *na základě zásad* .

Pro autorizaci založenou na rolích použijte <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> parametr:

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

Další informace naleznete v tématu <xref:security/authorization/roles>.

Pro autorizaci založenou na zásadách použijte <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> parametr:

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

Ověřování na základě deklarací identity je zvláštní případ ověřování na základě zásad. Můžete například definovat zásadu, která vyžaduje, aby uživatelé měli určitou deklaraci identity. Další informace naleznete v tématu <xref:security/authorization/policies>.

Tato rozhraní API se dají použít v Blazor aplikacích serveru nebo Blazor WebAssembly.

Pokud <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> není zadán ani ani, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> používá výchozí zásady.

### <a name="content-displayed-during-asynchronous-authentication"></a>Obsah zobrazený během asynchronního ověřování

Blazorumožňuje, aby byl stav ověřování určován *asynchronně*. Primární scénář pro tento přístup je v Blazor aplikacích pro WebAssembly, které vytvářejí požadavek na externí koncový bod pro ověřování.

V průběhu ověřování ve <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> výchozím nastavení nezobrazí žádný obsah. Chcete-li zobrazit obsah, když dojde k ověřování, použijte `<Authorizing>` element:

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <Authorizing>
        <h1>Authentication in progress</h1>
        <p>You can only see this content while authentication is in progress.</p>
    </Authorizing>
</AuthorizeView>
```

Tento přístup se obvykle nevztahuje na Blazor serverové aplikace. BlazorServerové aplikace znají stav ověřování, jakmile se stav naváže. <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeViewCore.Authorizing>obsah může být k dispozici v Blazor součásti serverové aplikace <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> , ale obsah se nikdy nezobrazí.

## <a name="authorize-attribute"></a>[Autorizační] – atribut

[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Atribut lze použít v Razor součástech:

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> Používat pouze [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) v `@page` součástech, které byly dosaženy prostřednictvím Blazor směrovače. Autorizace se provádí jenom jako aspekt směrování, a *ne* pro podřízené komponenty vygenerované v rámci stránky. Chcete-li autorizovat zobrazení určitých částí v rámci stránky, použijte <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> místo toho.

[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Atribut také podporuje ověřování na základě rolí nebo na základě zásad. Pro autorizaci založenou na rolích použijte <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> parametr:

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

Pro autorizaci založenou na zásadách použijte <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> parametr:

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

Pokud není <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> zadán ani ani <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> , [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) používá výchozí zásadu, která ve výchozím nastavení zachází:

* Ověření uživatelé (přihlášeni) jako autorizované.
* Neověřené (odhlášené) uživatelé jako neautorizované.

## <a name="customize-unauthorized-content-with-the-router-component"></a>Přizpůsobení neoprávněného obsahu pomocí součásti směrovače

<xref:Microsoft.AspNetCore.Components.Routing.Router>Komponenta společně s <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> komponentou umožňuje aplikaci zadat vlastní obsah, pokud:

* Obsah nebyl nalezen.
* Uživatel nezdařil [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) podmínku použitou pro komponentu. [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Atribut je popsán v části [ `[Authorize]` atributu](#authorize-attribute) .
* Probíhá asynchronní ověřování.

V šabloně výchozího Blazor serverového projektu `App` Komponenta (*App. Razor*) ukazuje, jak nastavit vlastní obsah:

```razor
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)">
            <NotAuthorized>
                <h1>Sorry</h1>
                <p>You're not authorized to reach this page.</p>
                <p>You may need to log in as a different user.</p>
            </NotAuthorized>
            <Authorizing>
                <h1>Authentication in progress</h1>
                <p>Only visible while authentication is in progress.</p>
            </Authorizing>
        </AuthorizeRouteView>
    </Found>
    <NotFound>
        <CascadingAuthenticationState>
            <LayoutView Layout="@typeof(MainLayout)">
                <h1>Sorry</h1>
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </CascadingAuthenticationState>
    </NotFound>
</Router>
```

Obsah `<NotFound>` `<NotAuthorized>` značek, a `<Authorizing>` může obsahovat libovolné položky, jako jsou například jiné interaktivní komponenty.

Pokud `<NotAuthorized>` není element zadán, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> používá následující záložní zprávu:

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a>Oznámení o změnách stavu ověřování

Pokud aplikace zjistí, že se změnila základní data stavu ověřování (například kvůli tomu, že se uživatel odhlásil nebo jiný uživatel změnil své role), [vlastní AuthenticationStateProvider](#implement-a-custom-authenticationstateprovider) může volitelně vyvolat metodu <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider.NotifyAuthenticationStateChanged%2A> pro <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> základní třídu. Tím se uživatele upozorní na data stavu ověřování (například <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> ) k opakovanému vykreslení pomocí nových dat.

## <a name="procedural-logic"></a>Procesní logika

Pokud je aplikace nutná k kontrole autorizačních pravidel v rámci procedurální logiky, použijte `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` k získání uživatele kaskádový parametr typu <xref:System.Security.Claims.ClaimsPrincipal> . `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>`lze kombinovat s jinými službami, například `IAuthorizationService` , k vyhodnocení zásad.

```razor
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<button @onclick="@DoSomething">Do something important</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task DoSomething()
    {
        var user = (await authenticationStateTask).User;

        if (user.Identity.IsAuthenticated)
        {
            // Perform an action only available to authenticated (signed-in) users.
        }

        if (user.IsInRole("admin"))
        {
            // Perform an action only available to users in the 'admin' role.
        }

        if ((await AuthorizationService.AuthorizeAsync(user, "content-editor"))
            .Succeeded)
        {
            // Perform an action only available to users satisfying the 
            // 'content-editor' policy.
        }
    }
}
```

> [!NOTE]
> Do Blazor komponenty aplikace WebAssembly přidejte <xref:Microsoft.AspNetCore.Authorization> <xref:Microsoft.AspNetCore.Components.Authorization> obory názvů a:
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> Tyto obory názvů je možné poskytnout globálně tak, že je přidáte do souboru *_Imports. Razor* aplikace.

## <a name="authorization-in-blazor-webassembly-apps"></a>Autorizace v Blazor aplikacích pro WebAssembly

V Blazor aplikacích pro WebAssembly lze kontroly autorizace obejít, protože všechny kódy na straně klienta mohou být změněny uživateli. Totéž platí pro všechny technologie aplikací na straně klienta, včetně rozhraní JavaScript SPA nebo nativních aplikací pro libovolný operační systém.

**Na serveru vždy provádějte kontroly autorizace v libovolném koncovém bodu rozhraní API, ke kterému přistupovala aplikace na straně klienta.**

Další informace najdete v článcích v části <xref:security/blazor/webassembly/index> .

## <a name="troubleshoot-errors"></a>Řešení chyb

Běžné chyby:

* **Autorizace vyžaduje kaskádový parametr typu Task \< AuthenticationState>. Zvažte použití CascadingAuthenticationState k zadání.**

* **`null`hodnota je přijata pro`authenticationStateTask`**

Je možné, že projekt nebyl vytvořen pomocí Blazor šablony serveru s povoleným ověřováním. Zabalte `<CascadingAuthenticationState>` kolem některé části stromu uživatelského rozhraní, například do `App` komponenty (*App. Razor*), následujícím způsobem:

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` Doplní kaskádový parametr, který zase získá z podkladové <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> služby di.

## <a name="additional-resources"></a>Další zdroje

* <xref:security/index>
* <xref:security/authentication/windowsauth>
* [Super Blazor : ověřování –](https://github.com/AdrienTorris/awesome-blazor#authentication) ukázkové odkazy komunity
