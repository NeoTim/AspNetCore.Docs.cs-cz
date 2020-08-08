---
title: Nastavení externího přihlášení k účtu Microsoft pomocí ASP.NET Core
author: rick-anderson
description: Tato ukázka demonstruje integraci účet Microsoft ověřování uživatelů do existující aplikace ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/microsoft-logins
ms.openlocfilehash: ce0e56595611a8a34d0ad11680e67e2252da66c6
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020610"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a>Nastavení externího přihlášení k účtu Microsoft pomocí ASP.NET Core

Od [Valeriy Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)

V této ukázce se dozvíte, jak uživatelům povolit, aby se přihlásili pomocí své pracovní, školní nebo osobní účet Microsoft pomocí projektu ASP.NET Core 3,0 vytvořeného na [předchozí stránce](xref:security/authentication/social/index).

## <a name="create-the-app-in-microsoft-developer-portal"></a>Vytvoření aplikace na portálu Microsoftu pro vývojáře

* Do projektu přidejte balíček NuGet [Microsoft. AspNetCore. Authentication. MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) .
* Přejděte na stránku [Azure Portal-registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) a vytvořte nebo Přihlaste se ke účet Microsoft:

Pokud nemáte účet Microsoft, vyberte **vytvořit**. Po přihlášení budete přesměrováni na stránku **Registrace aplikací** :

* Vybrat **novou registraci**
* Zadejte **název**.
* Vyberte možnost pro **podporované typy účtů**.  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
* V části **identifikátor URI přesměrování**zadejte adresu URL pro vývoj s `/signin-microsoft` připojením. Například, `https://localhost:5001/signin-microsoft`. Schéma ověřování společnosti Microsoft nakonfigurované později v této ukázce bude automaticky zpracovávat požadavky na `/signin-microsoft` trase za účelem implementace toku OAuth.
* Vybrat **registraci**

### <a name="create-client-secret"></a>Vytvořit tajný klíč klienta

* V levém podokně vyberte **certifikáty & tajných**kódů.
* V části **tajné klíče klienta**vyberte **nový tajný klíč klienta** .

  * Přidejte popis pro tajný klíč klienta.
  * Vyberte tlačítko **Přidat** .

* V části **tajné klíče klienta**Zkopírujte hodnotu tajného klíče klienta.

Segment identifikátoru URI `/signin-microsoft` je nastaven jako výchozí zpětné volání poskytovatele ověřování společnosti Microsoft. Výchozí identifikátor URI zpětného volání můžete změnit během konfigurace middleware ověřování od společnosti Microsoft prostřednictvím zděděné vlastnosti [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) třídy [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) .

## <a name="store-the-microsoft-client-id-and-secret"></a>Uložte si ID a tajný kód klienta Microsoft.

Uložte citlivá nastavení, jako je ID klienta Microsoftu a tajné hodnoty pomocí [správce tajných](xref:security/app-secrets)kódů. V této ukázce použijte následující postup:

1. Inicializujte projekt pro tajné úložiště podle pokynů v tématu [Povolení tajného úložiště](xref:security/app-secrets#enable-secret-storage).
1. Uložte citlivá nastavení do místního úložiště tajných klíčů pomocí tajných klíčů `Authentication:Microsoft:ClientId` a `Authentication:Microsoft:ClientSecret` :

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a>Konfigurace ověřování účtu Microsoft

Přidejte službu účtu Microsoft do `Startup.ConfigureServices` :

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

Další informace o možnostech Konfigurace podporovaných ověřováním účtů Microsoft najdete v referenčních informacích k rozhraní [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API. Dá se použít k vyžádání různých informací o uživateli.

## <a name="sign-in-with-microsoft-account"></a>Účet Přihlásit se účtem Microsoft

Spusťte aplikaci a klikněte na **Přihlásit se**. Zobrazí se možnost přihlásit se s Microsoftem. Když kliknete na Microsoft, budete přesměrováni na Microsoft pro ověřování. Po přihlášení pomocí účtu Microsoft se zobrazí výzva, abyste aplikaci dali přístup k vašim informacím:

Klepněte na **Ano** a budete přesměrováni zpět na web, kde můžete nastavit e-mail.

Nyní jste přihlášeni pomocí přihlašovacích údajů Microsoftu:

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Řešení potíží

* Pokud vám poskytovatel účtu Microsoft přesměruje na chybovou stránku pro přihlášení, poznamenejte si parametry řetězce chyby a popis v parametrech řetězce dotazu přímo za `#` (hashtag) v identifikátoru URI.

  I když se chybová zpráva jeví jako problém s ověřováním Microsoftu, Nejběžnější příčinou je, že identifikátor URI vaší aplikace neodpovídá žádnému identifikátoru **URI přesměrování** , který je zadaný pro **webovou** platformu.
* Pokud Identity není nakonfigurováno voláním `services.AddIdentity` v `ConfigureServices` , pokus o ověření bude mít za následek *ArgumentException: je třeba zadat možnost SignInScheme*. Šablona projektu použitá v této ukázce zajišťuje, že je to hotové.
* Pokud se databáze lokality nevytvořila při použití prvotní migrace, při zpracování chyby žádosti se zobrazí *operace databáze* . Klepnutím na **použít migrace** vytvořte databázi a aktualizujte, aby pokračovala i po chybě.

## <a name="next-steps"></a>Další kroky

* Tento článek ukazuje, jak se dá ověřit u Microsoftu. Můžete postupovat podle podobného přístupu k ověřování u jiných poskytovatelů uvedených na [předchozí stránce](xref:security/authentication/social/index).

* Po publikování webu do webové aplikace Azure vytvořte nové tajné klíče klienta na portálu Microsoftu pro vývojáře.

* Nastavte `Authentication:Microsoft:ClientId` nastavení a `Authentication:Microsoft:ClientSecret` jako nastavení aplikace v Azure Portal. Konfigurační systém je nastaven na čtení klíčů z proměnných prostředí.
