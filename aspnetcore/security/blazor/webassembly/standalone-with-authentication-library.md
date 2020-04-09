---
title: Zabezpečení samostatné Blazor aplikace ASP.NET Core WebAssembly pomocí knihovny Ověřování
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 893fff10df37e1c2be549604f4cb83cd20049108
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977038"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-the-authentication-library"></a>Zabezpečení samostatné Blazor aplikace ASP.NET Core WebAssembly pomocí knihovny Ověřování

[Javier Calvarro Nelson](https://github.com/javiercn) a Luke [Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

*Pro Azure Active Directory (AAD) a Azure Active Directory B2C (AAD B2C) nepostupujte podle pokynů v tomto tématu. Viz témata AAD a AAD B2C v tomto uzlu obsahu.*

Chcete-li Blazor vytvořit samostatnou aplikaci WebAssembly, která používá `Microsoft.AspNetCore.Components.WebAssembly.Authentication` knihovnu, spusťte v příkazovém prostředí následující příkaz:

```dotnetcli
dotnet new blazorwasm -au Individual
```

Chcete-li určit výstupní umístění, které vytvoří složku projektu, pokud neexistuje, zahrňte do `-o BlazorSample`příkazu možnost výstupu s cestou (například ). Název složky se také stane součástí názvu projektu.

V Sadě Visual Studio [vytvořte Blazor aplikaci WebAssembly](xref:blazor/get-started). Nastavte **ověřování** na **jednotlivé uživatelské účty** pomocí možnosti uživatelské účty Store v **aplikaci.**

## <a name="authentication-package"></a>Ověřovací balíček

Když je aplikace vytvořena pro použití individuálních uživatelských účtů, `Microsoft.AspNetCore.Components.WebAssembly.Authentication` aplikace automaticky obdrží odkaz na balíček pro balíček v souboru projektu aplikace. Balíček obsahuje sadu primitiv, které pomáhají aplikaci ověřovat uživatele a získat tokeny pro volání chráněných api.

Pokud přidáváte ověřování do aplikace, přidejte balíček ručně do souboru projektu aplikace:

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

Nahraďte `{VERSION}` v předchozím odkazu na `Microsoft.AspNetCore.Blazor.Templates` balíček verzí <xref:blazor/get-started> balíčku uvedenou v článku.

## <a name="authentication-service-support"></a>Podpora ověřovací služby

Podpora pro ověřování uživatelů je registrována v `AddOidcAuthentication` kontejneru služby `Microsoft.AspNetCore.Components.WebAssembly.Authentication` s metodou rozšíření poskytované balíček. Tato metoda nastaví všechny služby potřebné pro interakci aplikace s poskytovatelem identity (IP).

*Program.cs*:

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    options.ProviderOptions.Authority = "{AUTHORITY}";
    options.ProviderOptions.ClientId = "{CLIENT ID}";
});
```

Podpora ověřování pro samostatné aplikace je nabízena pomocí open id connect (OIDC). Metoda `AddOidcAuthentication` přijímá zpětné volání ke konfiguraci parametrů potřebných k ověření aplikace pomocí OIDC. Hodnoty potřebné pro konfiguraci aplikace lze získat z IP kompatibilní s OIDC. Získat hodnoty při registraci aplikace, která se obvykle vyskytuje v jejich online portálu.

## <a name="access-token-scopes"></a>Obory přístupových tokenů

Šablona Blazor WebAssembly automaticky nekonfiguruje aplikaci tak, aby požadovala přístupový token pro zabezpečené rozhraní API. Chcete-li zřídit token jako součást toku přihlášení, přidejte obor `OidcProviderOptions`do výchozích oborů tokenu :

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> Pokud portál Azure poskytuje identifikátor URI oboru a **aplikace vyvolá neošetřenou výjimku,** když obdrží *neoprávněnou odpověď 401* z rozhraní API, zkuste použít identifikátor URI oboru, který neobsahuje schéma a hostitele. Portál Azure může například poskytovat jeden z následujících formátů identifikátoru URI oboru:
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> Zadej oblast URI oboru bez schématu a hostitele:
>
> ```csharp
> options.ProviderOptions.DefaultScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

Další informace naleznete v tématu <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.

## <a name="imports-file"></a>Importuje soubor

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>Indexová stránka

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a>Součást aplikace

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>Komponenta RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>Komponenta LoginDisplay

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a>Ověřovací komponenta

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Další zdroje

* [Vyžádání dalších přístupových tokenů](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
