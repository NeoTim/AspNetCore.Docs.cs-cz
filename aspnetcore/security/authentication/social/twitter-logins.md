---
title: Nastavení externího přihlášení na Twitteru s ASP.NET Core
author: rick-anderson
description: Tento kurz ukazuje integraci ověřování uživatelů účtu Twitter do existující aplikace ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/twitter-logins
ms.openlocfilehash: 1f5d667e905e49ae05f5aa31bd5b69ad126f6e28
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277285"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a>Nastavení externího přihlášení na Twitteru s ASP.NET Core

[Valerij Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)

Tato ukázka ukazuje, jak umožnit uživatelům [přihlásit](https://dev.twitter.com/web/sign-in/desktop-browser) se pomocí svého účtu Twitter pomocí ukázkové ASP.NET projektu Core 3.0 vytvořeného na [předchozí stránce](xref:security/authentication/social/index).

## <a name="create-the-app-in-twitter"></a>Vytvoření aplikace na Twitteru

* Přidejte balíček [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet do projektu.

* Přejděte [https://apps.twitter.com/](https://apps.twitter.com/) na a přihlaste se. Pokud ještě nemáte účet na Twitteru, vytvořte ho pomocí odkazu **[Zaregistrovat nyní.](https://twitter.com/signup)**

* Vyberte **Vytvořit aplikaci**. Vyplňte **název aplikace**, **popis aplikace** a veřejný identifikátor **URI webu** (může to být dočasné, dokud název domény nezaregistrujete):

* Zaškrtněte políčko vedle **možnosti Povolit přihlášení na Twitteru.**

* Microsoft.AspNetCore.Identity vyžaduje, aby uživatelé měli ve výchozím nastavení e-mailovou adresu. Přejděte na kartu **Oprávnění,** klikněte na tlačítko **Upravit** a zaškrtněte políčko **vedle položky Požádat o e-mailovou adresu od uživatelů**.

* Zadejte identifikátor `/signin-twitter` URI vývoje s připojeným do pole **Adresy URL zpětného volání** (například: `https://webapp128.azurewebsites.net/signin-twitter`). Schéma ověřování Twitter nakonfigurované dále v `/signin-twitter` této ukázce bude automaticky zpracovávat požadavky na cestě k implementaci toku OAuth.

  > [!NOTE]
  > Segment `/signin-twitter` URI je nastaven jako výchozí zpětné volání poskytovatele ověřování na Twitteru. Výchozí identifikátor URI zpětného volání můžete změnit při konfiguraci middlewaru ověřování na Twitteru prostřednictvím zděděné [vlastnosti RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) třídy [TwitterOptions.](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions)

* Vyplňte zbytek formuláře a vyberte **Vytvořit**. Zobrazí se nové podrobnosti o aplikaci:

## <a name="store-the-twitter-consumer-api-key-and-secret"></a>Uložení klíče a tajného klíče a tajného klíče rozhraní API pro spotřebitele twitteru

Uklápěte citlivá nastavení, jako je například klíč twitterového spotřebitelského rozhraní API a tajný klíč, pomocí [správce tajných služeb](xref:security/app-secrets). Pro tuto ukázku použijte následující kroky:

1. Inicializovat projekt pro tajné úložiště podle pokynů na [Povolit tajné úložiště](xref:security/app-secrets#enable-secret-storage).
1. Uložte citlivá nastavení v místním tajném úložišti s klíči `Authentication:Twitter:ConsumerKey` tajných klíčů a `Authentication:Twitter:ConsumerSecret`:

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Tyto tokeny lze nalézt na kartě **Klíče a přístupové tokeny** po vytvoření nové aplikace Twitter:

## <a name="configure-twitter-authentication"></a>Konfigurace ověřování na Twitteru

Přidejte službu `ConfigureServices` Twitter do metody v *Startup.cs* souboru:

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Další informace o možnostech konfigurace podporovaných ověřováním na Twitteru naleznete v referenční příručce [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API. To lze použít k vyžádání různých informací o uživateli.

## <a name="sign-in-with-twitter"></a>Přihlaste se pomocí Twitteru

Spusťte aplikaci a **vyberte Přihlásit se**. Zobrazí se možnost přihlásit se pomocí Twitteru:

Kliknutím na **Twitter** přesměruje na Twitter pro ověření:

Po zadání pověření twitteru budete přesměrováni zpět na webovou stránku, kde můžete nastavit e-mail.

Nyní jste přihlášeni pomocí pověření Twitter:

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a>Řešení potíží

* **pouze ASP.NET Core 2.x:** Pokud identita není nakonfigurován `services.AddIdentity` `ConfigureServices`voláním , pokus o ověření bude mít za následek *ArgumentException: "SignInScheme" možnost musí být k dispozici*. Šablona projektu použitá v této ukázce zajišťuje, že se tak děje.
* Pokud databáze lokality nebyla vytvořena použitím počáteční migrace, zobrazí se *operace databáze, která se nezdařila při zpracování chyby požadavku.* Klepnutím na **Použít migrace vytvořte** databázi a aktualizujte, abyste pokračovali v minulosti za chybou.

## <a name="next-steps"></a>Další kroky

* Tento článek ukázal, jak se můžete ověřit pomocí Twitteru. Můžete postupovat podobným způsobem k ověření s jinými zprostředkovateli uvedenými na [předchozí stránce](xref:security/authentication/social/index).

* Po publikování webu do webové aplikace Azure byste `ConsumerSecret` měli obnovit na twitterovém vývojářském portálu.

* Nastavte `Authentication:Twitter:ConsumerKey` nastavení `Authentication:Twitter:ConsumerSecret` aplikace a jako na webu Azure Portal. Konfigurační systém je nastaven pro čtení klíčů z proměnných prostředí.
