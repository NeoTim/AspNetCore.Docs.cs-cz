---
title: Zabezpečení samostatné aplikace Blazor ASP.NET Coreového sestavení pomocí knihovny ověřování
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 043e4548ad6f40fdf1e6c27cd51946c7bf59a66e
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110942"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-the-authentication-library"></a>Zabezpečení samostatné aplikace Blazor ASP.NET Coreového sestavení pomocí knihovny ověřování

Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> Pokyny v tomto článku se týkají ASP.NET Core 3,2 Preview 4. Toto téma se bude aktualizovat na verzi Preview 5 v pátek, 24. dubna.

*V případě Azure Active Directory (AAD) a Azure Active Directory B2C (AAD B2C) nepostupujte podle pokynů v tomto tématu. Podívejte se na témata AAD a AAD B2C v tomto uzlu obsah.*

Chcete-li Blazor vytvořit samostatnou aplikaci WebAssembly `Microsoft.AspNetCore.Components.WebAssembly.Authentication` , která používá knihovnu, spusťte v příkazovém prostředí následující příkaz:

```dotnetcli
dotnet new blazorwasm -au Individual
```

Chcete-li určit umístění výstupu, které vytvoří složku projektu, pokud neexistuje, zahrňte možnost výstup do příkazu s cestou (například `-o BlazorSample`). Název složky se také stal součástí názvu projektu.

V aplikaci Visual Studio [vytvořte Blazor aplikaci WebAssembly](xref:blazor/get-started). Nastavte **ověřování** na **jednotlivé uživatelské účty** s možností **Uložit uživatelské účty do aplikace** .

## <a name="authentication-package"></a>Ověřovací balíček

Když je aplikace vytvořená tak, aby používala jednotlivé uživatelské účty, aplikace automaticky obdrží odkaz `Microsoft.AspNetCore.Components.WebAssembly.Authentication` na balíček v souboru projektu aplikace. Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.

Pokud se do aplikace přidává ověřování, přidejte balíček do souboru projektu aplikace ručně:

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

Nahraďte `{VERSION}` odkazem na předchozí balíček verzí `Microsoft.AspNetCore.Blazor.Templates` balíčku, který je uvedený v <xref:blazor/get-started> článku.

## <a name="authentication-service-support"></a>Podpora ověřovací služby

Podpora ověřování uživatelů je registrovaná v kontejneru služby s metodou `AddOidcAuthentication` rozšíření poskytovanou `Microsoft.AspNetCore.Components.WebAssembly.Authentication` balíčkem. Tato metoda nastavuje všechny služby, které aplikace potřebuje k interakci s poskytovatelem identity (IP).

*Program.cs*:

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    options.ProviderOptions.Authority = "{AUTHORITY}";
    options.ProviderOptions.ClientId = "{CLIENT ID}";
});
```

Podpora ověřování pro samostatné aplikace se nabízí pomocí Open ID Connect (OIDC). `AddOidcAuthentication` Metoda přijímá zpětné volání ke konfiguraci parametrů požadovaných k ověření aplikace pomocí OIDC. Hodnoty požadované pro konfiguraci aplikace lze získat z IP adresy kompatibilní s OIDC. Získejte hodnoty při registraci aplikace, ke kterým obvykle dochází na online portálu.

## <a name="access-token-scopes"></a>Obory přístupového tokenu

Blazor Šablona protokolu WebAssembly nekonfiguruje aplikaci automaticky pro vyžádání přístupového tokenu pro zabezpečené rozhraní API. Chcete-li zřídit token jako součást toku přihlášení, přidejte obor do výchozího oboru tokenu `OidcProviderOptions`:

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> Pokud Azure Portal poskytne identifikátor URI oboru a **aplikace vyvolá neošetřenou výjimku** , když obdrží od rozhraní API *neautorizovanou odpověď 401* , zkuste použít identifikátor URI oboru, který neobsahuje schéma a hostitele. Azure Portal například může poskytovat jeden z následujících formátů identifikátoru URI oboru:
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> Zadejte identifikátor URI oboru bez schématu a hostitele:
>
> ```csharp
> options.ProviderOptions.DefaultScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

Další informace naleznete v tématu <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

## <a name="imports-file"></a>Importovat soubor

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>Indexová stránka

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a>Součást aplikace

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>Komponenta RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>Komponenta LoginDisplay

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a>Součást ověřování

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Další materiály a zdroje informací

* <xref:security/blazor/webassembly/additional-scenarios>
 