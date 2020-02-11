---
title: Ověřování a autorizace ASP.NET Core Blazor
author: guardrex
description: Přečtěte si o Blazor scénářích ověřování a autorizace.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/02/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/index
ms.openlocfilehash: c7b3788b5737073100e7fa449fd6bb4a83c0043a
ms.sourcegitcommit: 235623b6e5a5d1841139c82a11ac2b4b3f31a7a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114884"
---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a>ASP.NET Core ověřování a autorizace Blazor

Pomocí [Steve Sanderson](https://github.com/SteveSandersonMS)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

ASP.NET Core podporuje konfiguraci a správu zabezpečení v aplikacích Blazor.

Scénáře zabezpečení se liší mezi Blazor serverem a Blazor aplikacemi WebAssembly. Vzhledem k tomu, že aplikace Blazor serveru běží na serveru, kontroly autorizace můžou určit:

* Možnosti uživatelského rozhraní prezentované uživateli (například položky nabídky, které jsou k dispozici uživateli).
* Pravidla přístupu pro oblasti aplikace a součástí

Blazor aplikace WebAssembly běží na klientovi. Autorizace se používá *jenom* k určení možností uživatelského rozhraní, které se mají zobrazit. Vzhledem k tomu, že kontroly na straně klienta mohou být upraveny nebo vynechány uživatelem, aplikace Blazor WebAssembly nemůže vyhovět autorizačním pravidlům.

## <a name="authentication"></a>Ověřování

Blazor používá k vytvoření identity uživatele existující mechanismy ověřování ASP.NET Core. Přesný mechanismus závisí na tom, jak je aplikace Blazor hostovaná, Blazor Server nebo Blazor WebAssembly.

### <a name="blazor-server-authentication"></a>Ověřování serveru Blazor

Blazor serverové aplikace pracují přes připojení v reálném čase, které je vytvořené pomocí signálu. [Ověřování v aplikacích založených na službě Signal](xref:signalr/authn-and-authz) se zpracovává při navázání připojení. Ověřování může být založené na souboru cookie nebo nějakém jiném nosným tokenu.

Šablona projektu serveru Blazor může pro vás nastavit ověřování při vytvoření projektu.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Pokud chcete vytvořit nový projekt serveru Blazor s mechanismem ověřování, postupujte podle pokynů pro Visual Studio v tématu <xref:blazor/get-started>.

Po výběru šablony **aplikace Blazor serveru** v dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** vyberte v části **ověřování**možnost **změnit** .

Otevře se dialogové okno s nabídkou stejné sady mechanismů ověřování pro jiné projekty ASP.NET Core:

* **Bez ověřování**
* **Jednotlivé uživatelské účty** &ndash; uživatelských účtů mohou být uloženy:
  * V aplikaci, která používá systém [identit](xref:security/authentication/identity) ASP.NET Core.
  * S [Azure AD B2C](xref:security/authentication/azure-ad-b2c).
* **Pracovní nebo školní účty**
* **Ověřování systému Windows**

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Postupujte podle pokynů Visual Studio Code v článku <xref:blazor/get-started> a vytvořte nový projekt serveru Blazor s mechanismem ověřování:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

V následující tabulce jsou uvedeny přípustné hodnoty ověřování (`{AUTHENTICATION}`).

| Mechanismus ověřování                                                                 | hodnota `{AUTHENTICATION}` |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| Bez ověřování                                                                        | `None`                   |
| Jednoho<br>Uživatelé uložení v aplikaci pomocí ASP.NET Core identity                        | `Individual`             |
| Jednoho<br>Uživatelé uložení v [Azure AD B2C](xref:security/authentication/azure-ad-b2c). | `IndividualB2C`          |
| Pracovní nebo školní účty<br>Ověřování organizace pro jednoho tenanta.            | `SingleOrg`              |
| Pracovní nebo školní účty<br>Ověřování organizace pro více tenantů.           | `MultiOrg`               |
| Ověřování systému Windows                                                                   | `Windows`                |

Příkaz vytvoří složku s názvem s hodnotou poskytnutou pro zástupný symbol `{APP NAME}` a jako název aplikace použije název složky. Další informace najdete v tématu [dotnet New](/dotnet/core/tools/dotnet-new) v příručce .NET Core.

<!--

# [Visual Studio for Mac](#tab/visual-studio-mac)

1. Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.

1.

1.

-->

<!--
# [.NET Core CLI](#tab/netcore-cli/)

Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.

| Authentication mechanism                                                                 | `{AUTHENTICATION}` value |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| No Authentication                                                                        | `None`                   |
| Individual<br>Users stored in the app with ASP.NET Core Identity.                        | `Individual`             |
| Individual<br>Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c). | `IndividualB2C`          |
| Work or School Accounts<br>Organizational authentication for a single tenant.            | `SingleOrg`              |
| Work or School Accounts<br>Organizational authentication for multiple tenants.           | `MultiOrg`               |
| Windows Authentication                                                                   | `Windows`                |

