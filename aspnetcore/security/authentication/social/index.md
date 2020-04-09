---
title: Ověřování na Facebooku, Googlu a externím poskytovateli v ASP.NET Core
author: rick-anderson
description: Tento kurz ukazuje, jak vytvořit aplikaci ASP.NET Core pomocí OAuth 2.0 s externími poskytovateli ověřování.
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
uid: security/authentication/social/index
ms.openlocfilehash: c698edbd85d665509366287b1dcad08e276e71cc
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78668040"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a>Ověřování na Facebooku, Googlu a externím poskytovateli v ASP.NET Core

[Valerij Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)

Tento kurz ukazuje, jak vytvořit ASP.NET aplikaci Core 3.0, která umožňuje uživatelům přihlásit pomocí OAuth 2.0 s pověřeními od externích poskytovatelů ověřování.

[Poskytovatelé Facebooku](xref:security/authentication/facebook-logins), [Twitteru](xref:security/authentication/twitter-logins), [Googlu](xref:security/authentication/google-logins)a [Microsoftu](xref:security/authentication/microsoft-logins) jsou zahrnuti v následujících částech a používají počáteční projekt vytvořený v tomto článku. Další poskytovatelé jsou k dispozici v balíčcích třetích stran, jako je [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) a [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).

Povolení uživatelům přihlásit se pomocí jejich stávajících přihlašovacích údajů:

* Je vhodný pro uživatele.
* Přesune mnoho složitostí správy procesu přihlášení na třetí stranu.

Příklady toho, jak mohou přihlášení na sociální chod řídit návštěvnost a konverze zákazníků, najdete v tématu případové studie společností [Facebook](https://www.facebook.com/unsupportedbrowser) a [Twitter](https://dev.twitter.com/resources/case-studies).

## <a name="create-a-new-aspnet-core-project"></a>Vytvoření nového ASP.NET základního projektu

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Vytvoření nového projektu
* Vyberte **ASP.NET základní webovou aplikaci** a **další**.
* Zadejte **název projektu** a potvrďte nebo změňte **umístění**. Vyberte **Vytvořit**.
* V rozevíracím souboru **(ASP.NET Jádrem {X.Y}** vyberte nejnovější verzi ASP.NET jádra a pak vyberte **Webová aplikace**.
* V části **Ověřování**vyberte **Změnit** a nastavte ověřování na **jednotlivé uživatelské účty**. Vyberte **OK**.
* V okně **Vytvořit novou ASP.NET základní webovou aplikaci** vyberte **Vytvořit**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

* Otevřete terminál.  Pro Visual Studio Code můžete otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Změňte adresáře (`cd`) na složku, která bude projekt obsahovat.

* V případě Windows spusťte následující příkaz:

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual -uld
  ```

  Pro macOS a Linux spusťte následující příkaz:

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual
  ```

  * Příkaz `dotnet new` vytvoří nový projekt Razor Pages ve složce *WebApp1.*
  * `-au Individual`vytvoří kód pro individuální ověřování.
  * `-uld`používá LocalDB, odlehčenou verzi SQL Server Express pro Windows. Vynechat `-uld` použití SQLite.
  * Příkaz `code` otevře složku *WebApp1* v nové instanci kódu sady Visual Studio.

---

## <a name="apply-migrations"></a>Použití migrace

* Spusťte aplikaci a vyberte odkaz **Registrovat.**
* Zadejte e-mail a heslo pro nový účet a pak vyberte **Registrovat**.
* Podle pokynů použijte migrace.

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a>Použití nástroje SecretManager k ukládání tokenů přiřazených poskytovateli přihlášení

Zprostředkovatelé přihlášení **na sociální chod** přiřazují tokeny Id aplikace a **tajných aplikací** během procesu registrace. Přesné názvy tokenů se liší podle zprostředkovatele. Tyto tokeny představují přihlašovací údaje, které vaše aplikace používá pro přístup k jejich rozhraní API. Tokeny představují "tajné klíče", které lze propojit s konfigurací aplikace pomocí [Správce tajných barev](xref:security/app-secrets#secret-manager). Správce tajných barev je bezpečnější alternativou k ukládání tokenů v konfiguračním souboru, například *appsettings.json*.

> [!IMPORTANT]
> Tajný správce je pouze pro účely vývoje. Tajné klíče azure test a produkční verze můžete ukládat a chránit pomocí [zprostředkovatele konfigurace azure key vault](xref:security/key-vault-configuration).

Postupujte podle kroků v [části Bezpečné ukládání tajných kódů aplikací ve vývoji v tématu ASP.NET Core](xref:security/app-secrets) pro ukládání tokenů přiřazených jednotlivými poskytovateli přihlášení níže.

## <a name="setup-login-providers-required-by-your-application"></a>Nastavení zprostředkovatelů přihlášení vyžadovaných vaší aplikací

Ke konfiguraci aplikace pro použití příslušných zprostředkovatelů použijte následující témata:

* [Pokyny k Facebooku](xref:security/authentication/facebook-logins)
* [Twitter](xref:security/authentication/twitter-logins) návod
* [Pokyny k Googlu](xref:security/authentication/google-logins)
* [Pokyny společnosti Microsoft](xref:security/authentication/microsoft-logins)
* [Další](xref:security/authentication/otherlogins) pokyny poskytovatele

[!INCLUDE[](includes/chain-auth-providers.md)]

## <a name="optionally-set-password"></a>Volitelně nastavit heslo

Když se zaregistrujete u externího poskytovatele přihlašovacích údajů, nemáte heslo registrované v aplikaci. To zmírňuje vytváření a zapamatování hesla pro web, ale také vás činí závislými na externím poskytovateli přihlášení. Pokud externí poskytovatel přihlášení není k dispozici, nebudete se moci přihlásit k webu.

Vytvoření hesla a přihlášení pomocí e-mailu, který jste nastavili během procesu přihlášení u externích poskytovatelů:

* Vyberte odkaz **Dobrý e-mailový &lt;alias&gt; ** v pravém horním rohu a přejděte do zobrazení **Spravovat.**

![Webová aplikace Spravovat zobrazení](index/_static/pass1a.png)

* Vybrat **vytvořit**

![Nastavení stránky s heslem](index/_static/pass2a.png)

* Nastavte platné heslo a můžete se pomocí tohoto nastavení přihlásit pomocí e-mailu.

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak přizpůsobit přihlašovací tlačítka, najdete v tomto problému s [GitHubem.](https://github.com/dotnet/AspNetCore.Docs/issues/10563)
* Tento článek zavedl externí ověřování a vysvětlil požadavky potřebné k přidání externích přihlášení do aplikace ASP.NET Core.
* Odkaz na stránky specifické pro zprostředkovatele pro konfiguraci přihlášení pro zprostředkovatele vyžadované vaší aplikací.
* Můžete chtít zachovat další data o uživateli a jeho přístup a aktualizovat tokeny. Další informace naleznete v tématu <xref:security/authentication/social/additional-claims>.
