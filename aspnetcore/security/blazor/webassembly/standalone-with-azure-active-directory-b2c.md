---
title: Zabezpečení samostatné Blazor aplikace ASP.NET Core WebAssembly pomocí služby Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 96e39a4c975a65fd11776f774fb1799acab525b9
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123460"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a>Zabezpečení samostatné Blazor aplikace ASP.NET Core WebAssembly pomocí služby Azure Active Directory B2C

[Javier Calvarro Nelson](https://github.com/javiercn) a Luke [Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Vytvoření samostatné Blazor aplikace WebAssembly, která používá pro ověřování [B2C služby Azure Active Directory (AAD):](/azure/active-directory-b2c/overview)

1. Podle pokynů v následujících tématech vytvořte klienta a zaregistrujte webovou aplikaci na webu Azure Portal:

   * &ndash; [Vytvořte klienta AAD B2C](/azure/active-directory-b2c/tutorial-create-tenant) Zaznamenejte následující informace:

     1\. Instance AAD B2C (například `https://contoso.b2clogin.com/`, která zahrnuje koncové lomítko)<br>
     2\. Doména klienta AAD B2C (například `contoso.onmicrosoft.com`)

   * [Zaregistrujte webovou aplikaci](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Při registraci aplikace proveďte následující výběry:

     1\. Nastavte **webové aplikace / webové rozhraní API** na **ano**.<br>
     2\. Nastavit **povolit implicitní tok** na **Ano**.<br>
     3\. Přidejte adresu `https://localhost:5001/authentication/login-callback`URL **odpovědi** společnosti .

     Zaznamenejte ID aplikace (ID `11111111-1111-1111-1111-111111111111`klienta) (například).

   * [Vytvoření toků](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; uživatelů: Vytvoření toku registrace a přihlášení uživatele.

     Vyberte minimálně uživatelský atribut**Zobrazení názvu** **aplikace,** >  `LoginDisplay` chcete-li `context.User.Identity.Name` naplnit v komponentě (*Shared/LoginDisplay.razor).*

     Zaznamenejte název uživatelského toku registrace a přihlášení `B2C_1_signupsignin`vytvořený pro aplikaci (například).

1. Nahraďte zástupné symboly v následujícím příkazu informacemi zaznamenanými dříve a proveďte příkaz v příkazovém prostředí:

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   Chcete-li určit výstupní umístění, které vytvoří složku projektu, pokud neexistuje, zahrňte do `-o BlazorSample`příkazu možnost výstupu s cestou (například ). Název složky se také stane součástí názvu projektu.

## <a name="authentication-package"></a>Ověřovací balíček

Když je aplikace vytvořena pro použití individuálního`IndividualB2C`účtu B2C ( ), aplikace automaticky`Microsoft.Authentication.WebAssembly.Msal`obdrží odkaz na balíček pro [knihovnu Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ( ). Balíček obsahuje sadu primitiv, které pomáhají aplikaci ověřovat uživatele a získat tokeny pro volání chráněných api.

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
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
});
```

Metoda `AddMsalAuthentication` přijímá zpětné volání ke konfiguraci parametrů potřebných k ověření aplikace. Hodnoty potřebné pro konfiguraci aplikace lze získat z konfigurace Azure Portal AAD při registraci aplikace.

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

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Další zdroje

* [Vyžádání dalších přístupových tokenů](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-ad-b2c>
* [Kurz: Vytvoření tenanta Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant)
* [Dokumentace k platformě Microsoft Identity Platform](/azure/active-directory/develop/)