The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name. For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.

-->

---

### <a name="opno-locblazor-webassembly-authentication"></a>ověřování Blazorho WebAssembly

V Blazor aplikace WebAssembly lze kontroly ověřování obejít, protože všechny kódy na straně klienta mohou být změněny uživateli. Totéž platí pro všechny technologie aplikací na straně klienta, včetně rozhraní JavaScript SPA nebo nativních aplikací pro libovolný operační systém.

Přidejte odkaz na balíček pro [Microsoft. AspNetCore. Components. Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) do souboru projektu aplikace.

Implementace vlastní služby `AuthenticationStateProvider` pro Blazor aplikace WebAssembly je popsaná v následujících částech.

## <a name="authenticationstateprovider-service"></a>Služba AuthenticationStateProvider

mezi aplikace Blazor serveru patří integrovaná `AuthenticationStateProvider` služba, která získává data stavu ověřování ze `HttpContext.User`ASP.NET Core. To je způsob, jakým se stav ověřování integruje s existujícími mechanismy ASP.NET Core ověřování na straně serveru.

`AuthenticationStateProvider` je základní služba používaná komponentou `AuthorizeView` a `CascadingAuthenticationState` komponentou pro získání stavu ověřování.

Nepoužíváte obvykle `AuthenticationStateProvider` přímo. Použijte [komponentu AuthorizeView](#authorizeview-component) nebo [úkoly<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) přístupy popsané dále v tomto článku. Hlavní nevýhodou použití `AuthenticationStateProvider` přímo je, že komponenta není automaticky oznámena, pokud se změní příslušná data stavu ověřování.

Služba `AuthenticationStateProvider` může poskytnout data <xref:System.Security.Claims.ClaimsPrincipal> aktuálního uživatele, jak je znázorněno v následujícím příkladu:

```razor
@page "/"
@using Microsoft.AspNetCore.Components.Authorization
@inject AuthenticationStateProvider AuthenticationStateProvider

<button @onclick="@LogUsername">Write user info to console</button>

@code {
    private async Task LogUsername()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            Console.WriteLine($"{user.Identity.Name} is authenticated.");
        }
        else
        {
            Console.WriteLine("The user is NOT authenticated.");
        }
    }
}
```

Pokud je `user.Identity.IsAuthenticated` `true` a protože je uživatel <xref:System.Security.Claims.ClaimsPrincipal>, mohou být deklarace identity vyhodnoceny a členství v rolích.

Další informace o vkládání závislostí (DI) a službách najdete v tématu <xref:blazor/dependency-injection> a <xref:fundamentals/dependency-injection>.

## <a name="implement-a-custom-authenticationstateprovider"></a>Implementace vlastního AuthenticationStateProvider

Pokud vytváříte aplikaci Blazor WebAssembly nebo pokud specifikace vaší aplikace naprosto vyžaduje vlastního poskytovatele, implementujte poskytovatele a přepište `GetAuthenticationStateAsync`:

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Authorization;

namespace BlazorSample.Services
{
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
}
```

V Blazor aplikaci WebAssembly je `CustomAuthStateProvider` služba zaregistrovaná v `Main` *program.cs*:

```csharp
using Microsoft.AspNetCore.Blazor.Hosting;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.Extensions.DependencyInjection;
using BlazorSample.Services;

public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddScoped<AuthenticationStateProvider, 
            CustomAuthStateProvider>();
        builder.RootComponents.Add<App>("app");

        await builder.Build().RunAsync();
    }
}
```

Pomocí `CustomAuthStateProvider`se všechny uživatele ověřují pomocí uživatelského jména `mrfibuli`.

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a>Zveřejnit stav ověřování jako kaskádový parametr

Pokud jsou v procedurální logice požadovány údaje o stavu ověřování, například při provádění akce aktivované uživatelem, Získejte údaje o stavu ověřování definováním kaskádového parametru typu `Task<AuthenticationState>`:

```razor
@page "/"

