---
title: Zabezpečení Blazor samostatné aplikace ASP.NET Coreového sestavení pomocí Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 76bcac29d86a236e56c0eaea24a694c4845ecbcf
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083746"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory"></a>Zabezpečení Blazor samostatné aplikace ASP.NET Coreového sestavení pomocí Azure Active Directory

Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Vytvoření samostatné aplikace Blazorového sestavení, která pro ověřování používá [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) :

[Vytvoření TENANTA AAD a webové aplikace](/azure/active-directory/develop/v2-overview):

Zaregistrujte aplikaci AAD v **Azure Active Directory** > **Registrace aplikací** oblasti Azure Portal:

1. Zadejte **název** aplikace (například **Blazor AAD klienta**).
1. Vyberte **podporované typy účtů**. **Účty v tomto organizačním adresáři** můžete vybrat jenom pro toto prostředí.
1. Vynechejte rozevírací seznam **identifikátor URI přesměrování** nastavený na **Web**a zadejte identifikátor URI pro přesměrování `https://localhost:5001/authentication/login-callback`.
1. Zakažte **oprávnění** > oprávnění **správce udělit OpenID a offline_access oprávnění** .
1. Vyberte **Zaregistrovat**.

V > ověřování **Konfigurace platforem** > **webu**:

1. Potvrďte, že je k dispozici **identifikátor URI pro přesměrování** `https://localhost:5001/authentication/login-callback`.
1. V případě **implicitního udělení**zaškrtněte políčka pro **přístupové tokeny** a **tokeny ID**.
1. Zbývající výchozí hodnoty pro aplikaci jsou pro toto prostředí přijatelné.
1. Vyberte tlačítko **Uložit**.

Zaznamenejte následující informace:

* ID aplikace (ID klienta) (například `11111111-1111-1111-1111-111111111111`)
* ID adresáře (ID klienta) (například `22222222-2222-2222-2222-222222222222`)

Zástupné symboly v následujícím příkazu nahraďte dříve zaznamenanými informacemi a spusťte příkaz v příkazovém prostředí:

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

Chcete-li určit umístění výstupu, které vytvoří složku projektu, pokud neexistuje, zahrňte možnost výstup do příkazu s cestou (například `-o BlazorSample`). Název složky se také stal součástí názvu projektu.

## <a name="authentication-package"></a>Ověřovací balíček

Když je aplikace vytvořená tak, aby používala pracovní nebo školní účty (`SingleOrg`), aplikace automaticky obdrží odkaz na balíček pro [knihovnu Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`). Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.

Pokud se do aplikace přidává ověřování, přidejte balíček do souboru projektu aplikace ručně:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

V odkazu na předchozí balíček nahraďte `{VERSION}` verzí balíčku `Microsoft.AspNetCore.Blazor.Templates` zobrazeného v <xref:blazor/get-started> článku.

`Microsoft.Authentication.WebAssembly.Msal` balíček do aplikace přidá balíček `Microsoft.AspNetCore.Components.WebAssembly.Authentication`.

## <a name="authentication-service-support"></a>Podpora ověřovací služby

Podpora ověřování uživatelů je zaregistrovaná v kontejneru služby s metodou rozšíření `AddMsalAuthentication` poskytovanou balíčkem `Microsoft.Authentication.WebAssembly.Msal`. Tato metoda nastavuje všechny služby, které aplikace potřebuje k interakci s poskytovatelem identity (IP).

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
});
```

Metoda `AddMsalAuthentication` přijímá zpětné volání ke konfiguraci parametrů požadovaných k ověření aplikace. Hodnoty požadované pro konfiguraci aplikace lze získat z konfigurace AAD webu Azure Portal při registraci aplikace.

Šablona Blazor WebAssembly nekonfiguruje automatickou konfiguraci aplikace tak, aby požadovala přístupový token pro zabezpečené rozhraní API. Pokud chcete vytvořit token jako součást toku přihlášení, přidejte obor do výchozích oborů přístupových tokenů `MsalProviderOptions`:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}");
});
```

> [!NOTE]
> Výchozí rozsah přístupového tokenu musí být ve formátu `{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}` (například `11111111-1111-1111-1111-111111111111/API.Access`). Pokud se schéma nebo schéma a hostitel poskytne nastavení oboru (jak je znázorněno na webu Azure Portal), *klientská aplikace* vyvolá neošetřenou výjimku, pokud obdrží *neautorizovanou odpověď 401* od *aplikace API serveru*.

## <a name="index-page"></a>Indexová stránka

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

## <a name="app-component"></a>Součást aplikace

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>Komponenta RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>Komponenta LoginDisplay

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a>Součást ověřování

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Další zdroje

* <xref:security/authentication/azure-active-directory/index>
