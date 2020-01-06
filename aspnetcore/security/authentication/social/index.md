---
title: Ověřování pro Facebook, Google a externí poskytovatele v ASP.NET Core
author: rick-anderson
description: Tento kurz ukazuje, jak vytvořit aplikaci ASP.NET Core pomocí OAuth 2,0 s externími poskytovateli ověřování.
ms.author: riande
ms.custom: mvc
ms.date: 10/21/2019
uid: security/authentication/social/index
ms.openlocfilehash: 06ce9c72f43955345efb61afed2538158810005f
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75358066"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a>Ověřování pro Facebook, Google a externí poskytovatele v ASP.NET Core

Podle [Valeriy Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)

Tento kurz ukazuje, jak vytvořit aplikaci ASP.NET Core 3,0, která uživatelům umožňuje přihlásit se pomocí OAuth 2,0 s přihlašovacími údaji od externích zprostředkovatelů ověřování.

Poskytovatelé [Facebooku](xref:security/authentication/facebook-logins), [Twitteru](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins)a [Microsoftu](xref:security/authentication/microsoft-logins) jsou popsaná v následujících částech a používají počáteční projekt vytvořený v tomto článku. Jiní poskytovatelé jsou k dispozici v balíčcích třetích stran, jako jsou [ASPNET. Security. OAuth. Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) a [ASPNET. Security. OpenId. Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).

Povolení přihlášení uživatelů pomocí stávajících přihlašovacích údajů:

* Je vhodné pro uživatele.
* Posune mnoho složitých složitosti při správě procesu přihlašování na třetí stranu.

Příklady, jak mohou sociální přihlášení prosazovat přenosy a převody zákazníků, najdete v tématu případové studie na [Facebooku](https://www.facebook.com/unsupportedbrowser) a [Twitteru](https://dev.twitter.com/resources/case-studies).

## <a name="create-a-new-aspnet-core-project"></a>Vytvoření nového projektu ASP.NET Core

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Vytvořte nový projekt.
* Vyberte **ASP.NET Core webová aplikace** a **Další**.
* Zadejte **název projektu** a potvrďte nebo změňte **umístění**. Vyberte **vytvořit**.
* V rozevíracím seznamu vyberte **ASP.NET Core 3,0** a pak vyberte **Webová aplikace**.
* V části **ověřování**vyberte **změnit** a nastavte ověřování na **jednotlivé uživatelské účty**. Vyberte **OK**.
* V okně **vytvořit novou webovou aplikaci ASP.NET Core** vyberte **vytvořit**.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

* Otevřete terminál.  Pro Visual Studio Code můžete otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Změňte adresáře (`cd`) na složku, která bude obsahovat projekt.

* V případě Windows spusťte následující příkaz:

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual -uld
  ```

  Pro macOS a Linux spusťte následující příkaz:

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual
  ```

  * Příkaz `dotnet new` vytvoří nový projekt Razor Pages ve složce *WebApp1* .
  * `-au Individual` vytvoří kód pro individuální ověřování.
  * `-uld` používá LocalDB, zjednodušenou verzi SQL Server Express pro Windows. Vynechejte `-uld` pro použití SQLite.
  * Příkaz `code` otevře složku *WebApp1* v nové instanci Visual Studio Code.

---

## <a name="apply-migrations"></a>Použít migrace

* Spusťte aplikaci a vyberte odkaz **Registrovat** .
* Zadejte e-mail a heslo nového účtu a pak vyberte **zaregistrovat**.
* Při instalaci migrace postupujte podle pokynů.

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a>Ukládat tokeny přiřazené poskytovateli přihlášení pomocí SecretManager

Poskytovatelé sociálního přihlášení přidělují **ID aplikace** a tajné tokeny **aplikace** během procesu registrace. Přesné názvy tokenů se liší podle poskytovatele. Tyto tokeny reprezentují přihlašovací údaje, které aplikace používá pro přístup ke svým rozhraním API. Tokeny představují "tajné klíče", které lze propojit s konfigurací aplikace pomocí [správce tajných klíčů](xref:security/app-secrets#secret-manager). Správce tajných klíčů je bezpečnější alternativou pro ukládání tokenů do konfiguračního souboru, například *appSettings. JSON*.

> [!IMPORTANT]
> Správce tajných klíčů je jenom pro účely vývoje. Pomocí [poskytovatele konfigurace Azure Key Vault](xref:security/key-vault-configuration)můžete ukládat a chránit tajné kódy Azure test a produkčního prostředí.

Postupujte podle kroků v [bezpečném úložišti tajných kódů aplikací ve vývoji v tématu ASP.NET Core](xref:security/app-secrets) tématu uložení tokenů přiřazených každým poskytovatelem přihlašovacích údajů níže.

## <a name="setup-login-providers-required-by-your-application"></a>Nastavení poskytovatelů přihlášení požadovaných vaší aplikací

Pomocí následujících témat můžete nakonfigurovat aplikaci tak, aby používala příslušné poskytovatele:

* Pokyny pro [Facebook](xref:security/authentication/facebook-logins)
* Pokyny pro [Twitter](xref:security/authentication/twitter-logins)
* Pokyny pro [Google](xref:security/authentication/google-logins)
* Pokyny [Microsoftu](xref:security/authentication/microsoft-logins)
* [Další](xref:security/authentication/otherlogins) pokyny pro poskytovatele

[!INCLUDE[](includes/chain-auth-providers.md)]

## <a name="optionally-set-password"></a>Volitelně nastavit heslo

Když se zaregistrujete u externího poskytovatele přihlašovacích údajů, nemáte v aplikaci zaregistrovaným heslem. To vám pomůžeme při vytváření a zapamatování hesla pro web, ale také vám to závisí na externím poskytovateli přihlášení. Pokud externí poskytovatel přihlášení není dostupný, nebudete se moct přihlásit k webu.

Pokud chcete vytvořit heslo a přihlásit se pomocí e-mailu, který jste nastavili během procesu přihlašování pomocí externích zprostředkovatelů:

* Výběrem odkazu **hello &lt;email alias&gt;** v pravém horním rohu přejděte do zobrazení **Správa** .

![Správa zobrazení webové aplikace](index/_static/pass1a.png)

* Vyberte **Vytvořit**.

![Nastavení stránky pro heslo](index/_static/pass2a.png)

* Nastavte platné heslo a můžete ho použít k přihlášení pomocí e-mailu.

## <a name="next-steps"></a>Další kroky

* Informace o přizpůsobení přihlašovacích tlačítek najdete v [tomto problému GitHubu](https://github.com/aspnet/AspNetCore.Docs/issues/10563) .
* Tento článek představil externí ověřování a vysvětluje požadavky potřebné k přidání externích přihlášení do vaší aplikace ASP.NET Core.
* Stránky specifické pro poskytovatele odkazů pro konfiguraci přihlášení pro poskytovatele vyžadované vaší aplikací
* Můžete chtít zachovat další data o uživateli a jejich přístup a aktualizovat tokeny. Další informace najdete v tématu <xref:security/authentication/social/additional-claims>.