<button @onclick="@LogUsername">Log username</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task LogUsername()
    {
        var authState = await authenticationStateTask;
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            Console.WriteLine($"{user.Identity.Name} is authenticated.");
        }
        else
        {
            Console.WriteLine("The user is NOT authenticated.");
        }
    }
}
```

> [!NOTE]
> V Blazor komponenty aplikace WebAssembly přidejte obor názvů `Microsoft.AspNetCore.Components.Authorization` (`@using Microsoft.AspNetCore.Components.Authorization`).

Pokud je `user.Identity.IsAuthenticated` `true`, můžou být deklarace identity výčtované a ve vyhodnocených rolích se vytvořilo členství.

Nastavte `Task<AuthenticationState>` kaskádový parametr pomocí `AuthorizeRouteView` a `CascadingAuthenticationState` komponenty v souboru *App. Razor* :

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

## <a name="authorization"></a>Autorizace

Po ověření uživatele se uplatní *autorizační* pravidla, která řídí, co může uživatel dělat.

Přístup je obvykle udělen nebo odepřen na základě toho, zda:

* Uživatel je ověřený (přihlášený).
* Uživatel je v *roli*.
* Uživatel má *deklaraci identity*.
* Byla splněna *zásada* .

Každá z těchto konceptů je stejná jako u ASP.NET Core MVC nebo Razor Pages aplikace. Další informace o ASP.NET Core zabezpečení najdete v článcích [ASP.NET Core Security and identity](xref:security/index).

## <a name="authorizeview-component"></a>Komponenta AuthorizeView

Součást `AuthorizeView` selektivně zobrazuje uživatelské rozhraní v závislosti na tom, zda je uživatel oprávněn k zobrazení. Tento přístup je užitečný, když potřebujete jenom *Zobrazit* data pro uživatele a nemusíte používat identitu uživatele v procedurální logice.

Komponenta zpřístupňuje `context` proměnnou typu `AuthenticationState`, kterou můžete použít pro přístup k informacím o přihlášeném uživateli:

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

Obsah značek `<Authorized>` a `<NotAuthorized>` může zahrnovat libovolné položky, jako jsou například jiné interaktivní součásti.

Podmínky autorizace, jako jsou role nebo zásady, které řídí možnosti uživatelského rozhraní nebo přístup, jsou uvedené v části [autorizace](#authorization) .

Pokud nejsou zadané autorizační podmínky, `AuthorizeView` používá výchozí zásady a zpracovává se:

* Ověření uživatelé (přihlášeni) jako autorizované.
* Neověřené (odhlášené) uživatelé jako neautorizované.

### <a name="role-based-and-policy-based-authorization"></a>Ověřování na základě rolí a na základě zásad

Komponenta `AuthorizeView` podporuje autorizaci založenou na *rolích* nebo *na základě zásad* .

U ověřování na základě rolí použijte parametr `Roles`:

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

Další informace naleznete v tématu <xref:security/authorization/roles>.

Pro autorizaci založenou na zásadách použijte parametr `Policy`:

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

Ověřování na základě deklarací identity je zvláštní případ ověřování na základě zásad. Můžete například definovat zásadu, která vyžaduje, aby uživatelé měli určitou deklaraci identity. Další informace naleznete v tématu <xref:security/authorization/policies>.

Tato rozhraní API se dají použít buď v Blazor serveru, nebo v Blazor aplikace WebAssembly.

Pokud není zadána žádná `Roles` ani `Policy`, `AuthorizeView` použije výchozí zásady.

### <a name="content-displayed-during-asynchronous-authentication"></a>Obsah zobrazený během asynchronního ověřování

Blazor umožňuje *asynchronní*určení stavu ověřování. Primární scénář pro tento přístup je v Blazor aplikace WebAssembly, které vytvářejí požadavek na externí koncový bod pro ověřování.

Při ověřování probíhá `AuthorizeView` ve výchozím nastavení nezobrazí žádný obsah. Chcete-li zobrazit obsah, když dojde k ověřování, použijte element `<Authorizing>`:

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

Tento přístup se obvykle nevztahuje na Blazor serverových aplikací. aplikace Blazor serveru znají stav ověřování, jakmile se stav naváže. obsah `Authorizing` lze poskytnout v součásti `AuthorizeView` serverové aplikace Blazor, ale obsah se nikdy nezobrazí.

## <a name="authorize-attribute"></a>[Autorizační] – atribut

Atribut `[Authorize]` lze použít v součástech Razor:

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!NOTE]
> V Blazor komponenty aplikace WebAssembly přidejte do příkladů v této části obor názvů `Microsoft.AspNetCore.Authorization` (`@using Microsoft.AspNetCore.Authorization`).

> [!IMPORTANT]
> Pomocí Blazor směrovače dorazí jenom `[Authorize]` na `@page` součásti. Autorizace se provádí jenom jako aspekt směrování, a *ne* pro podřízené komponenty vygenerované v rámci stránky. K ověření zobrazení určitých částí na stránce použijte místo toho `AuthorizeView`.

Atribut `[Authorize]` také podporuje ověřování na základě rolí nebo na základě zásad. U ověřování na základě rolí použijte parametr `Roles`:

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

Pro autorizaci založenou na zásadách použijte parametr `Policy`:

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

Pokud není zadána žádná `Roles` ani `Policy`, `[Authorize]` používá výchozí zásadu, která ve výchozím nastavení zachází:

* Ověření uživatelé (přihlášeni) jako autorizované.
* Neověřené (odhlášené) uživatelé jako neautorizované.

## <a name="customize-unauthorized-content-with-the-router-component"></a>Přizpůsobení neoprávněného obsahu pomocí součásti směrovače

Komponenta `Router` společně s komponentou `AuthorizeRouteView` umožňuje aplikaci zadat vlastní obsah, pokud:

* Obsah nebyl nalezen.
* Uživatel nevydá `[Authorize]` podmínku použitou pro komponentu. Atribut `[Authorize]` je popsán v části [`[Authorize]` atributu](#authorize-attribute) .
* Probíhá asynchronní ověřování.

Ve výchozí šabloně projektu Blazor serveru ukazuje soubor *App. Razor* , jak nastavit vlastní obsah:

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

Obsah značek `<NotFound>`, `<NotAuthorized>`a `<Authorizing>` může zahrnovat libovolné položky, jako jsou například jiné interaktivní součásti.

Pokud `<NotAuthorized>` element není zadán, `AuthorizeRouteView` používá následující záložní zprávu:

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a>Oznámení o změnách stavu ověřování

Pokud aplikace zjistí, že se změnila základní data stavu ověřování (například kvůli tomu, že se uživatel odhlásil nebo jiný uživatel změnil své role), vlastní `AuthenticationStateProvider` může volitelně vyvolat metodu `NotifyAuthenticationStateChanged` na `AuthenticationStateProvider` základní třídu. Tím se uživatelům upozorní na data stavu ověřování (například `AuthorizeView`), která se mají znovu vykreslovat pomocí nových dat.

## <a name="procedural-logic"></a>Procesní logika

Pokud je aplikace nutná k kontrole autorizačních pravidel v rámci procedurální logiky, použijte k získání <xref:System.Security.Claims.ClaimsPrincipal>uživatele kaskádový parametr typu `Task<AuthenticationState>`. `Task<AuthenticationState>` lze kombinovat s jinými službami, jako je například `IAuthorizationService`, k vyhodnocení zásad.

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
> Do Blazor komponenty aplikace WebAssembly přidejte obory názvů `Microsoft.AspNetCore.Authorization` a `Microsoft.AspNetCore.Components.Authorization`:
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```

