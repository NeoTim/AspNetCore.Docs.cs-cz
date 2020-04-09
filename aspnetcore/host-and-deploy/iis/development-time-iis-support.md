---
title: Podpora služby IIS při vývoji v sadě Visual Studio pro ASP.NET Core
author: rick-anderson
description: Objevte podporu pro ladění aplikací ASP.NET Core při spuštění služby IIS ve Windows Serveru.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/iis/development-time-iis-support
ms.openlocfilehash: f87a1d8cf41248f14932908c0633f98a7198853f
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664043"
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a>Podpora služby IIS při vývoji v sadě Visual Studio pro ASP.NET Core

Podle [Sourabh Shirhatti](https://twitter.com/sshirhatti)

::: moniker range=">= aspnetcore-3.0"

Tento článek popisuje podporu [sady Visual Studio](https://visualstudio.microsoft.com) pro ladění aplikací ASP.NET Core spuštěných se službou IIS v systému Windows Server. Toto téma vás provede povolením tohoto scénáře a nastavením projektu.

## <a name="prerequisites"></a>Požadavky

* [Visual Studio pro Windows](https://visualstudio.microsoft.com/downloads/)
* **ASP.NET a pracovní zátěž pro vývoj webových** aplikací
* Pracovní **vytížení napříč platformami .NET Core**
* Certifikát zabezpečení X.509 (pro podporu HTTPS)

## <a name="enable-iis"></a>Povolení iis

1. V systému Windows přejděte na **Ovládací panely** > **Programy** > **a funkce** > **Zapněte nebo vypněte funkce systému Windows** (levá strana obrazovky).
1. Zaškrtněte políčko **Internetová informační služba.** Vyberte **OK**.

Instalace služby IIS může vyžadovat restartování systému.

## <a name="configure-iis"></a>Konfigurace služby IIS

IIS musí mít web nakonfigurovaný s následujícími:

* **Název** &ndash; hostitele Výchozí **web** se obvykle používá s `localhost`názvem **hostitele** aplikace . Všechny platné webové stránky iis s jedinečným názvem hostitele však fungují.
* **Vazba webu**
  * Pro aplikace, které vyžadují protokol HTTPS, vytvořte vazbu na port 443 s certifikátem. Obvykle se používá **certifikát IIS Express Development Certificate,** ale funguje všechny platné certifikáty.
  * Pro aplikace, které používají protokol HTTP, potvrďte existenci vazby pro zaúčtování 80 nebo vytvořte vazbu na port 80 pro nový web.
  * Použijte jednu vazbu pro protokol HTTP nebo HTTPS. **Vazba na porty HTTP i HTTPS současně není podporována.**

## <a name="enable-development-time-iis-support-in-visual-studio"></a>Povolení podpory služby IIS v době vývoje v sadě Visual Studio

1. Spusťte instalační program sady Visual Studio.
1. Vyberte **Změnit** pro instalaci sady Visual Studio, kterou chcete použít pro podporu vývoje služby IIS.
1. Pro **ASP.NET a vývoj webových** úloh vyhledejte a nainstalujte součást **podpory služby IIS pro vývoj času vývoje.**

   Součást je uvedena v části **Volitelné** **čas podpory služby IIS** v panelu **Podrobnosti instalace** vpravo od úloh. Součást nainstaluje [ASP.NET základní modul](xref:host-and-deploy/aspnet-core-module), což je nativní modul služby IIS potřebný ke spuštění aplikací ASP.NET Core se službou IIS.

## <a name="configure-the-project"></a>Konfigurace projektu

### <a name="https-redirection"></a>Přesměrování https

U nového projektu, který vyžaduje protokol HTTPS, zaškrtněte políčko **Konfigurovat protokol HTTPS** v okně Vytvořit nový ASP.NET základní webové **aplikace.** Zaškrtnutím políčka přidáte do aplikace přesměrování [HTTPS a HSTS Middleware,](xref:security/enforcing-ssl) když se vytvoří.

Pro existující projekt, který vyžaduje protokol HTTPS, použijte v `Startup.Configure`aplikaci příkazy přesměrování HTTPS a Middleware HSTS . Další informace naleznete v tématu <xref:security/enforcing-ssl>.

Pro projekt, který používá HTTP, [https přesměrování a HSTS Middleware](xref:security/enforcing-ssl) nejsou přidány do aplikace. Není vyžadována žádná konfigurace aplikace.

### <a name="iis-launch-profile"></a>Profil spuštění iis

Vytvořte nový spouštěcí profil pro přidání podpory služby IIS v době vývoje:

1. Klepněte pravým tlačítkem myši na projekt v **Průzkumníku řešení**. Vyberte **vlastnosti**. Otevřete kartu **Ladění.**
1. V **části Profil**vyberte tlačítko **Nový.** V automaticky otevíraném okně pojmenujte profil IIS. Chcete-li vytvořit profil, vyberte **OK.**
1. Pro **nastavení Spuštění** vyberte **iis** ze seznamu.
1. Zaškrtněte políčko **spustit prohlížeč** a zadejte adresu URL koncového bodu.

   Když aplikace vyžaduje HTTPS, použijte koncový`https://`bod HTTPS ( ). Pro protokol HTTP použijte`http://`koncový bod HTTP ( ).

   Zadejte stejný název hostitele a port jako [konfigurace služby IIS zadaná dříve ,](#configure-iis)obvykle . `localhost`

   Na konci adresy URL zadejte název aplikace.

   Například `https://localhost/WebApplication1` (HTTPS) `http://localhost/WebApplication1` nebo (HTTP) jsou platné adresy URL koncového bodu.
1. V části **Proměnné prostředí** vyberte tlačítko **Přidat.** Poskytněte proměnné prostředí `ASPNETCORE_ENVIRONMENT` s názvem `Development` **a** **hodnotou** .
1. V oblasti **Nastavení webového serveru** nastavte **adresu URL aplikace** na stejnou hodnotu, která se používá pro adresu URL koncového bodu **prohlížeče launch.**
1. Pro nastavení **hostování modelu** v sadě Visual Studio 2019 nebo novější vyberte **Výchozí,** chcete-li použít hostitelský model používaný v projektu. Pokud projekt nastaví `<AspNetCoreHostingModel>` vlastnost v souboru projektu,`InProcess` použije `OutOfProcess`se hodnota vlastnosti ( nebo ). Pokud vlastnost není k dispozici, použije se výchozí model hostování aplikace, který je v procesu. Pokud aplikace vyžaduje explicitní nastavení hostitelského modelu odlišné od normálního hostitelského `In Process` modelu `Out Of Process` aplikace, nastavte **model hostování** na buď nebo podle potřeby.
1. Uložte profil.

Pokud nepoužíváte Visual Studio, ručně přidejte spouštěcí profil do souboru [launchSettings.json](https://json.schemastore.org/launchsettings) ve složce *Vlastnosti.* Následující příklad konfiguruje profil tak, aby používal protokol HTTPS:

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

Zkontrolujte, `applicationUrl` `launchUrl` zda koncové body a odpovídají a používají stejný protokol jako konfigurace vazby služby IIS, a to buď HTTP, nebo HTTPS.

## <a name="run-the-project"></a>Spuštění projektu

Spusťte Visual Studio jako správce:

* Zkontrolujte, zda je rozevírací seznam konfigurace sestavení nastaven na **ladit**.
* Nastavte [tlačítko Spustit ladění](/visualstudio/debugger/debugger-feature-tour) na profil **iis** a vyberte tlačítko pro spuštění aplikace.

Visual Studio může vyzvat k restartování, pokud není spuštěn jako správce. Po zobrazení výzvy restartujte visual studio.

Pokud je použit nedůvěryhodný vývojový certifikát, prohlížeč může vyžadovat vytvoření výjimky pro nedůvěryhodný certifikát.

> [!NOTE]
> Ladění konfigurace sestavení verze s [optimalizací](/visualstudio/debugger/just-my-code) pouze můj kód a kompilátoru má za následek zhoršené prostředí. Například body přerušení nejsou přístupů.

## <a name="additional-resources"></a>Další zdroje

* [Začínáme se Správcem služby IIS ve službách IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Tento článek popisuje podporu [sady Visual Studio](https://visualstudio.microsoft.com) pro ladění aplikací ASP.NET Core spuštěných se službou IIS v systému Windows Server. Toto téma vás provede povolením tohoto scénáře a nastavením projektu.

## <a name="prerequisites"></a>Požadavky

* [Visual Studio pro Windows](https://visualstudio.microsoft.com/downloads/)
* **ASP.NET a pracovní zátěž pro vývoj webových** aplikací
* Pracovní **vytížení napříč platformami .NET Core**
* Certifikát zabezpečení X.509 (pro podporu HTTPS)

## <a name="enable-iis"></a>Povolení iis

1. V systému Windows přejděte na **Ovládací panely** > **Programy** > **a funkce** > **Zapněte nebo vypněte funkce systému Windows** (levá strana obrazovky).
1. Zaškrtněte políčko **Internetová informační služba.** Vyberte **OK**.

Instalace služby IIS může vyžadovat restartování systému.

## <a name="configure-iis"></a>Konfigurace služby IIS

IIS musí mít web nakonfigurovaný s následujícími:

* **Název** &ndash; hostitele Výchozí **web** se obvykle používá s `localhost`názvem **hostitele** aplikace . Všechny platné webové stránky iis s jedinečným názvem hostitele však fungují.
* **Vazba webu**
  * Pro aplikace, které vyžadují protokol HTTPS, vytvořte vazbu na port 443 s certifikátem. Obvykle se používá **certifikát IIS Express Development Certificate,** ale funguje všechny platné certifikáty.
  * Pro aplikace, které používají protokol HTTP, potvrďte existenci vazby pro zaúčtování 80 nebo vytvořte vazbu na port 80 pro nový web.
  * Použijte jednu vazbu pro protokol HTTP nebo HTTPS. **Vazba na porty HTTP i HTTPS současně není podporována.**

## <a name="enable-development-time-iis-support-in-visual-studio"></a>Povolení podpory služby IIS v době vývoje v sadě Visual Studio

1. Spusťte instalační program sady Visual Studio.
1. Vyberte **Změnit** pro instalaci sady Visual Studio, kterou chcete použít pro podporu vývoje služby IIS.
1. Pro **ASP.NET a vývoj webových** úloh vyhledejte a nainstalujte součást **podpory služby IIS pro vývoj času vývoje.**

   Součást je uvedena v části **Volitelné** **čas podpory služby IIS** v panelu **Podrobnosti instalace** vpravo od úloh. Součást nainstaluje [ASP.NET základní modul](xref:host-and-deploy/aspnet-core-module), což je nativní modul služby IIS potřebný ke spuštění aplikací ASP.NET Core se službou IIS.

## <a name="configure-the-project"></a>Konfigurace projektu

### <a name="https-redirection"></a>Přesměrování https

U nového projektu, který vyžaduje protokol HTTPS, zaškrtněte políčko **Konfigurovat protokol HTTPS** v okně Vytvořit nový ASP.NET základní webové **aplikace.** Zaškrtnutím políčka přidáte do aplikace přesměrování [HTTPS a HSTS Middleware,](xref:security/enforcing-ssl) když se vytvoří.

Pro existující projekt, který vyžaduje protokol HTTPS, použijte v `Startup.Configure`aplikaci příkazy přesměrování HTTPS a Middleware HSTS . Další informace naleznete v tématu <xref:security/enforcing-ssl>.

Pro projekt, který používá HTTP, [https přesměrování a HSTS Middleware](xref:security/enforcing-ssl) nejsou přidány do aplikace. Není vyžadována žádná konfigurace aplikace.

### <a name="iis-launch-profile"></a>Profil spuštění iis

Vytvořte nový spouštěcí profil pro přidání podpory služby IIS v době vývoje:

1. Klepněte pravým tlačítkem myši na projekt v **Průzkumníku řešení**. Vyberte **vlastnosti**. Otevřete kartu **Ladění.**
1. V **části Profil**vyberte tlačítko **Nový.** V automaticky otevíraném okně pojmenujte profil IIS. Chcete-li vytvořit profil, vyberte **OK.**
1. Pro **nastavení Spuštění** vyberte **iis** ze seznamu.
1. Zaškrtněte políčko **spustit prohlížeč** a zadejte adresu URL koncového bodu.

   Když aplikace vyžaduje HTTPS, použijte koncový`https://`bod HTTPS ( ). Pro protokol HTTP použijte`http://`koncový bod HTTP ( ).

   Zadejte stejný název hostitele a port jako [konfigurace služby IIS zadaná dříve ,](#configure-iis)obvykle . `localhost`

   Na konci adresy URL zadejte název aplikace.

   Například `https://localhost/WebApplication1` (HTTPS) `http://localhost/WebApplication1` nebo (HTTP) jsou platné adresy URL koncového bodu.
1. V části **Proměnné prostředí** vyberte tlačítko **Přidat.** Poskytněte proměnné prostředí `ASPNETCORE_ENVIRONMENT` s názvem `Development` **a** **hodnotou** .
1. V oblasti **Nastavení webového serveru** nastavte **adresu URL aplikace** na stejnou hodnotu, která se používá pro adresu URL koncového bodu **prohlížeče launch.**
1. Pro nastavení **hostování modelu** v sadě Visual Studio 2019 nebo novější vyberte **Výchozí,** chcete-li použít hostitelský model používaný v projektu. Pokud projekt nastaví `<AspNetCoreHostingModel>` vlastnost v souboru projektu,`InProcess` použije `OutOfProcess`se hodnota vlastnosti ( nebo ). Pokud vlastnost není k dispozici, použije se výchozí model hostování aplikace, který je mimo proces. Pokud aplikace vyžaduje explicitní nastavení hostitelského modelu odlišné od normálního hostitelského `In Process` modelu `Out Of Process` aplikace, nastavte **model hostování** na buď nebo podle potřeby.
1. Uložte profil.

Pokud nepoužíváte Visual Studio, ručně přidejte spouštěcí profil do souboru [launchSettings.json](https://json.schemastore.org/launchsettings) ve složce *Vlastnosti.* Následující příklad konfiguruje profil tak, aby používal protokol HTTPS:

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

Zkontrolujte, `applicationUrl` `launchUrl` zda koncové body a odpovídají a používají stejný protokol jako konfigurace vazby služby IIS, a to buď HTTP, nebo HTTPS.

## <a name="run-the-project"></a>Spuštění projektu

Spusťte Visual Studio jako správce:

* Zkontrolujte, zda je rozevírací seznam konfigurace sestavení nastaven na **ladit**.
* Nastavte [tlačítko Spustit ladění](/visualstudio/debugger/debugger-feature-tour) na profil **iis** a vyberte tlačítko pro spuštění aplikace.

Visual Studio může vyzvat k restartování, pokud není spuštěn jako správce. Po zobrazení výzvy restartujte visual studio.

Pokud je použit nedůvěryhodný vývojový certifikát, prohlížeč může vyžadovat vytvoření výjimky pro nedůvěryhodný certifikát.

> [!NOTE]
> Ladění konfigurace sestavení verze s [optimalizací](/visualstudio/debugger/just-my-code) pouze můj kód a kompilátoru má za následek zhoršené prostředí. Například body přerušení nejsou přístupů.

## <a name="additional-resources"></a>Další zdroje

* [Začínáme se Správcem služby IIS ve službách IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end
