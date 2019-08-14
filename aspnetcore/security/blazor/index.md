---
title: ASP.NET Core ověřování a autorizace Blazor
author: guardrex
description: Přečtěte si o scénářích ověřování a autorizace Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/26/2019
uid: security/blazor/index
ms.openlocfilehash: 87d61a7ccda209243a62bc54467b8f02dad92c24
ms.sourcegitcommit: 89fcc6cb3e12790dca2b8b62f86609bed6335be9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68994189"
---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a>ASP.NET Core ověřování a autorizace Blazor

Pomocí [Steve Sanderson](https://github.com/SteveSandersonMS)

ASP.NET Core podporuje konfiguraci a správu zabezpečení v aplikacích Blazor.

Scénáře zabezpečení se liší mezi Blazor a klientskými aplikacemi na straně serveru. Vzhledem k tomu, že Blazor aplikace na straně serveru běží na serveru, kontroly autorizace můžou určit:

* Možnosti uživatelského rozhraní prezentované uživateli (například položky nabídky, které jsou k dispozici uživateli).
* Pravidla přístupu pro oblasti aplikace a součástí

Blazor aplikace na straně klienta běží na klientovi. Autorizace se používá *jenom* k určení možností uživatelského rozhraní, které se mají zobrazit. Vzhledem k tomu, že kontroly na straně klienta mohou být upraveny nebo vynechány uživatelem, aplikace Blazor na straně klienta nemůže vyhovět autorizačním pravidlům.

## <a name="authentication"></a>Ověřování

Blazor používá k vytvoření identity uživatele existující mechanismy ověřování ASP.NET Core. Přesný mechanismus závisí na tom, jak je aplikace Blazor hostována, na straně serveru nebo na straně klienta.

### <a name="blazor-server-side-authentication"></a>Blazor ověřování na straně serveru

Blazor aplikace na straně serveru fungují prostřednictvím připojení v reálném čase vytvořeného pomocí signálu. [Ověřování v aplikacích založených na službě Signal](xref:signalr/authn-and-authz) se zpracovává při navázání připojení. Ověřování může být založené na souboru cookie nebo nějakém jiném nosným tokenu.

Šablona projektu Blazor na straně serveru může nastavit ověřování při vytvoření projektu.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Podle pokynů pro Visual Studio v <xref:blazor/get-started> článku vytvořte nový projekt Blazor na straně serveru s mechanismem ověřování.

Po výběru šablony **aplikace Blazor serveru** v dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** vyberte v části **ověřování**možnost **změnit** .

Otevře se dialogové okno s nabídkou stejné sady mechanismů ověřování pro jiné projekty ASP.NET Core:

* **Bez ověřování**
* **Jednotlivé uživatelské účty** &ndash; Uživatelské účty je možné uložit:
  * V aplikaci, která používá systém [identit](xref:security/authentication/identity) ASP.NET Core.
  * S [Azure AD B2C](xref:security/authentication/azure-ad-b2c).
* **Pracovní nebo školní účty**
* **Ověřování systému Windows**

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Postupujte podle pokynů Visual Studio Code v <xref:blazor/get-started> článku Vytvoření nového projektu na straně serveru Blazor pomocí mechanismu ověřování:

```console
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Přípustné hodnoty ověřování (`{AUTHENTICATION}`) jsou uvedeny v následující tabulce.

| Mechanismus ověřování                                                                 | `{AUTHENTICATION}`osa |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| Bez ověřování                                                                        | `None`                   |
| Jednoho<br>Uživatelé uložení v aplikaci pomocí ASP.NET Core identity                        | `Individual`             |
| Jednoho<br>Uživatelé uložení v [Azure AD B2C](xref:security/authentication/azure-ad-b2c). | `IndividualB2C`          |
| Pracovní nebo školní účty<br>Ověřování organizace pro jednoho tenanta.            | `SingleOrg`              |
| Pracovní nebo školní účty<br>Ověřování organizace pro více tenantů.           | `MultiOrg`               |
| Ověřování systému Windows                                                                   | `Windows`                |

Příkaz vytvoří složku s názvem s hodnotou zadanou pro `{APP NAME}` zástupný text a jako název aplikace použije název složky. Další informace najdete v tématu [dotnet New](/dotnet/core/tools/dotnet-new) v příručce .NET Core.

<!--

# [Visual Studio for Mac](#tab/visual-studio-mac)

1. Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.

1.

1.

-->

<!--
# [.NET Core CLI](#tab/netcore-cli/)

Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor server-side project with an authentication mechanism:

```console
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

### <a name="blazor-client-side-authentication"></a>Blazor ověřování na straně klienta

V Blazor aplikací na straně klienta se můžou kontroly ověřování obejít, protože všichni můžou upravovat kód na straně klienta. Totéž platí pro všechny technologie aplikací na straně klienta, včetně rozhraní JavaScript SPA nebo nativních aplikací pro libovolný operační systém.

Implementace vlastní `AuthenticationStateProvider` služby pro Blazor aplikace na straně klienta je popsaná v následujících částech.

## <a name="authenticationstateprovider-service"></a>Služba AuthenticationStateProvider

Blazor aplikace na straně serveru obsahují integrovanou `AuthenticationStateProvider` službu, která získává data o stavu ověřování z `HttpContext.User`ASP.NET Core. To je způsob, jakým se stav ověřování integruje s existujícími mechanismy ASP.NET Core ověřování na straně serveru.

`AuthenticationStateProvider`je základní službou, kterou `AuthorizeView` komponenta a `CascadingAuthenticationState` komponenta používá k získání stavu ověřování.

Obvykle přímo nepoužíváte `AuthenticationStateProvider` . Použijte [komponentu AuthorizeView](#authorizeview-component) nebo přístupy [k<AuthenticationState> úkolům](#expose-the-authentication-state-as-a-cascading-parameter) , které jsou popsány dále v tomto článku. Hlavní nevýhodou použití `AuthenticationStateProvider` přímo je, že součást není automaticky oznámena v případě, že dojde ke změně podkladových dat stavu ověřování.

Služba může poskytnout <xref:System.Security.Claims.ClaimsPrincipal> data aktuálního uživatele, jak je znázorněno v následujícím příkladu: `AuthenticationStateProvider`

```cshtml
@page "/"
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

Pokud `user.Identity.IsAuthenticated` je `true` a<xref:System.Security.Claims.ClaimsPrincipal>vzhledem k tomu, že je uživatel a, mohou být deklarace identity vyhodnoceny a ve vyhodnocování členů.

Další informace o vkládání závislostí (di) a službách naleznete v <xref:blazor/dependency-injection> tématu <xref:fundamentals/dependency-injection>a.

## <a name="implement-a-custom-authenticationstateprovider"></a>Implementace vlastního AuthenticationStateProvider

Pokud vytváříte aplikaci Blazor na straně klienta nebo pokud specifikace vaší aplikace naprosto vyžaduje vlastního poskytovatele, implementujte zprostředkovatele a přepište `GetAuthenticationStateAsync`:

```csharp
class CustomAuthStateProvider : AuthenticationStateProvider
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

Služba je registrovaná v `Startup.ConfigureServices`: `CustomAuthStateProvider`

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
}
```

Pomocí nástroje se všechny uživatele ověřují pomocí uživatelského jména `mrfibuli`. `CustomAuthStateProvider`

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a>Zveřejnit stav ověřování jako kaskádový parametr

Pokud jsou v procedurální logice požadovány údaje o stavu ověřování, například při provádění akce aktivované uživatelem, Získejte údaje o stavu ověřování definováním kaskádového parametru typu `Task<AuthenticationState>`:

```cshtml
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

V takovém případě `user.Identity.IsAuthenticated` můžou být deklarace identity výčtové a ve vyhodnocených rolích. `true`

Nastavte kaskádový parametr `CascadingAuthenticationState` pomocí komponenty: `Task<AuthenticationState>`

```cshtml
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        <NotFoundContent>
            <h1>Sorry</h1>
            <p>Sorry, there's nothing at this address.</p>
        </NotFoundContent>
    </Router>
</CascadingAuthenticationState>
```

## <a name="authorization"></a>Autorizace

Po ověření uživatele se uplatní *autorizační* pravidla, která řídí, co může uživatel dělat.

Přístup je obvykle udělen nebo odepřen na základě toho, zda:

* Uživatel je ověřený (přihlášený).
* Uživatel je v *roli*.
* Uživatel má deklaraci *identity*.
* Byla splněna *zásada* .

Každá z těchto konceptů je stejná jako u ASP.NET Core MVC nebo Razor Pages aplikace. Další informace o ASP.NET Core zabezpečení najdete v článcích [ASP.NET Core Security and identity](xref:security/index).

## <a name="authorizeview-component"></a>Komponenta AuthorizeView

`AuthorizeView` Komponenta selektivně zobrazuje uživatelské rozhraní v závislosti na tom, zda je uživatel autorizován pro jeho zobrazení. Tento přístup je užitečný, když potřebujete jenom *Zobrazit* data pro uživatele a nemusíte používat identitu uživatele v procedurální logice.

Komponenta zveřejňuje `context` proměnnou typu `AuthenticationState`, kterou můžete použít pro přístup k informacím o přihlášeném uživateli:

```cshtml
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

V případě, že se uživatel neověřuje, můžete také Dodejte jiný obsah, který se zobrazí:

```cshtml
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

Obsah `<Authorized>` a`<NotAuthorized>` může zahrnovat libovolné položky, jako jsou například jiné interaktivní komponenty.

Podmínky autorizace, jako jsou role nebo zásady, které řídí možnosti uživatelského rozhraní nebo přístup, jsou uvedené v části [autorizace](#authorization) .

Pokud nejsou zadané autorizační podmínky, `AuthorizeView` použije se výchozí zásada a bude se považovat za:

* Ověření uživatelé (přihlášeni) jako autorizované.
* Neověřené (odhlášené) uživatelé jako neautorizované.

### <a name="role-based-and-policy-based-authorization"></a>Ověřování na základě rolí a na základě zásad

Komponenta podporuje autorizaci na základě rolí nebo *na základě zásad* . `AuthorizeView`

Pro autorizaci založenou na rolích `Roles` použijte parametr:

```cshtml
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

Další informace naleznete v tématu <xref:security/authorization/roles>.

Pro autorizaci založenou na zásadách použijte `Policy` parametr:

```cshtml
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

Ověřování na základě deklarací identity je zvláštní případ ověřování na základě zásad. Můžete například definovat zásadu, která vyžaduje, aby uživatelé měli určitou deklaraci identity. Další informace naleznete v tématu <xref:security/authorization/policies>.

Tato rozhraní API se dají použít buď v Blazor na straně serveru, nebo v Blazor klientských aplikacích.

Pokud není zadán `Policy` ani `AuthorizeView` ani, používá výchozí zásady. `Roles`

### <a name="content-displayed-during-asynchronous-authentication"></a>Obsah zobrazený během asynchronního ověřování

Blazor umožňuje *asynchronní*určení stavu ověřování. Primární scénář pro tento přístup je v Blazor aplikací na straně klienta, které vytvářejí požadavek na externí koncový bod pro ověřování.

V průběhu ověřování ve `AuthorizeView` výchozím nastavení nezobrazí žádný obsah. Chcete-li zobrazit obsah, když dojde k `<Authorizing>` ověřování, použijte element:

```cshtml
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

Tento přístup se obvykle nevztahuje na Blazor aplikace na straně serveru. Blazor aplikace na straně serveru znají stav ověřování, jakmile se naváže stav. `Authorizing`obsah může být k dispozici v `AuthorizeView` součásti aplikace na straně serveru Blazor, ale obsah se nikdy nezobrazí.

## <a name="authorize-attribute"></a>[Autorizační] – atribut

Stejně jako aplikace může `[Authorize]` být použita s řadičem MVC nebo stránkou Razor, `[Authorize]` lze také použít s komponentami Razor:

```cshtml
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> Používejte `[Authorize]` pouze v `@page` součástech, které jsou dostupné prostřednictvím směrovače Blazor. Autorizace se provádí jenom jako aspekt směrování, a *ne* pro podřízené komponenty vygenerované v rámci stránky. Chcete-li autorizovat zobrazení určitých částí v rámci stránky, použijte `AuthorizeView` místo toho.

Aby mohla být komponenta zkompilována, může být nutné přidat `@using Microsoft.AspNetCore.Authorization` buď do součásti, nebo do souboru *_Imports. Razor* .

`[Authorize]` Atribut také podporuje ověřování na základě rolí nebo na základě zásad. Pro autorizaci založenou na rolích `Roles` použijte parametr:

```cshtml
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

Pro autorizaci založenou na zásadách použijte `Policy` parametr:

```cshtml
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

Pokud není zadán `Policy` ani `[Authorize]` ani, používá výchozí zásadu, která ve výchozím nastavení zachází: `Roles`

* Ověření uživatelé (přihlášeni) jako autorizované.
* Neověřené (odhlášené) uživatelé jako neautorizované.

## <a name="customize-unauthorized-content-with-the-router-component"></a>Přizpůsobení neoprávněného obsahu pomocí součásti směrovače

`Router` Komponenta umožňuje aplikaci zadat vlastní obsah, pokud:

* Obsah nebyl nalezen.
* Uživatel nezdařil `[Authorize]` podmínku použitou pro komponentu. Atribut je popsán v části [[autorizační] atributu.](#authorize-attribute) `[Authorize]`
* Probíhá asynchronní ověřování.

Ve výchozí šabloně projektu na straně serveru Blazor soubor *App. Razor* ukazuje, jak nastavit vlastní obsah:

```cshtml
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        <NotFoundContent>
            <h1>Sorry</h1>
            <p>Sorry, there's nothing at this address.</p>
        </NotFoundContent>
        <NotAuthorizedContent>
            <h1>Sorry</h1>
            <p>You're not authorized to reach this page.</p>
            <p>You may need to log in as a different user.</p>
        </NotAuthorizedContent>
        <AuthorizingContent>
            <h1>Authentication in progress</h1>
            <p>Only visible while authentication is in progress.</p>
        </AuthorizingContent>
    </Router>
</CascadingAuthenticationState>
```

Obsah `<NotFoundContent>`, `<NotAuthorizedContent>`a můžezahrnovatlibovolnépoložky,jakojsounapříkladjinéinteraktivníkomponenty.`<AuthorizingContent>`

Pokud `<NotAuthorizedContent>` parametr není zadán, bude směrovač používat následující záložní zprávu:

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a>Oznámení o změnách stavu ověřování

Pokud aplikace zjistí, že se změnila základní data stavu ověřování (například kvůli tomu, že se uživatel odhlásil nebo jiný uživatel změnil své role), vlastní `AuthenticationStateProvider` možnost může volitelně vyvolat metodu `NotifyAuthenticationStateChanged` na `AuthenticationStateProvider` bázi Base. Deník. Tím se uživatele upozorní na data stavu ověřování (například `AuthorizeView`) k opakovanému vykreslení pomocí nových dat.

## <a name="procedural-logic"></a>Procesní logika

Pokud je aplikace nutná k kontrole autorizačních pravidel v rámci procedurální logiky, použijte k získání `Task<AuthenticationState>` <xref:System.Security.Claims.ClaimsPrincipal>uživatele kaskádový parametr typu. `Task<AuthenticationState>`lze kombinovat s jinými službami, například `IAuthorizationService`, k vyhodnocení zásad.

```cshtml
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

## <a name="authorization-in-blazor-client-side-apps"></a>Autorizace v Blazor klientských aplikacích na straně klienta

V aplikacích Blazor na straně klienta se můžou kontroly autorizace obejít, protože všichni můžou upravovat kód na straně klienta. Totéž platí pro všechny technologie aplikací na straně klienta, včetně rozhraní JavaScript SPA nebo nativních aplikací pro libovolný operační systém.

**Na serveru vždy provádějte kontroly autorizace v libovolném koncovém bodu rozhraní API, ke kterému přistupovala aplikace na straně klienta.**

## <a name="troubleshoot-errors"></a>Řešení chyb

Běžné chyby:

* **Autorizace vyžaduje kaskádový parametr typu Task\<AuthenticationState >. Zvažte použití CascadingAuthenticationState k zadání.**

* **`null`hodnota je přijata pro`authenticationStateTask`**

Je možné, že projekt nebyl vytvořen pomocí šablony Blazor na straně serveru s povoleným ověřováním. Zabalte kolem některé části stromu uživatelského rozhraní, například v *App. Razor* , následovně: `<CascadingAuthenticationState>`

```cshtml
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

Doplní kaskádový parametr, který zase získá z podkladové `AuthenticationStateProvider` služby di. `Task<AuthenticationState>` `CascadingAuthenticationState`

## <a name="additional-resources"></a>Další zdroje

* <xref:security/index>
* <xref:security/authentication/windowsauth>
