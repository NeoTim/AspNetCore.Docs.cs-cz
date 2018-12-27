---
title: Podpora služby IIS při vývoji v sadě Visual Studio pro ASP.NET Core
author: shirhatti
description: Podívejte se na podporu pro ladění aplikací ASP.NET Core, když za služby IIS a systémem Windows Server.
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2018
uid: host-and-deploy/iis/development-time-iis-support
ms.openlocfilehash: 44570bb28451ce4c5fde12ec77e3856fb5bd3062
ms.sourcegitcommit: 816f39e852a8f453e8682081871a31bc66db153a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53637661"
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a>Podpora služby IIS při vývoji v sadě Visual Studio pro ASP.NET Core

Podle [Sourabh Shirhatti](https://twitter.com/sshirhatti) a [Luke Latham](https://github.com/guardrex)

Tento článek popisuje [sady Visual Studio](https://www.visualstudio.com/vs/) podporuje ladění aplikací ASP.NET Core spuštěné za nástrojem služby IIS v systému Windows Server. Toto téma vás provede povolením této funkce a nastavení projektu.

## <a name="prerequisites"></a>Požadavky

* [Visual Studio pro Windows](https://www.microsoft.com/net/download/windows)
* **Vývoj pro ASP.NET a web** pracovního vytížení
* **Vývoj pro různé platformy .NET core** pracovního vytížení
* Bezpečnostní certifikát X.509

## <a name="enable-iis"></a>Povolte službu IIS

1. Přejděte do **ovládací panely** > **programy** > **programy a funkce** > **zapnout Windows funkce na nebo vypnout** (levé straně obrazovky).
1. Vyberte **Internetová informační služba** zaškrtávací políčko.

![Funkce Windows zobrazující Internetová informační služba zaškrtávací políčko zaškrtnuto jako černá čtverec (ne zaškrtnutí) označující, že jsou povolené některé z funkcí služby IIS](development-time-iis-support/_static/enable_iis.png)

Instalace služby IIS může vyžadovat restartování systému.

## <a name="configure-iis"></a>Konfigurace služby IIS

Služba IIS musí mít web nakonfigurované tyto parametry:

* Název hostitele, který odpovídá název hostitele adresy URL profilu spuštění aplikace.
* Vazby portu 443 s certifikátem přiřazené.

Například **název hostitele** pro přidání webu je nastaven na hodnotu "localhost" (profil spuštění také používat "localhost" dále v tomto tématu). Port je nastavena na hodnotu "443" (HTTPS). **Služby IIS Express vývojářský certifikát, kterým** je přiřazen k webu, ale libovolný platný certifikát funguje:

![Přidáte okno webu ve službě IIS, zobrazuje se sada vazby pro místního hostitele na portu 443 s certifikátem přiřazené.](development-time-iis-support/_static/add-website-window.png)

Pokud má již instalace služby IIS **výchozí webový server** s názvem hostitele, který odpovídá název hostitele adresy URL profilu spuštění aplikace:

* Přidáte vazbu na port pro port 443 (HTTPS).
* Přiřaďte platného certifikátu k webu.

## <a name="enable-development-time-iis-support-in-visual-studio"></a>Povolit podporu služby IIS dobu vývoje v sadě Visual Studio

1. Spusťte instalační program sady Visual Studio.
1. Vyberte **dobu vývoje podpora služby IIS** komponenty. Součást je uveden jako volitelný v **Souhrn** panelu **vývoj pro ASP.NET a web** pracovního vytížení. Nainstaluje komponenty [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module), což je nativní modul IIS potřebné ke spuštění aplikace ASP.NET Core se službou IIS.

![Úpravy funkce aplikace Visual Studio: Je vybraná karta úlohy. V části Web a Cloud je vybraná panelu vývoj pro ASP.NET a web. Na pravé straně v oblasti volitelné panel souhrnu je zaškrtávací políčko pro při vývoji podpora služby IIS.](development-time-iis-support/_static/development_time_support.png)

## <a name="configure-the-project"></a>Konfigurace projektu

### <a name="https-redirection"></a>Přesměrování protokolu HTTPS

Nový projekt, zaškrtněte políčko pro **konfigurace pro protokol HTTPS** v **nová webová aplikace ASP.NET Core** okno:

![Nové okno webové aplikace ASP.NET Core s konfigurací pro zaškrtnutým políčkem HTTPS.](development-time-iis-support/_static/new-app.png)

V existujícím projektu, použijte protokol HTTPS přesměrování middlewaru v `Startup.Configure` voláním [UseHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpspolicybuilderextensions.usehttpsredirection) – metoda rozšíření:

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();

    app.UseMvc();
}
```

### <a name="iis-launch-profile"></a>Profil spuštění služby IIS

Vytvoření nového profilu spuštění přidává dobu vývoje služby IIS:

1. Pro **profilu**, vyberte **nový** tlačítko. Název profilu "IIS" v automaticky otevíraném okně. Vyberte **OK** vytvořte profil.
1. Pro **spuštění** vyberte **IIS** ze seznamu.
1. Zaškrtněte políčko pro **spuštění prohlížeče** a zadejte adresu URL koncového bodu. Použijte protokol HTTPS. Tento příklad používá `https://localhost/WebApplication1`.
1. V **proměnné prostředí** vyberte **přidat** tlačítko. Zadejte proměnnou prostředí s klíčem `ASPNETCORE_ENVIRONMENT` a hodnotu `Development`.
1. V **nastavení webového serveru** nastavení oblasti **adresa URL aplikace**. Tento příklad používá `https://localhost/WebApplication1`.
1. Uložte profil.

![V okně vlastností projektu s vybranou kartou ladění. Nastavení profilu a spuštění se nastaví do služby IIS. Funkce spuštění prohlížeče je povolená s adresou https://localhost/WebApplication1. Poskytujeme také stejnou adresu do pole Adresa URL aplikace v oblasti nastavení webového serveru.](development-time-iis-support/_static/project_properties.png)

Můžete také ručně přidat profil spuštění, který [launchSettings.json](http://json.schemastore.org/launchsettings) souboru v aplikaci:

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

## <a name="run-the-project"></a>Spusťte projekt

V sadě Visual Studio:

* Potvrďte, že je nastavena rozevíracím seznamu konfigurací sestavení **ladění**.
* Nastavte na tlačítko Spustit **IIS** profilu a klikněte na tlačítko a spusťte aplikaci.

![Tlačítko spustit na panelu nástrojů VS je nastavena na profilaci služby IIS s rozevíracím seznamu konfigurací sestavení nastavená na vydání.](development-time-iis-support/_static/toolbar.png)

Visual Studio může výzvu restartování v případě není spuštěn jako správce. Pokud se zobrazí výzva, restartujte aplikaci Visual Studio.

Pokud se používá nedůvěryhodný vývojářský certifikát, prohlížeče mohou vyžadovat vytvořte výjimku pro nedůvěryhodný certifikát.

> [!NOTE]
> Ladění verze sestavení konfigurace s [pouze můj kód](/visualstudio/debugger/just-my-code) a optimalizace kompilátoru za následek sníženými možnostmi. Například nejsou přístupů bodech rozdělení.

## <a name="additional-resources"></a>Další zdroje

* [Hostitele ASP.NET Core ve Windows se službou IIS](xref:host-and-deploy/iis/index)
* [Úvod k modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module)
* [Referenční dokumentace k modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module)
* [Vynucení protokolu HTTPS](xref:security/enforcing-ssl)
