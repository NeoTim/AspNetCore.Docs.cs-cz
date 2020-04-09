---
title: ASP.NET Blazor základní ověřování a autorizace
author: guardrex
description: Informace Blazor o scénářích ověřování a autorizace.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/index
ms.openlocfilehash: 04bbf20d1d848edfa98e719f316b790c812bfd95
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80501312"
---
# <a name="aspnet-core-opno-locblazor-authentication-and-authorization"></a>ASP.NET Blazor základní ověřování a autorizace

[Steve Sanderson](https://github.com/SteveSandersonMS) a [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

> [!NOTE]
> Pokyny v tomto článku, které Blazor se vztahují k Blazor webové sestavě, je vyžadována šablona ASP.NET Core WebAssembly verze 3.2 nebo novější. Pokud nepoužíváte Visual Studio verze 16.6 Preview 2 nebo Blazor novější, získejte nejnovější <xref:blazor/get-started>šablonu WebAssembly podle pokynů v aplikaci .

ASP.NET Core podporuje konfiguraci a Blazor správu zabezpečení aplikací.

Scénáře zabezpečení Blazor se Blazor liší mezi aplikacemi Server a WebAssembly. Vzhledem k tomu, Blazor že serverové aplikace běží na serveru, mohou kontroly autorizací určit:

* Možnosti uživatelského rozhraní prezentované uživateli (například položky nabídky, které jsou uživateli k dispozici).
* Přístup k pravidlům pro oblasti aplikace a komponent.

BlazorAplikace WebAssembly běží na straně klienta. Autorizace se používá *pouze* k určení, které možnosti ui chcete zobrazit. Vzhledem k tomu, že kontroly na straně Blazor klienta může uživatel upravit nebo obejít, aplikace WebAssembly nemůže vynutit pravidla přístupu k autorizaci.

[Konvence autorizace Razor Pages](xref:security/authorization/razor-pages-authorization) se nevztahují na směrovatelné komponenty Razor. Pokud je na [stránce vložena](xref:blazor/integrate-components#render-components-from-a-page-or-view)nesměrovatelná komponenta Razor , konvence autorizace stránky nepřímo ovlivňují komponentu Razor spolu se zbytkem obsahu stránky.

> [!NOTE]
> <xref:Microsoft.AspNetCore.Identity.SignInManager%601>a <xref:Microsoft.AspNetCore.Identity.UserManager%601> nejsou podporovány v komponentách Razor.

## <a name="authentication"></a>Authentication

Blazorpoužívá existující mechanismy ověřování ASP.NET Core k vytvoření identity uživatele. Přesný mechanismus závisí Blazor na tom, Blazor jak je Blazor aplikace hostována, webassembly nebo server.

### <a name="opno-locblazor-webassembly-authentication"></a>BlazorOvěřování webové sestavy

V Blazor aplikacích WebAssembly lze kontroly ověřování obejít, protože všechny kódy na straně klienta mohou uživatelé upravovat. Totéž platí pro všechny technologie aplikací na straně klienta, včetně javascriptových spa architektur nebo nativních aplikací pro jakýkoli operační systém.

Přidejte následující:

* Odkaz na balíček pro [Microsoft.AspNetCore.Components.Autorizace](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) souboru projektu aplikace.
* Obor `Microsoft.AspNetCore.Components.Authorization` názvů do souboru *_Imports.razor* aplikace.

Pro zpracování ověřování je implementace předdefinované `AuthenticationStateProvider` nebo vlastní služby popsána v následujících částech.

Další informace o vytváření aplikací <xref:security/blazor/webassembly/index>a konfigurace naleznete v tématu .

### <a name="opno-locblazor-server-authentication"></a>BlazorOvěřování serveru

BlazorServerové aplikace pracují přes připojení v reálném SignalRčase, které je vytvořeno pomocí aplikace . [Ověřování SignalRv aplikacích založených na](xref:signalr/authn-and-authz) zabezpečení se zpracovává při navázání připojení. Ověřování může být založeno na souboru cookie nebo jiném nožním tokenu.

Další informace o vytváření aplikací <xref:security/blazor/server>a konfigurace naleznete v tématu .

## <a name="authenticationstateprovider-service"></a>Služba AuthenticationStateProvider

Vestavěná `AuthenticationStateProvider` služba získává data o stavu ověřování `HttpContext.User`z ASP.NET Core . Takto se integruje stav ověřování s existujícími mechanismy ověřování ASP.NET Core.

`AuthenticationStateProvider`je základní služba používaná `CascadingAuthenticationState` komponentou a komponentou `AuthorizeView` k získání stavu ověřování.

Obvykle se nepoužíváte `AuthenticationStateProvider` přímo. Použijte [přístupy authorizeview nebo](#authorizeview-component) [úlohy\<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) popsané dále v tomto článku. Hlavní nevýhodou použití `AuthenticationStateProvider` přímo je, že komponenta není automaticky upozorněna, pokud se změní základní data stavu ověřování.

Služba `AuthenticationStateProvider` může poskytnout <xref:System.Security.Claims.ClaimsPrincipal> data aktuálního uživatele, jak je znázorněno v následujícím příkladu:

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

Pokud `user.Identity.IsAuthenticated` `true` je a protože <xref:System.Security.Claims.ClaimsPrincipal>uživatel je , deklarace mohou být výčtu a členství v rolích vyhodnoceny.

Další informace o vkládání závislostí (DI) <xref:blazor/dependency-injection> <xref:fundamentals/dependency-injection>a služby naleznete v tématu a .

## <a name="implement-a-custom-authenticationstateprovider"></a>Implementace vlastního zprostředkovatele AuthenticationStateProvider

Pokud aplikace vyžaduje vlastního poskytovatele, implementujte `AuthenticationStateProvider` a přepište `GetAuthenticationStateAsync`:

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

V Blazor aplikaci WebAssembly `CustomAuthStateProvider` je služba `Main` registrována v *Program.cs*:

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

V Blazor aplikaci Server `CustomAuthStateProvider` je služba `Startup.ConfigureServices`registrována v :

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

Pomocí `CustomAuthStateProvider` v předchozím příkladu jsou všichni uživatelé ověřeni pomocí uživatelského jména `mrfibuli`.

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a>Vystavit stav ověřování jako kaskádový parametr

Pokud jsou pro procedurální logiku vyžadována data o stavu ověřování, například při provádění akce aktivované uživatelem, získají data o stavu ověřování definováním kaskádového parametru typu `Task<AuthenticationState>`:

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

Pokud `user.Identity.IsAuthenticated` `true`je , deklarace mohou být výčty a členství v rolích vyhodnoceny.

Nastavte `Task<AuthenticationState>` kaskádový parametr pomocí `AuthorizeRouteView` komponent `CascadingAuthenticationState` a v `App` komponentě *(App.razor*):

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

V Blazor aplikaci WebAssembly přidejte služby `Program.Main`pro možnosti a autorizaci do aplikace :

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

V Blazor aplikaci Server jsou již k dispozici služby pro možnosti a autorizaci, takže není nutná žádná další akce.

## <a name="authorization"></a>Autorizace

Po ověření uživatele jsou použita *autorizační* pravidla k řízení toho, co může uživatel dělat.

Přístup je obvykle udělen nebo odepřen na základě toho, zda:

* Uživatel je ověřen (přihlášen).
* Uživatel je v *roli*.
* Uživatel má *nárok*.
* *Politika* je splněna.

Každý z těchto konceptů je stejný jako v aplikaci ASP.NET Core MVC nebo Razor Pages. Další informace o ASP.NET základní zabezpečení naleznete v článcích [v části ASP.NET základní zabezpečení a identity](xref:security/index).

## <a name="authorizeview-component"></a>Součást AuthorizeView

Komponenta `AuthorizeView` selektivně zobrazí uživatelské rozhraní v závislosti na tom, zda je uživatel oprávněn jej zobrazit. Tento přístup je užitečný pouze v případě, že potřebujete *zobrazit* data pro uživatele a není nutné používat identitu uživatele v procedurální logice.

Komponenta zpřístupňuje proměnnou `context` typu `AuthenticationState`, kterou můžete použít k přístupu k informacím o přihlášeném uživateli:

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

Pokud uživatel není ověřen, můžete také zadat jiný obsah pro zobrazení:

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

Komponentu `AuthorizeView` lze použít `NavMenu` v komponentě (*Shared/NavMenu.razor*)`<li>...</li>`k `NavLink`zobrazení položky seznamu ( ) pro , ale všimněte si, že tento přístup odebere pouze položku seznamu z vykresleného výstupu. Nezabrání uživateli v navigaci na komponentu.

Obsah a `<Authorized>` `<NotAuthorized>` značky mohou obsahovat libovolné položky, například jiné interaktivní součásti.

Podmínky autorizace, jako jsou role nebo zásady, které řídí možnosti rozhraní nebo přístup, jsou zahrnuty v části [Autorizace.](#authorization)

Pokud nejsou zadány podmínky `AuthorizeView` autorizace, použije výchozí zásady a zachází:

* Ověření (přihlášení) uživatelé jako autorizovaní.
* Neověřené (odhlášení) uživatelé jako neoprávnění.

### <a name="role-based-and-policy-based-authorization"></a>Autorizace založená na rolích a zásadách

Komponenta `AuthorizeView` podporuje *autorizaci založenou na rolích* nebo *zásadách.*

Pro autorizaci založenou `Roles` na rolích použijte parametr:

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

Další informace naleznete v tématu <xref:security/authorization/roles>.

Pro autorizaci založenou `Policy` na zásadách použijte parametr:

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

Autorizace založená na deklaracích je zvláštní případ autorizace založené na zásadách. Můžete například definovat zásadu, která vyžaduje, aby uživatelé měli určitou deklaraci. Další informace naleznete v tématu <xref:security/authorization/policies>.

Tato api lze použít Blazor v Blazor aplikacích Server nebo WebAssembly.

Pokud ani `Roles` `Policy` není zadán, `AuthorizeView` použije výchozí zásady.

### <a name="content-displayed-during-asynchronous-authentication"></a>Obsah zobrazený během asynchronního ověřování

Blazorumožňuje *asynchronně*určit stav ověřování . Primární scénář pro tento Blazor přístup je v aplikacích WebAssembly, které podají požadavek na externí koncový bod pro ověřování.

Během ověřování se `AuthorizeView` ve výchozím nastavení nezobrazuje žádný obsah. Chcete-li zobrazit obsah při `<Authorizing>` ověřování, použijte prvek:

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

Tento přístup se obvykle Blazor nevztahuje na serverové aplikace. BlazorServerové aplikace znají stav ověřování, jakmile je stav vytvořen. `Authorizing`obsah může být poskytnut Blazor v `AuthorizeView` součásti aplikace Server, ale obsah se nikdy nezobrazí.

## <a name="authorize-attribute"></a>[Autorizovat] atribut

Atribut `[Authorize]` lze použít v komponentách Razor:

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> Používejte `[Authorize]` pouze `@page` u součástí Blazor dostupných přes router. Autorizace se provádí pouze jako aspekt směrování a *nikoli* pro podřízené součásti vykreslené na stránce. Chcete-li autorizovat zobrazení určitých `AuthorizeView` částí na stránce, použijte místo toho.

Atribut `[Authorize]` také podporuje autorizaci založenou na rolích nebo zásadách. Pro autorizaci založenou `Roles` na rolích použijte parametr:

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

Pro autorizaci založenou `Policy` na zásadách použijte parametr:

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

Pokud ani `Roles` `Policy` není zadán, `[Authorize]` používá výchozí zásady, které ve výchozím nastavení je k léčbě:

* Ověření (přihlášení) uživatelé jako autorizovaní.
* Neověřené (odhlášení) uživatelé jako neoprávnění.

## <a name="customize-unauthorized-content-with-the-router-component"></a>Přizpůsobení neoprávněného obsahu pomocí komponenty Router

Komponenta `Router` ve spojení `AuthorizeRouteView` s komponentou umožňuje aplikaci určit vlastní obsah, pokud:

* Obsah nebyl nalezen.
* Uživatel selže `[Authorize]` podmínku použitou na komponentu. Atribut `[Authorize]` je popsán v části [ `[Authorize]` atributu.](#authorize-attribute)
* Probíhá asynchronní ověřování.

Ve výchozí Blazor šabloně projektu `App` Server komponenta *(App.razor)* ukazuje, jak nastavit vlastní obsah:

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

Obsah , `<NotFound>` `<NotAuthorized>`a `<Authorizing>` značky mohou obsahovat libovolné položky, například jiné interaktivní součásti.

Pokud `<NotAuthorized>` prvek není zadán, `AuthorizeRouteView` použije následující záložní zprávu:

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a>Oznámení o změnách stavu ověřování

Pokud aplikace zjistí, že se změnila základní data stavu ověřování (například proto, že se uživatel odhlásil nebo jiný `NotifyAuthenticationStateChanged` uživatel `AuthenticationStateProvider` změnil své role), může [vlastní AuthenticationStateProvider](#implement-a-custom-authenticationstateprovider) volitelně vyvolat metodu v základní třídě. To upozorní spotřebitele data stavu ověřování (například `AuthorizeView`) k rerender pomocí nových dat.

## <a name="procedural-logic"></a>Procedurální logika

Pokud je aplikace povinna zkontrolovat autorizační pravidla jako součást procedurální `Task<AuthenticationState>` logiky, použijte <xref:System.Security.Claims.ClaimsPrincipal>kaskádový parametr typu k získání uživatele . `Task<AuthenticationState>`lze kombinovat s dalšími službami, například `IAuthorizationService`k vyhodnocení zásad.

```razor
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
> V Blazor součásti aplikace WebAssembly `Microsoft.AspNetCore.Authorization` `Microsoft.AspNetCore.Components.Authorization` přidejte obory názvů a:
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> Tyto obory názvů lze poskytnout globálně jejich přidáním do souboru *_Imports.razor* aplikace.

## <a name="authorization-in-opno-locblazor-webassembly-apps"></a>Autorizace Blazor v aplikacích WebAssembly

V Blazor aplikacích WebAssembly lze kontroly autorizace obejít, protože všechny kódy na straně klienta mohou uživatelé upravovat. Totéž platí pro všechny technologie aplikací na straně klienta, včetně javascriptových spa architektur nebo nativních aplikací pro jakýkoli operační systém.

**Vždy provádějte kontroly autorizace na serveru v rámci všech koncových bodů rozhraní API, ke kterým přistupuje aplikace na straně klienta.**

Další informace naleznete v <xref:security/blazor/webassembly/index>článcích v části .

## <a name="troubleshoot-errors"></a>Řešení chyb

Běžné chyby:

* **Autorizace vyžaduje kaskádový parametr\<typu Úloha AuthenticationState>. Zvažte použití CascadingAuthenticationState k zadání tohoto.**

* **`null`hodnota je přijata pro`authenticationStateTask`**

Je pravděpodobné, že projekt nebyl vytvořen Blazor pomocí šablony serveru s povoleným ověřováním. Zabalte `<CascadingAuthenticationState>` přibližně některé části stromu ui, `App` například v komponentě (*App.razor*) takto:

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

Dodává `CascadingAuthenticationState` `Task<AuthenticationState>` kaskádový parametr, který zase obdrží z podkladové `AuthenticationStateProvider` služby DI.

## <a name="additional-resources"></a>Další zdroje

* <xref:security/index>
* <xref:security/blazor/server>
* <xref:security/authentication/windowsauth>
* [Awesome: BlazorOvěřování](https://github.com/AdrienTorris/awesome-blazor#authentication) společenství ukázkové odkazy
