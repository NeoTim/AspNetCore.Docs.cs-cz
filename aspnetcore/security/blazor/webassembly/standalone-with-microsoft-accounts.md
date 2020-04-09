---
title: Zabezpečení samostatné Blazor aplikace ASP.NET Core WebAssembly s účty Microsoft
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 8c409651b3338c2baeae497bef43b994823a20f9
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977077"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a>Zabezpečení samostatné Blazor aplikace ASP.NET Core WebAssembly s účty Microsoft

[Javier Calvarro Nelson](https://github.com/javiercn) a Luke [Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Vytvoření samostatné Blazor aplikace WebAssembly, která používá [účty Microsoft s Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) pro ověřování:

1. [Vytvoření klienta AAD a webové aplikace](/azure/active-directory/develop/v2-overview)

   Zaregistrujte aplikaci AAD v oblasti**registrace aplikací** Azure **Active Directory** > na webu Azure Portal:

   1\. Zadejte **název** aplikace (například ** Blazor AAD klienta).**<br>
   2\. V **části Podporované typy účtů**vyberte možnost Účty v **libovolném organizačním adresáři**.<br>
   3\. Ponechejte rozevírací soubor **URI přesměrování** nastavený `https://localhost:5001/authentication/login-callback`na **web**a poskytněte identifikátor URI přesměrování aplikace .<br>
   4\. Zamítejte**zaškrtávací** políčko **Oprávnění** > Udělit oprávnění k otevření a offline_access oprávnění.<br>
   5\. Vyberte **Zaregistrovat**.

   V**konfiguracích platformy** >  **ověřování** > **web**:

   1\. Potvrďte, že `https://localhost:5001/authentication/login-callback` je přítomen **identifikátor URI přesměrování.**<br>
   2\. V **případě implicitního udělení**zaškrtněte políčka u **tokenů přístupu** a **tokenů ID**.<br>
   3\. Zbývající výchozí hodnoty pro aplikaci jsou přijatelné pro toto prostředí.<br>
   4\. Vyberte tlačítko **Uložit**.

   Zaznamenejte ID aplikace (ID `11111111-1111-1111-1111-111111111111`klienta) (například).

1. Nahraďte zástupné symboly v následujícím příkazu informacemi zaznamenanými dříve a proveďte příkaz v příkazovém prostředí:

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   Chcete-li určit výstupní umístění, které vytvoří složku projektu, pokud neexistuje, zahrňte do `-o BlazorSample`příkazu možnost výstupu s cestou (například ). Název složky se také stane součástí názvu projektu.

Po vytvoření aplikace byste měli být schopni:

* Přihlaste se do aplikace pomocí účtu Microsoft.
* Požádejte o přístupové tokeny pro rozhraní API Blazor Microsoft u stejný chod jako u samostatných aplikací za předpokladu, že jste aplikaci nakonfigurovali správně. Další informace naleznete v [tématu Úvodní příručka: Konfigurace aplikace pro vystavení webových rozhraní API](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).

## <a name="authentication-package"></a>Ověřovací balíček

Když je aplikace vytvořena pro použití`SingleOrg`pracovních nebo školních účtů ( ), aplikace`Microsoft.Authentication.WebAssembly.Msal`automaticky obdrží odkaz na balíček pro [knihovnu Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ( ). Balíček obsahuje sadu primitiv, které pomáhají aplikaci ověřovat uživatele a získat tokeny pro volání chráněných api.

Pokud přidáváte ověřování do aplikace, přidejte balíček ručně do souboru projektu aplikace:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Nahraďte `{VERSION}` v předchozím odkazu na `Microsoft.AspNetCore.Blazor.Templates` balíček verzí <xref:blazor/get-started> balíčku uvedenou v článku.

Balíček `Microsoft.Authentication.WebAssembly.Msal` transitively `Microsoft.AspNetCore.Components.WebAssembly.Authentication` přidá balíček do aplikace.

## <a name="authentication-service-support"></a>Podpora ověřovací služby

Podpora pro ověřování uživatelů je registrována v `AddMsalAuthentication` kontejneru služby `Microsoft.Authentication.WebAssembly.Msal` s metodou rozšíření poskytované balíček. Tato metoda nastaví všechny služby potřebné pro interakci aplikace s poskytovatelem identity (IP).

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

Metoda `AddMsalAuthentication` přijímá zpětné volání ke konfiguraci parametrů potřebných k ověření aplikace. Hodnoty potřebné pro konfiguraci aplikace lze získat z konfigurace účtů Microsoft při registraci aplikace.

## <a name="access-token-scopes"></a>Obory přístupových tokenů

Šablona Blazor WebAssembly automaticky nekonfiguruje aplikaci tak, aby požadovala přístupový token pro zabezpečené rozhraní API. Chcete-li zřídit token jako součást toku přihlášení, přidejte obor do `MsalProviderOptions`výchozího oboru přístupového tokenu :

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
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
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

Další informace naleznete v tématu <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.

## <a name="imports-file"></a>Importuje soubor

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>Indexová stránka

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

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
* [Úvodní příručka: Registrace aplikace s platformou microsoftu pro identity](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [Rychlý start: Konfigurace aplikace pro vystavení webových rozhraní API](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
