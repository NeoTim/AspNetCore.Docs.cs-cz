---
title: ASP.NET Core Blazor ověřování a autorizace
author: guardrex
description: Přečtěte Blazor si o scénářích ověřování a autorizace.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/index
ms.openlocfilehash: d55880265ed1ceedf8f115412e5ac47309521239
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772892"
---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a>ASP.NET Core Blazor ověřování a autorizace

Pomocí [Steve Sanderson](https://github.com/SteveSandersonMS) a [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

> [!NOTE]
> Pro pokyny v tomto článku, které platí pro Blazor WebAssembly, je vyžadována Blazor šablona ASP.NET Core WebAssembly verze 3,2 nebo novější. Pokud nepoužíváte Visual Studio verze 16,6 Preview 2 nebo novější, Získejte nejnovější Blazor šablonu WebAssembly podle pokynů v <xref:blazor/get-started>části.

ASP.NET Core podporuje konfiguraci a správu zabezpečení v Blazor aplikacích.

Scénáře zabezpečení se liší Blazor mezi serverem Blazor a aplikacemi WebAssembly. Vzhledem Blazor k tomu, že serverové aplikace běží na serveru, kontroly autorizace můžou určit:

* Možnosti uživatelského rozhraní prezentované uživateli (například položky nabídky, které jsou k dispozici uživateli).
* Pravidla přístupu pro oblasti aplikace a součástí

BlazorAplikace WebAssembly běží na klientovi. Autorizace se používá *jenom* k určení možností uživatelského rozhraní, které se mají zobrazit. Vzhledem k tomu, že kontroly na straně klienta může uživatel upravit nebo obejít, Blazor nemůže aplikace typu WebAssembly vyhovět autorizačním pravidlům.

Konvence autorizace stránek se nevztahují na směrovatelné Razor součásti. [ Razor ](xref:security/authorization/razor-pages-authorization) Pokud je na Razor [stránce vložena](xref:blazor/integrate-components#render-components-from-a-page-or-view)Nesměrovatelné komponenty, zásady autorizace stránky nepřímo ovlivňují Razor komponentu spolu se zbytkem obsahu stránky.

> [!NOTE]
> <xref:Microsoft.AspNetCore.Identity.SignInManager%601>a <xref:Microsoft.AspNetCore.Identity.UserManager%601> nejsou podporovány v Razor součástech.

## <a name="authentication"></a>Authentication

Blazorpoužívá k vytvoření identity uživatele existující mechanismy ověřování ASP.NET Core. Přesný mechanismus závisí na tom, jak Blazor je aplikace hostována Blazor , WebAssembly Blazor nebo server.

### <a name="blazor-webassembly-authentication"></a>BlazorOvěřování pro WebAssembly

V Blazor aplikacích pro WebAssembly lze kontroly ověřování obejít, protože všechny kódy na straně klienta mohou být změněny uživateli. Totéž platí pro všechny technologie aplikací na straně klienta, včetně rozhraní JavaScript SPA nebo nativních aplikací pro libovolný operační systém.

Přidejte následující:

* Odkaz na balíček pro [Microsoft. AspNetCore. Components. Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) do souboru projektu aplikace.
* `Microsoft.AspNetCore.Components.Authorization` Obor názvů pro soubor *_Imports. Razor* aplikace

Pro zpracování ověřování se implementace předdefinované nebo vlastní `AuthenticationStateProvider` služby zabývá následujícími oddíly.

Další informace o vytváření aplikací a konfigurace najdete v tématu <xref:security/blazor/webassembly/index>.

### <a name="blazor-server-authentication"></a>BlazorOvěřování serveru

BlazorServerové aplikace fungují prostřednictvím připojení v reálném čase vytvořeného pomocí SignalR. [Ověřování v SignalRaplikacích založených na ověřování](xref:signalr/authn-and-authz) se zpracovává při navázání připojení. Ověřování může být založené na souboru cookie nebo nějakém jiném nosným tokenu.

Další informace o vytváření aplikací a konfigurace najdete v tématu <xref:security/blazor/server/index>.

## <a name="authenticationstateprovider-service"></a>Služba AuthenticationStateProvider

Integrovaná `AuthenticationStateProvider` služba získá data stavu ověřování z ASP.NET Core `HttpContext.User`. To je způsob, jakým se stav ověřování integruje se stávajícími mechanismy ověřování ASP.NET Core.

`AuthenticationStateProvider`je základní službou, kterou `AuthorizeView` komponenta a `CascadingAuthenticationState` komponenta používá k získání stavu ověřování.

Obvykle přímo nepoužíváte `AuthenticationStateProvider` . Použijte [komponentu AuthorizeView](#authorizeview-component) nebo [\<úlohu AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) přístupy popsané dále v tomto článku. Hlavní nevýhodou použití `AuthenticationStateProvider` přímo je, že součást není automaticky oznámena v případě, že dojde ke změně podkladových dat stavu ověřování.

`AuthenticationStateProvider` Služba může poskytnout <xref:System.Security.Claims.ClaimsPrincipal> data aktuálního uživatele, jak je znázorněno v následujícím příkladu:

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

Pokud `user.Identity.IsAuthenticated` je `true` a vzhledem k tomu <xref:System.Security.Claims.ClaimsPrincipal>, že je uživatel a, mohou být deklarace identity vyhodnoceny a ve vyhodnocování členů.

Další informace o vkládání závislostí (DI) a službách naleznete v <xref:blazor/dependency-injection> tématu <xref:fundamentals/dependency-injection>a.

## <a name="implement-a-custom-authenticationstateprovider"></a>Implementace vlastního AuthenticationStateProvider

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

Blazor V aplikaci WebAssembly je `CustomAuthStateProvider` služba zaregistrovaná v `Main` *program.cs*:

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

V Blazor serverové aplikaci je `CustomAuthStateProvider` služba zaregistrovaná v: `Startup.ConfigureServices`

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

`CustomAuthStateProvider` Pomocí v předchozím příkladu se všechny uživatele ověřují pomocí uživatelského jména `mrfibuli`.

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a>Zveřejnit stav ověřování jako kaskádový parametr

Pokud jsou v procedurální logice požadovány údaje o stavu ověřování, například při provádění akce aktivované uživatelem, Získejte údaje o stavu ověřování definováním kaskádového parametru typu `Task<AuthenticationState>`:

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

`true`V takovém případě `user.Identity.IsAuthenticated` můžou být deklarace identity výčtové a ve vyhodnocených rolích.

`Task<AuthenticationState>` Nastavte kaskádový parametr pomocí komponent `AuthorizeRouteView` a `CascadingAuthenticationState` v `App` součásti (*App. Razor*):

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

Blazor V aplikaci WebAssembly přidejte služby pro možnosti a autorizaci do `Program.Main`:

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

`AuthorizeView` Komponenta selektivně zobrazuje uživatelské rozhraní v závislosti na tom, zda je uživatel autorizován pro jeho zobrazení. Tento přístup je užitečný, když potřebujete jenom *Zobrazit* data pro uživatele a nemusíte používat identitu uživatele v procedurální logice.

Komponenta zveřejňuje `context` proměnnou typu `AuthenticationState`, kterou můžete použít pro přístup k informacím o přihlášeném uživateli:

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

`AuthorizeView` Komponentu `NavMenu` lze použít v komponentě (*Shared/NavMenu. Razor*) k zobrazení položky seznamu (`<li>...</li>`) pro `NavLink`, ale tento přístup odstraní pouze položku seznamu z vykresleného výstupu. Nebrání uživateli přejít na součást.

Obsah značek `<Authorized>` a `<NotAuthorized>` může obsahovat libovolné položky, jako jsou například jiné interaktivní komponenty.

Podmínky autorizace, jako jsou role nebo zásady, které řídí možnosti uživatelského rozhraní nebo přístup, jsou uvedené v části [autorizace](#authorization) .

Pokud nejsou zadané autorizační podmínky, `AuthorizeView` použije se výchozí zásada a bude se považovat za:

* Ověření uživatelé (přihlášeni) jako autorizované.
* Neověřené (odhlášené) uživatelé jako neautorizované.

### <a name="role-based-and-policy-based-authorization"></a>Ověřování na základě rolí a na základě zásad

`AuthorizeView` Komponenta podporuje autorizaci na základě *rolí* nebo *na základě zásad* .

Pro autorizaci založenou na rolích `Roles` použijte parametr:

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

Další informace naleznete v tématu <xref:security/authorization/roles>.

Pro autorizaci založenou na zásadách použijte `Policy` parametr:

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

Ověřování na základě deklarací identity je zvláštní případ ověřování na základě zásad. Můžete například definovat zásadu, která vyžaduje, aby uživatelé měli určitou deklaraci identity. Další informace naleznete v tématu <xref:security/authorization/policies>.

Tato rozhraní API se dají použít v Blazor aplikacích serveru Blazor nebo WebAssembly.

Pokud není `Roles` zadán `Policy` ani ani, `AuthorizeView` používá výchozí zásady.

### <a name="content-displayed-during-asynchronous-authentication"></a>Obsah zobrazený během asynchronního ověřování

Blazorumožňuje, aby byl stav ověřování určován *asynchronně*. Primární scénář pro tento přístup je v Blazor aplikacích pro WebAssembly, které vytvářejí požadavek na externí koncový bod pro ověřování.

V průběhu ověřování ve výchozím nastavení `AuthorizeView` nezobrazí žádný obsah. Chcete-li zobrazit obsah, když dojde k `<Authorizing>` ověřování, použijte element:

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

Tento přístup se obvykle nevztahuje na Blazor serverové aplikace. BlazorServerové aplikace znají stav ověřování, jakmile se stav naváže. `Authorizing`obsah může být k dispozici Blazor v `AuthorizeView` součásti serverové aplikace, ale obsah se nikdy nezobrazí.

## <a name="authorize-attribute"></a>[Autorizační] – atribut

`[Authorize]` Atribut lze použít v Razor součástech:

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> Používat `[Authorize]` pouze v `@page` součástech, které Blazor byly dosaženy prostřednictvím směrovače. Autorizace se provádí jenom jako aspekt směrování, a *ne* pro podřízené komponenty vygenerované v rámci stránky. Chcete-li autorizovat zobrazení určitých částí v rámci stránky, použijte `AuthorizeView` místo toho.

`[Authorize]` Atribut také podporuje ověřování na základě rolí nebo na základě zásad. Pro autorizaci založenou na rolích `Roles` použijte parametr:

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

Pro autorizaci založenou na zásadách použijte `Policy` parametr:

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

Pokud není `Roles` zadán `Policy` ani ani, `[Authorize]` používá výchozí zásadu, která ve výchozím nastavení zachází:

* Ověření uživatelé (přihlášeni) jako autorizované.
* Neověřené (odhlášené) uživatelé jako neautorizované.

## <a name="customize-unauthorized-content-with-the-router-component"></a>Přizpůsobení neoprávněného obsahu pomocí součásti směrovače

`Router` Komponenta společně s `AuthorizeRouteView` komponentou umožňuje aplikaci zadat vlastní obsah, pokud:

* Obsah nebyl nalezen.
* Uživatel nezdařil `[Authorize]` podmínku použitou pro komponentu. `[Authorize]` Atribut je popsán v části [ `[Authorize]` atributu](#authorize-attribute) .
* Probíhá asynchronní ověřování.

V šabloně výchozího Blazor serverového projektu `App` komponenta (*App. Razor*) ukazuje, jak nastavit vlastní obsah:

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

Obsah značek `<NotFound>`, `<NotAuthorized>`a `<Authorizing>` může obsahovat libovolné položky, jako jsou například jiné interaktivní komponenty.

Pokud není `<NotAuthorized>` element zadán, `AuthorizeRouteView` používá následující záložní zprávu:

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a>Oznámení o změnách stavu ověřování

Pokud aplikace zjistí, že se změnila základní data stavu ověřování (například kvůli tomu, že se uživatel odhlásil nebo jiný uživatel změnil své role), [vlastní AuthenticationStateProvider](#implement-a-custom-authenticationstateprovider) může volitelně vyvolat metodu `NotifyAuthenticationStateChanged` pro `AuthenticationStateProvider` základní třídu. Tím se uživatele upozorní na data stavu ověřování (například `AuthorizeView`) k opakovanému vykreslení pomocí nových dat.

## <a name="procedural-logic"></a>Procesní logika

Pokud je aplikace nutná k kontrole autorizačních pravidel v rámci procedurální logiky, použijte k získání uživatele kaskádový parametr `Task<AuthenticationState>` typu <xref:System.Security.Claims.ClaimsPrincipal>. `Task<AuthenticationState>`lze kombinovat s jinými službami, například `IAuthorizationService`, k vyhodnocení zásad.

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
> Blazor Do komponenty aplikace WebAssembly přidejte obory názvů `Microsoft.AspNetCore.Authorization` a `Microsoft.AspNetCore.Components.Authorization` :
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

Další informace najdete v článcích v části <xref:security/blazor/webassembly/index>.

## <a name="troubleshoot-errors"></a>Řešení chyb

Běžné chyby:

* **Autorizace vyžaduje kaskádový parametr typu Task\<AuthenticationState>. Zvažte použití CascadingAuthenticationState k zadání.**

* **`null`hodnota je přijata pro`authenticationStateTask`**

Je možné, že projekt nebyl vytvořen pomocí šablony Blazor serveru s povoleným ověřováním. Zabalte `<CascadingAuthenticationState>` kolem některé části stromu uživatelského rozhraní, například do `App` komponenty (*App. Razor*), následujícím způsobem:

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

`CascadingAuthenticationState` Doplní `Task<AuthenticationState>` kaskádový parametr, který zase získá z podkladové `AuthenticationStateProvider` služby di.

## <a name="additional-resources"></a>Další zdroje

* <xref:security/index>
* <xref:security/authentication/windowsauth>
* [Super Blazor: ověřování –](https://github.com/AdrienTorris/awesome-blazor#authentication) ukázkové odkazy komunity