## <a name="authorization-in-opno-locblazor-webassembly-apps"></a>Autorizace v Blazor aplikace WebAssembly

V Blazor aplikacích pro WebAssembly lze kontroly autorizace obejít, protože všechny kódy na straně klienta mohou být změněny uživateli. Totéž platí pro všechny technologie aplikací na straně klienta, včetně rozhraní JavaScript SPA nebo nativních aplikací pro libovolný operační systém.

**Na serveru vždy provádějte kontroly autorizace v libovolném koncovém bodu rozhraní API, ke kterému přistupovala aplikace na straně klienta.**

## <a name="troubleshoot-errors"></a>Řešení chyb

Běžné chyby:

* **Autorizace vyžaduje kaskádový parametr typu Task\<AuthenticationState >. Zvažte použití CascadingAuthenticationState k zadání.**

* **`null` hodnota se přijímá pro `authenticationStateTask`**

Je možné, že projekt nebyl vytvořen pomocí šablony serveru Blazor s povoleným ověřováním. Zabalte `<CascadingAuthenticationState>` kolem některé části stromu uživatelského rozhraní, například v *App. Razor* následujícím způsobem:

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

`CascadingAuthenticationState` poskytuje `Task<AuthenticationState>` kaskádový parametr, který pak získá od příslušné služby `AuthenticationStateProvider` DI.

## <a name="additional-resources"></a>Další zdroje

* <xref:security/index>
* <xref:security/blazor/server>
* <xref:security/authentication/windowsauth>
* [Super Blazor:](https://github.com/AdrienTorris/awesome-blazor#authentication) ukázkové odkazy komunity ověřování
