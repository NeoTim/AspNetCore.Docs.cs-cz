---
title: Zabezpečení samostatné aplikace Blazor ASP.NET Coreového sestavení pomocí Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 7d1031d3eac0e1d6790ca946809038127eb59a73
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111159"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a>Zabezpečení samostatné aplikace Blazor ASP.NET Coreového sestavení pomocí Azure Active Directory B2C

Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> Pokyny v tomto článku se týkají ASP.NET Core 3,2 Preview 4. Toto téma se bude aktualizovat na verzi Preview 5 v pátek, 24. dubna.

Chcete-li Blazor vytvořit samostatnou aplikaci WebAssembly, která pro ověřování používá [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) :

1. Při vytváření tenanta a registraci webové aplikace na webu Azure Portal postupujte podle pokynů v následujících tématech:

   * [Vytvořte tenanta](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; AAD B2C zaznamenejte následující informace:

     1 \. Instance AAD B2C (například `https://contoso.b2clogin.com/`, která zahrnuje koncové lomítko)<br>
     2 \. AAD B2C domény klienta (například `contoso.onmicrosoft.com`)

   * [Registrace webové aplikace](/azure/active-directory-b2c/tutorial-register-applications) &ndash; proveďte následující výběry během registrace aplikace:

     1 \. Nastavte **Web App/Web API** na **Ano**.<br>
     2 \. Nastavte možnost **povoluje implicitní tok** na **hodnotu Ano**.<br>
     3 \. Přidejte **adresu URL odpovědi** `https://localhost:5001/authentication/login-callback`.

     Poznamenejte si ID aplikace (ID klienta) (například `11111111-1111-1111-1111-111111111111`).

   * [Vytváření uživatelských toků](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; : vytvoření uživatelského toku pro registraci a přihlašování.

     Aby bylo možné naplnit `context.User.Identity.Name` `LoginDisplay` součást (*Shared/LoginDisplay. Razor*), vyberte alespoň atribut uživatele > **Zobrazovaný název** **deklarací identity aplikace**.

     Zaznamenejte si název uživatelského toku pro registraci a přihlašování vytvořený pro aplikaci (například `B2C_1_signupsignin`).

1. Zástupné symboly v následujícím příkazu nahraďte dříve zaznamenanými informacemi a spusťte příkaz v příkazovém prostředí:

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   Chcete-li určit umístění výstupu, které vytvoří složku projektu, pokud neexistuje, zahrňte možnost výstup do příkazu s cestou (například `-o BlazorSample`). Název složky se také stal součástí názvu projektu.

## <a name="authentication-package"></a>Ověřovací balíček

Když je aplikace vytvořená tak, aby používala individuální účet`IndividualB2C`B2C (), aplikace automaticky obdrží odkaz na balíček pro [knihovnu Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`). Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.

Pokud se do aplikace přidává ověřování, přidejte balíček do souboru projektu aplikace ručně:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Nahraďte `{VERSION}` odkazem na předchozí balíček verzí `Microsoft.AspNetCore.Blazor.Templates` balíčku, který je uvedený v <xref:blazor/get-started> článku.

`Microsoft.Authentication.WebAssembly.Msal` Balíček do této aplikace přidá `Microsoft.AspNetCore.Components.WebAssembly.Authentication` balíček.

## <a name="authentication-service-support"></a>Podpora ověřovací služby

Podpora ověřování uživatelů je registrovaná v kontejneru služby s metodou `AddMsalAuthentication` rozšíření poskytovanou `Microsoft.Authentication.WebAssembly.Msal` balíčkem. Tato metoda nastavuje všechny služby, které aplikace potřebuje k interakci s poskytovatelem identity (IP).

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
});
```

`AddMsalAuthentication` Metoda přijímá zpětné volání ke konfiguraci parametrů požadovaných k ověření aplikace. Hodnoty požadované pro konfiguraci aplikace lze získat z konfigurace AAD webu Azure Portal při registraci aplikace.

## <a name="access-token-scopes"></a>Obory přístupového tokenu

Blazor Šablona protokolu WebAssembly nekonfiguruje aplikaci automaticky pro vyžádání přístupového tokenu pro zabezpečené rozhraní API. Pokud chcete vytvořit token jako součást toku přihlašování, přidejte obor do výchozích oborů přístupových tokenů `MsalProviderOptions`:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
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
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

Další informace naleznete v tématu <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

## <a name="imports-file"></a>Importovat soubor

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>Indexová stránka

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a>Součást aplikace

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>Komponenta RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>Komponenta LoginDisplay

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a>Součást ověřování

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Další materiály a zdroje informací

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-ad-b2c>
* [Kurz: Vytvoření tenanta Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant)
* [Dokumentace k platformě Microsoft Identity Platform](/azure/active-directory/develop/)
