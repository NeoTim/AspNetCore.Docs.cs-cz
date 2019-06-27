---
title: ASP.NET Core Blazor ověřování a autorizace
author: guardrex
description: Další informace o Blazor scénářů ověřování a autorizace.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/26/2019
uid: security/blazor/index
ms.openlocfilehash: b3bca26e7088a8353084a065f9b9593c9d8e08e6
ms.sourcegitcommit: 9bb29f9ba6f0645ee8b9cabda07e3a5aa52cd659
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2019
ms.locfileid: "67406199"
---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a>ASP.NET Core Blazor ověřování a autorizace

Podle [Steve Sanderson](https://github.com/SteveSandersonMS)

ASP.NET Core podporuje konfiguraci a správu zabezpečení v aplikacích Blazor.

Scénáře zabezpečení se liší mezi Blazor serverové a klientské aplikace. Protože Blazor serverové aplikace spustit na serveru, budou moct určit kontroly autorizace:

* Možnosti uživatelského rozhraní zobrazovat uživatele (například položky nabídky, které jsou k dispozici pro uživatele).
* Pravidla přístupu pro oblasti aplikace a komponenty.

Aplikace na straně klienta Blazor na klientech. Autorizace je *pouze* používá k určení, které pokud chcete zobrazit možnosti uživatelského rozhraní. Protože kontroly na straně klienta může obejít uživatelem nebo upraví, aplikace na straně klienta Blazor nemůže vynutit pravidla autorizace přístupu.

## <a name="authentication"></a>Ověřování

Blazor pomocí stávající mechanismy ověřování ASP.NET Core zřizuje identitu uživatele. Přesný postup závisí na tom, jak je hostitelem aplikace Blazor, na straně serveru nebo na straně klienta.

### <a name="blazor-server-side-authentication"></a>Blazor ověřování na straně serveru

Aplikace na straně serveru Blazor provoz přes připojení v reálném čase, který je vytvořen pomocí nástroje SignalR. [Ověřování v aplikacích založených na SignalR](xref:signalr/authn-and-authz) je zpracována při vytvoření připojení. Ověřování může být založen na souboru cookie nebo některé jiné nosný token.

Šablona projektu na straně serveru Blazor můžete nastavit ověřování za vás při vytvoření projektu.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Postupujte podle pokynů sady Visual Studio v <xref:blazor/get-started> článku vytvořte nový projekt na straně serveru Blazor s mechanismus ověřování.

Po výběru **Blazor (serverové)** šablony v **vytvořit novou webovou aplikaci ASP.NET Core** dialogového okna, vyberte **změnu** pod **ověřování** .

Otevře se dialogové okno nabízí stejnou sadu ověřovací mechanismy dostupné pro jiné technologie ASP.NET Core projekty:

* **Bez ověřování**
* **Individuální uživatelské účty** &ndash; mohou být uloženy uživatelských účtů:
  * V rámci aplikace pomocí ASP.NET Core [Identity](xref:security/authentication/identity) systému.
  * S [Azure AD B2C](xref:security/authentication/azure-ad-b2c).
* **Pracovní nebo školní účty**
* **Ověřování Windows**

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Postupujte podle pokynů Visual Studio Code v <xref:blazor/get-started> článku vytvořte nový projekt na straně serveru Blazor s mechanismus ověřování:

```console
dotnet new blazorserverside -o {APP NAME} -au {AUTHENTICATION}
```

Hodnoty povolenou ověřování (`{AUTHENTICATION}`) jsou uvedeny v následující tabulce.

| Mechanismus ověřování                                                                 | `{AUTHENTICATION}` Hodnota |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| Bez ověřování                                                                        | `None`                   |
| Osoba<br>Uživatelé uložení v aplikaci s ASP.NET Core Identity.                        | `Individual`             |
| Osoba<br>Uživatelé ukládají v [Azure AD B2C](xref:security/authentication/azure-ad-b2c). | `IndividualB2C`          |
| Pracovní nebo školní účty<br>Ověřování organizace pro jednoho tenanta.            | `SingleOrg`              |
| Pracovní nebo školní účty<br>Ověřování organizace pro více tenantů.           | `MultiOrg`               |
| Ověřování systému Windows                                                                   | `Windows`                |

Tento příkaz vytvoří složku s názvem se hodnota zadaná pro `{APP NAME}` zástupný text a používá název složky názvem aplikace. Další informace najdete v tématu [dotnet nové](/dotnet/core/tools/dotnet-new) příkaz v příručce .NET Core.

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
dotnet new blazorserverside -o {APP NAME} -au {AUTHENTICATION}
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

V aplikacích Blazor na straně klienta lze vynechat kontroly ověřování vzhledem k tomu, že veškerý kód na straně klienta je možné upravovat prostřednictvím uživatelů. Totéž platí pro všechny aplikace na straně klienta technologií, jako je JavaScript SPA rozhraní nebo nativní aplikace pro libovolný operační systém.

Implementace vlastního `AuthenticationStateProvider` služby service for apps Blazor na straně klienta je popsané v následujících částech.

## <a name="authenticationstateprovider-service"></a>AuthenticationStateProvider služby

Aplikace na straně serveru Blazor zahrnují integrované `AuthenticationStateProvider` služba, která získává data o stavu ověřování z ASP.NET Core `HttpContext.User`. To je, jak stav ověřování integruje do stávající mechanismy ověřování na straně serveru ASP.NET Core.

`AuthenticationStateProvider` je základní služby používané `AuthorizeView` komponenty a `CascadingAuthenticationState` součást pro získání stavu ověření.

Nepoužívejte obvykle `AuthenticationStateProvider` přímo. Použití [AuthorizeView komponenty](#authorizeview-component) nebo [úloh<AuthenticationState> ](#expose-the-authentication-state-as-a-cascading-parameter) přístupů popsaných dále v tomto článku. Hlavní nevýhodou použití `AuthenticationStateProvider` přímo je, že není-li základní ověřování stavu změny dat automaticky upozorněn komponentu.

`AuthenticationStateProvider` Služba může poskytovat aktuální uživatel <xref:System.Security.Claims.ClaimsPrincipal> data, jak je znázorněno v následujícím příkladu:

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

Pokud `user.Identity.IsAuthenticated` je `true` , a proto je uživatel <xref:System.Security.Claims.ClaimsPrincipal>, jsou uvedené deklarace identity a vyhodnocování členství v rolích.

Další informace o injektáž závislostí (DI) a služby, najdete v části <xref:blazor/dependency-injection> a <xref:fundamentals/dependency-injection>.

## <a name="implement-a-custom-authenticationstateprovider"></a>Implementovat vlastní AuthenticationStateProvider

Pokud jste sestavujete aplikaci na straně klienta Blazor nebo pokud vaše aplikace specifikace naprosto vyžaduje vlastního zprostředkovatele implementaci zprostředkovatele a přepsat `GetAuthenticationStateAsync`:

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

`CustomAuthStateProvider` Není registrován v `Startup.ConfigureServices`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
}
```

Použití `CustomAuthStateProvider`, všichni uživatelé, se ověří pomocí uživatelského jména `mrfibuli`.

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a>Stav ověřování vystavit jako parametr šablony

Pokud data ověření stavu, je třeba procesní logiky, jako je například provést akci při aktivaci tohoto uživatele získat ověřovací data stavu tak, že definujete šablony parametr typu `Task<AuthenticationState>`:

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

Pokud `user.Identity.IsAuthenticated` je `true`, jsou uvedené deklarace identity a vyhodnocování členství v rolích.

Nastavit `Task<AuthenticationState>` kaskádové pomocí parametru `CascadingAuthenticationState` komponenty:

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

Po ověření uživatele *autorizace* pravidla se používají k řízení, co můžete dělat uživatele.

Obvykle povolen nebo odepřen přístup podle toho, jestli:

* Ověření uživatele (přihlášení).
* Je uživatel v *role*.
* Uživatel má *deklarace identity*.
* A *zásady* je spokojeni.

Každá z těchto konceptů je stejný jako v aplikaci ASP.NET Core MVC nebo stránky Razor. Další informace o zabezpečení ASP.NET Core, najdete v článcích v části [ASP.NET Core zabezpečení a identita](xref:security/index).

## <a name="authorizeview-component"></a>Komponenta AuthorizeView

`AuthorizeView` Komponenty selektivně zobrazí uživatelské rozhraní v závislosti na tom, jestli je uživatel oprávnění a prohlédněte si ho. Tento přístup je užitečný, pokud je potřeba jenom *zobrazit* data uživatele a není nutné používat identitu uživatelů v procesní logiky.

Komponenta zpřístupní `context` proměnné typu `AuthenticationState`, který můžete použít pro přístup k informacím o přihlášeného uživatele:

```cshtml
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

Pokud uživatel není ověřen, lze také zadat jiný obsah pro zobrazení:

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

Obsah `<Authorized>` a `<NotAuthorized>` může obsahovat libovolné položky, jako další interaktivní komponenty.

Autorizace podmínky, například role nebo zásady, které řídí možnosti uživatelského rozhraní nebo přístup, jsou popsané v [autorizace](#authorization) oddílu.

Pokud nejsou zadané podmínky autorizace, `AuthorizeView` využívá výchozí zásady a zpracovává:

* Ověřeným uživatelům (přihlášeni), jako oprávněnou.
* Neověřené uživatele (podepsané out) jako neověřené.

### <a name="role-based-and-policy-based-authorization"></a>Ověřování založené na rolích a na základě zásad

`AuthorizeView` Komponenta podporuje *na základě rolí* nebo *založené na zásadách* autorizace.

Autorizace na základě role, použít `Roles` parametr:

```cshtml
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

Další informace naleznete v tématu <xref:security/authorization/roles>.

Na základě zásad autorizace, použijte `Policy` parametr:

```cshtml
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

Autorizace na základě deklarací identity je zvláštní případ autorizace na základě zásad. Například můžete definovat zásady, které vyžaduje, aby uživatelé měli určité deklarace identity. Další informace naleznete v tématu <xref:security/authorization/policies>.

Tato rozhraní API je možné v Blazor serverové nebo klientské aplikace Blazor.

Pokud ani `Roles` ani `Policy` není zadána, `AuthorizeView` využívá výchozí zásady.

### <a name="content-displayed-during-asynchronous-authentication"></a>Obsah zobrazený při asynchronním ověřování

Blazor umožňuje pro ověřování stavu bude určen *asynchronně*. Primární scénáře pro tento přístup je v aplikacích Blazor na straně klienta, které vytvoříte žádost o externí koncový bod pro ověřování.

Probíhá ověřování, `AuthorizeView` žádný obsah se zobrazí ve výchozím nastavení. Chcete-li zobrazit obsah, když dojde k ověření, použijte `<Authorizing>` element:

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

Tento přístup se obvykle pro Blazor serverové aplikace. Stav ověřování Blazor serverové aplikace vědět, co nejdříve pokládáme stav, stav. `Authorizing` obsah lze zadat v aplikaci na straně serveru Blazor `AuthorizeView` součásti, ale obsah je nikdy zobrazeny.

## <a name="authorize-attribute"></a>Atribut [Povolit]

Stejně jako aplikace můžete použít `[Authorize]` s kontroler MVC nebo stránky Razor `[Authorize]` lze také použít s komponentami Razor:

```cshtml
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> Používejte pouze `[Authorize]` na `@page` součásti kontaktovat prostřednictvím směrovače Blazor. Se autorizace provádí pouze jako určitý aspekt směrování a *není* pro podřízené součásti vykreslen v rámci stránky. Chcete-li povolit zobrazení konkrétní součástí v rámci stránky, použijte `AuthorizeView` místo.

Budete muset přidat `@using Microsoft.AspNetCore.Authorization` součásti nebo položky *_Imports.razor* souboru v pořadí pro komponentu pro kompilaci.

`[Authorize]` Atribut také podporuje ověřování na základě rolí nebo na základě zásad. Autorizace na základě role, použít `Roles` parametr:

```cshtml
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

Na základě zásad autorizace, použijte `Policy` parametr:

```cshtml
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

Pokud ani `Roles` ani `Policy` není zadána, `[Authorize]` využívá výchozí zásady, které ve výchozím nastavení se zachází:

* Ověřeným uživatelům (přihlášeni), jako oprávněnou.
* Neověřené uživatele (podepsané out) jako neověřené.

## <a name="customize-unauthorized-content-with-the-router-component"></a>Přizpůsobit neoprávněné obsah s komponentou směrovače

`Router` Komponenta umožňuje aplikaci určit vlastní obsah, pokud:

* Obsah nebyl nalezen.
* Uživatel `[Authorize]` podmínku použít pro komponentu. `[Authorize]` Se věnuje atribut [atribut [Authorize]](#authorize-attribute) části.
* Probíhá asynchronní ověřování.

Ve výchozí šablona Blazor projektu na straně serveru *App.razor* souboru ukazuje, jak nastavit vlastní obsah:

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

Obsah `<NotFoundContent>`, `<NotAuthorizedContent>`, a `<AuthorizingContent>` může obsahovat libovolné položky, jako další interaktivní komponenty.

Pokud `<NotAuthorizedContent>` není zadán, směrovač používá záložní následující zpráva:

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a>Oznámení o změně stavu ověřování

Pokud aplikace zjistí, že došlo ke změně podkladová data ověření stavu (například proto odhlášení uživatele nebo jiný uživatel změnil své role), vlastní `AuthenticationStateProvider` můžete případně vyvolat metodu `NotifyAuthenticationStateChanged` na `AuthenticationStateProvider` základní Třída. To upozorní spotřebitelé dat stavu ověřování (například `AuthorizeView`) k rerender nová data.

## <a name="procedural-logic"></a>Procesní logiky

Pokud aplikace je potřeba zkontrolovat autorizační pravidla, jako součást procesní logiky, pomocí kaskádových akcí parametr typu `Task<AuthenticationState>` získat uživatele <xref:System.Security.Claims.ClaimsPrincipal>. `Task<AuthenticationState>` je možné kombinovat s dalšími službami, jako například `IAuthorizationService`, k vyhodnocení zásad.

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

## <a name="authorization-in-blazor-client-side-apps"></a>Autorizace v aplikacích Blazor na straně klienta

V aplikacích Blazor na straně klienta lze vynechat kontroly autorizace vzhledem k tomu, že veškerý kód na straně klienta je možné upravovat prostřednictvím uživatelů. Totéž platí pro všechny aplikace na straně klienta technologií, jako je JavaScript SPA rozhraní nebo nativní aplikace pro libovolný operační systém.

**Vždy provádějte kontroly autorizace na serveru v rámci žádné koncové body rozhraní API přistupuje aplikace na straně klienta.**

## <a name="troubleshoot-errors"></a>Řešení potíží s chybami

Běžné chyby:

* **Vyžaduje ověřování šablony parametr typu úloh\<AuthenticationState >. Zvažte použití CascadingAuthenticationState zadat.**

* **`null` je přijímána hodnota pro `authenticationStateTask`**

Je pravděpodobné, že projekt nebyl vytvořen pomocí šablony Blazor na straně serveru s povoleným ověřováním. Zabalení `<CascadingAuthenticationState>` některé části uživatelského rozhraní stromu, například v *App.razor* následujícím způsobem:

```cshtml
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

`CascadingAuthenticationState` Poskytuje `Task<AuthenticationState>` šablony parametr, který pak přijme ze základního `AuthenticationStateProvider` DI služby.

## <a name="additional-resources"></a>Další zdroje

* <xref:security/index>
* <xref:security/authentication/windowsauth>
