---
title: Zabezpečení samostatné aplikace Blazor ASP.NET Coreového sestavení pomocí účtů Microsoft
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: a12cc8f94a97882e4a0ac3a6553628df4da2e82c
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111185"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a>Zabezpečení samostatné aplikace Blazor ASP.NET Coreového sestavení pomocí účtů Microsoft

Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> Pokyny v tomto článku se týkají ASP.NET Core 3,2 Preview 4. Toto téma se bude aktualizovat na verzi Preview 5 v pátek, 24. dubna.

Vytvoření Blazor samostatné aplikace WebAssembly, která používá [účty Microsoft s Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) pro ověřování:

1. [Vytvoření tenanta a webové aplikace AAD](/azure/active-directory/develop/v2-overview)

   Zaregistrujte aplikaci AAD v **Azure Active Directory** > **Registrace aplikací** oblasti Azure Portal:

   1 \. Zadejte **název** aplikace (například ** Blazor AAD klienta**).<br>
   2 \. V **podporovaných typech účtů**vyberte **účty v libovolném organizačním adresáři**.<br>
   3 \. Vynechejte rozevírací seznam **identifikátor URI přesměrování** nastavený na **Web**a zadejte identifikátor URI pro `https://localhost:5001/authentication/login-callback`přesměrování.<br>
   4 \. Zakažte **oprávnění** > **udělit správcům oprávnění k OpenID a offline_access** .<br>
   5 \. Vyberte **Zaregistrovat**.

   Na webu**Konfigurace** >  **ověřovacích** > platforem**Web**:

   1 \. Ověřte, zda `https://localhost:5001/authentication/login-callback` je **identifikátor URI přesměrování** k dispozici.<br>
   2 \. V případě **implicitního udělení**zaškrtněte políčka pro **přístupové tokeny** a **tokeny ID**.<br>
   3 \. Zbývající výchozí hodnoty pro aplikaci jsou pro toto prostředí přijatelné.<br>
   4 \. Vyberte tlačítko **Uložit**.

   Poznamenejte si ID aplikace (ID klienta) (například `11111111-1111-1111-1111-111111111111`).

1. Zástupné symboly v následujícím příkazu nahraďte dříve zaznamenanými informacemi a spusťte příkaz v příkazovém prostředí:

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   Chcete-li určit umístění výstupu, které vytvoří složku projektu, pokud neexistuje, zahrňte možnost výstup do příkazu s cestou (například `-o BlazorSample`). Název složky se také stal součástí názvu projektu.

Po vytvoření aplikace byste měli mít tyto možnosti:

* Přihlaste se k aplikaci pomocí účtu Microsoft.
* Vyžádejte si přístupové tokeny pro rozhraní API Microsoftu stejným způsobem jako Blazor u samostatných aplikací za předpokladu, že jste aplikaci správně nakonfigurovali. Další informace najdete v tématu [rychlý Start: Konfigurace aplikace k vystavování webových rozhraní API](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).

## <a name="authentication-package"></a>Ověřovací balíček

Když je aplikace vytvořená tak, aby používala pracovní nebo`SingleOrg`školní účty (), aplikace automaticky obdrží odkaz na balíček pro [knihovnu Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`). Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.

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
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

`AddMsalAuthentication` Metoda přijímá zpětné volání ke konfiguraci parametrů požadovaných k ověření aplikace. Hodnoty požadované pro konfiguraci aplikace lze získat z konfigurace účtů Microsoft při registraci aplikace.

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

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Další materiály a zdroje informací

* <xref:security/blazor/webassembly/additional-scenarios>
* [Rychlý Start: registrace aplikace s platformou Microsoft identity](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [Rychlý Start: Konfigurace aplikace k vystavení webových rozhraní API](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
