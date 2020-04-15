---
title: Nastavení externího přihlášení účtu Microsoft s ASP.NET Core
author: rick-anderson
description: Tato ukázka ukazuje integraci ověřování uživatelů účtu Microsoft do existující aplikace ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 12c86456dad86731b86487a3a4dd725f36677002
ms.sourcegitcommit: f29a12486313e38e0163a643d8a97c8cecc7e871
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384043"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a>Nastavení externího přihlášení účtu Microsoft s ASP.NET Core

[Valerij Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)

V této ukázce se zobrazí, jak uživatelům umožnit přihlášení pomocí pracovního, školního nebo osobního účtu Microsoft pomocí projektu ASP.NET Core 3.0 vytvořeného na [předchozí stránce](xref:security/authentication/social/index).

## <a name="create-the-app-in-microsoft-developer-portal"></a>Vytvoření aplikace na portálu Microsoft Developer Portal

* Přidejte do projektu balíček [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet.
* Přejděte na stránku [Registrace aplikací Azure –](https://go.microsoft.com/fwlink/?linkid=2083908) registrace aplikací a vytvořte nebo přihlaste účet Microsoft:

Pokud nemáte účet Microsoft, vyberte **Vytvořit jeden**. Po přihlášení budete přesměrováni na stránku **Registrace aplikací:**

* Vybrat **novou registraci**
* Zadejte **název**.
* Vyberte možnost pro **podporované typy účtů**.  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
* V části **Přesměrování identifikátoru**URI `/signin-microsoft` zadejte adresu URL vývoje s připojeným. Například, `https://localhost:5001/signin-microsoft`. Schéma ověřování společnosti Microsoft nakonfigurované dále `/signin-microsoft` v této ukázce bude automaticky zpracovávat požadavky na cestě k implementaci toku OAuth.
* Vybrat **registr**

### <a name="create-client-secret"></a>Vytvořit tajný klíč klienta

* V levém podokně vyberte **certifikáty & tajných kódů**.
* V části **Tajné klíče klienta**vyberte **Nový tajný klíč klienta.**

  * Přidejte popis tajného klíče klienta.
  * Vyberte tlačítko **Přidat.**

* V části **Tajné klíče klienta**zkopírujte hodnotu tajného klíče klienta.

Segment `/signin-microsoft` URI je nastaven jako výchozí zpětné volání zprostředkovatele ověřování společnosti Microsoft. Výchozí identifikátor URI zpětného volání můžete změnit při konfiguraci middlewaru ověřování společnosti Microsoft prostřednictvím zděděné [vlastnosti RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) třídy [MicrosoftAccountOptions.](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions)

## <a name="store-the-microsoft-client-id-and-secret"></a>Uložení ID klienta Microsoftu a tajného klíče

Uklápěte citlivá nastavení, jako je ID klienta Microsoft a tajné hodnoty, pomocí [Správce tajných barev](xref:security/app-secrets). Pro tuto ukázku použijte následující kroky:

1. Inicializovat projekt pro tajné úložiště podle pokynů na [Povolit tajné úložiště](xref:security/app-secrets#enable-secret-storage).
1. Uložte citlivá nastavení v místním tajném úložišti s tajnými klíči `Authentication:Microsoft:ClientId` a `Authentication:Microsoft:ClientSecret`:

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a>Konfigurace ověřování účtu Microsoft

Přidejte službu Účet `Startup.ConfigureServices`Microsoft do :

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

Další informace o možnostech konfigurace podporovaných ověřováním účtu Microsoft najdete v tématu Odkaz na rozhraní [API MicrosoftAccountOptions.](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) To lze použít k vyžádání různých informací o uživateli.

## <a name="sign-in-with-microsoft-account"></a>Přihlášení pomocí účtu Microsoft

Spusťte aplikaci a klepněte na tlačítko **Přihlásit se**. Zobrazí se možnost přihlásit se pomocí společnosti Microsoft. Po kliknutí na společnost Microsoft budete přesměrováni na společnost Microsoft k ověření. Po přihlášení pomocí účtu Microsoft budete vyzváni k tomu, abyste aplikaci dovolili přístup k vašim údajům:

Klepněte na **Ano** a budete přesměrováni zpět na web, kde můžete nastavit e-mail.

Nyní jste přihlášeni pomocí pověření společnosti Microsoft:

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Řešení potíží

* Pokud vás poskytovatel účtu Microsoft přesměruje na přihlašovací chybovou stránku, poznamenejte `#` si parametry řetězce názvu chyby a popisu dotazu přímo za (hashtag) v uri.

  Přestože se zdá, že chybová zpráva označuje problém s ověřováním společnosti Microsoft, nejčastější příčinou je, že aplikace Uri neodpovídá žádné z **identifikátorů URI přesměrování** zadaných pro **webovou** platformu.
* Pokud identita není nakonfigurován `services.AddIdentity` `ConfigureServices`voláním , pokus o ověření bude mít za následek *ArgumentException: "SignInScheme" možnost musí být k dispozici*. Šablona projektu použitá v této ukázce zajišťuje, že se tak děje.
* Pokud databáze lokality nebyla vytvořena použitím počáteční migrace, zobrazí se *operace databáze, která se nezdařila při zpracování chyby požadavku.* Klepnutím na **Použít migrace vytvořte** databázi a aktualizujte, abyste pokračovali v minulosti za chybou.

## <a name="next-steps"></a>Další kroky

* Tento článek ukazuje, jak můžete ověřit s Microsoft. Můžete postupovat podobným způsobem k ověření s jinými zprostředkovateli uvedenými na [předchozí stránce](xref:security/authentication/social/index).

* Po publikování webu do webové aplikace Azure vytvořte nové tajné kódy klientů na portálu Microsoft Developer Portal.

* Nastavte `Authentication:Microsoft:ClientId` nastavení `Authentication:Microsoft:ClientSecret` aplikace a jako na webu Azure Portal. Konfigurační systém je nastaven pro čtení klíčů z proměnných prostředí.
