---
title: Podpora služby IIS při vývoji v sadě Visual Studio pro ASP.NET Core
author: guardrex
description: Podívejte se na podporu pro ladění aplikací ASP.NET Core, když se službou IIS a systémem Windows Server.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2019
uid: host-and-deploy/iis/development-time-iis-support
ms.openlocfilehash: 6f555858239b4432d252f8b3ac7add5c3e8bfe62
ms.sourcegitcommit: 258a97159da206f9009f23fdf6f8fa32f178e50b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2019
ms.locfileid: "59425098"
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a>Podpora služby IIS při vývoji v sadě Visual Studio pro ASP.NET Core

Podle [Sourabh Shirhatti](https://twitter.com/sshirhatti) a [Luke Latham](https://github.com/guardrex)

Tento článek popisuje [sady Visual Studio](https://www.visualstudio.com/vs/) podporuje ladění aplikací ASP.NET Core se službou IIS a systémem Windows Server. Toto téma vás provede povolením tento scénář a nastavení projektu.

## <a name="prerequisites"></a>Požadavky

* [Visual Studio pro Windows](https://visualstudio.microsoft.com/downloads/)
* **Vývoj pro ASP.NET a web** pracovního vytížení
* **Vývoj pro různé platformy .NET core** pracovního vytížení
* Certifikát X.509 zabezpečení (pro podporu protokolu HTTPS)

## <a name="enable-iis"></a>Povolte službu IIS

1. Ve Windows, přejděte na **ovládací panely** > **programy** > **programy a funkce** > **zapnout Windows zapnout nebo vypnout funkce** (levé straně obrazovky).
1. Vyberte **Internetová informační služba** zaškrtávací políčko. Vyberte **OK**.

Instalace služby IIS může vyžadovat restartování systému.

## <a name="configure-iis"></a>Konfigurace služby IIS

Služba IIS musí mít web nakonfigurované tyto parametry:

* **Název hostitele** &ndash; obvykle **výchozí webový server** se používá s **název hostitele** z `localhost`. Libovolný platný web služby IIS s názvem hostitele však funguje.
* **Vazby webu**
  * Pro aplikace, které vyžadují protokol HTTPS vytvořte vazbu na port 443 pomocí certifikátu. Obvykle **služby IIS Express vývojářský certifikát, kterým** se používá, ale libovolný platný certifikát funguje.
  * Pro aplikace, které používají protokol HTTP, zkontrolujte existenci vazbu na příspěvek 80 nebo vytvořit vazbu na port 80 pro nový web.
  * Použití jedné vazby pro protokol HTTP nebo HTTPS. **Vytvoření vazby na portech HTTP a HTTPS současně není podporováno.**

## <a name="enable-development-time-iis-support-in-visual-studio"></a>Povolit podporu služby IIS dobu vývoje v sadě Visual Studio

1. Spusťte instalační program sady Visual Studio.
1. Vyberte **změnit** pro instalaci sady Visual Studio, který chcete použít pro podporu vývoje služby IIS.
1. Pro **vývoj pro ASP.NET a web** pracovního vytížení, vyhledejte a nainstalujte **dobu vývoje podpora služby IIS** komponenty.

   Součást je uveden v **volitelné** části **dobu vývoje podpora služby IIS** v **podrobné informace o instalaci** panelu napravo od úlohy. Nainstaluje komponenty [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module), což je nativní modul IIS potřebné ke spuštění aplikace ASP.NET Core se službou IIS.

## <a name="configure-the-project"></a>Konfigurace projektu

### <a name="https-redirection"></a>Přesměrování protokolu HTTPS

Nový projekt, který vyžaduje protokol HTTPS, zaškrtněte políčko pro **konfigurace pro protokol HTTPS** v **vytvořit novou webovou aplikaci ASP.NET Core** okna. Zaškrtnutím políčka přidá [přesměrování protokolu HTTPS a Middlewarem HSTS](xref:security/enforcing-ssl) do aplikace při jeho vytvoření.

Pro existující projekt, který vyžaduje protokol HTTPS, použijte přesměrování protokolu HTTPS a HSTS middlewaru v `Startup.Configure`. Další informace naleznete v tématu <xref:security/enforcing-ssl>.

V projektu, který používá protokol HTTP [přesměrování protokolu HTTPS a Middlewarem HSTS](xref:security/enforcing-ssl) nejsou přidány do aplikace. Není nutná žádná konfigurace aplikace.

### <a name="iis-launch-profile"></a>Profil spuštění služby IIS

Vytvoření nového profilu spuštění přidává dobu vývoje služby IIS:

::: moniker range=">= aspnetcore-3.0"

1. Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení**. Vyberte **vlastnosti**. Otevřít **ladění** kartu.
1. Pro **profilu**, vyberte **nový** tlačítko. Název profilu "IIS" v automaticky otevíraném okně. Vyberte **OK** vytvořte profil.
1. Pro **spuštění** vyberte **IIS** ze seznamu.
1. Zaškrtněte políčko pro **spuštění prohlížeče** a zadejte adresu URL koncového bodu.

   Pokud aplikace vyžaduje protokol HTTPS, použijte koncový bod HTTPS (`https://`). Pro protokol HTTP, používat HTTP (`http://`) koncového bodu.

   Zadejte stejný název hostitele a portu, jako [zadána konfigurace služby IIS používá starší](#configure-iis), obvykle `localhost`.

   Zadejte název aplikace na konci adresy URL.

   Například `https://localhost/WebApplication1` (HTTPS) nebo `http://localhost/WebApplication1` (HTTP) jsou platný koncový bod adresy URL.
1. V **proměnné prostředí** vyberte **přidat** tlačítko. Zadat proměnné prostředí s **název** z `ASPNETCORE_ENVIRONMENT` a **hodnotu** z `Development`.
1. V **nastavení webového serveru** nastavení oblasti **adresa URL aplikace** na stejnou hodnotu pro **spuštění prohlížeče** adresu URL koncového bodu.
1. Pro **Model hostování** nastavení v aplikaci Visual Studio 2019 nebo novější, vyberte **výchozí** používat model hostingu používané v projektu. Pokud je projekt nastaví `<AspNetCoreHostingModel>` vlastnost v souboru projektu, hodnota vlastnosti (`InProcess` nebo `OutOfProcess`) se používá. Pokud vlastnost není k dispozici, použije se výchozí hostování modelu aplikace, což je v procesu. Pokud aplikace vyžaduje explicitní model hostingu nastavení liší od normální model hostingu aplikace, nastavte **Model hostování** buď `In Process` nebo `Out Of Process` podle potřeby.
1. Uložte profil.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

1. Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení**. Vyberte **vlastnosti**. Otevřít **ladění** kartu.
1. Pro **profilu**, vyberte **nový** tlačítko. Název profilu "IIS" v automaticky otevíraném okně. Vyberte **OK** vytvořte profil.
1. Pro **spuštění** vyberte **IIS** ze seznamu.
1. Zaškrtněte políčko pro **spuštění prohlížeče** a zadejte adresu URL koncového bodu.

   Pokud aplikace vyžaduje protokol HTTPS, použijte koncový bod HTTPS (`https://`). Pro protokol HTTP, používat HTTP (`http://`) koncového bodu.

   Zadejte stejný název hostitele a portu, jako [zadána konfigurace služby IIS používá starší](#configure-iis), obvykle `localhost`.

   Zadejte název aplikace na konci adresy URL.

   Například `https://localhost/WebApplication1` (HTTPS) nebo `http://localhost/WebApplication1` (HTTP) jsou platný koncový bod adresy URL.
1. V **proměnné prostředí** vyberte **přidat** tlačítko. Zadat proměnné prostředí s **název** z `ASPNETCORE_ENVIRONMENT` a **hodnotu** z `Development`.
1. V **nastavení webového serveru** nastavení oblasti **adresa URL aplikace** na stejnou hodnotu pro **spuštění prohlížeče** adresu URL koncového bodu.
1. Pro **Model hostování** nastavení v aplikaci Visual Studio 2019 nebo novější, vyberte **výchozí** používat model hostingu používané v projektu. Pokud je projekt nastaví `<AspNetCoreHostingModel>` vlastnost v souboru projektu, hodnota vlastnosti (`InProcess` nebo `OutOfProcess`) se používá. Pokud vlastnost není k dispozici, použije se výchozí hostování modelu aplikace, což je mimo proces. Pokud aplikace vyžaduje explicitní model hostingu nastavení liší od normální model hostingu aplikace, nastavte **Model hostování** buď `In Process` nebo `Out Of Process` podle potřeby.
1. Uložte profil.

::: moniker-end

Pokud není pomocí sady Visual Studio, ručně přidat profil spuštění, který [launchSettings.json](http://json.schemastore.org/launchsettings) ve *vlastnosti* složky. Následující příklad nastaví profil, který chcete používat protokol HTTPS:

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

Ujistěte se, že `applicationUrl` a `launchUrl` koncové body odpovídat a používají stejný protokol jako konfigurace vazby služby IIS, HTTP nebo HTTPS.

## <a name="run-the-project"></a>Spusťte projekt

Spuštění sady Visual Studio jako správce:

* Potvrďte, že je nastavena rozevíracím seznamu konfigurací sestavení **ladění**.
* Nastavte na tlačítko Spustit **IIS** profilu a klikněte na tlačítko a spusťte aplikaci.

Visual Studio může výzvu restartování v případě není spuštěn jako správce. Pokud se zobrazí výzva, restartujte aplikaci Visual Studio.

Pokud se používá nedůvěryhodný vývojářský certifikát, prohlížeče mohou vyžadovat vytvořte výjimku pro nedůvěryhodný certifikát.

> [!NOTE]
> Ladění verze sestavení konfigurace s [pouze můj kód](/visualstudio/debugger/just-my-code) a optimalizace kompilátoru za následek sníženými možnostmi. Například nejsou přístupů bodech rozdělení.

## <a name="additional-resources"></a>Další zdroje

* [Začínáme se službou Správce služby IIS ve službě IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [Hostitele ASP.NET Core ve Windows se službou IIS](xref:host-and-deploy/iis/index)
* [Úvod k modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module)
* [Referenční informace o ASP.NET Core modulu Konfigurace](xref:host-and-deploy/aspnet-core-module)
* [Vynucení protokolu HTTPS](xref:security/enforcing-ssl)
