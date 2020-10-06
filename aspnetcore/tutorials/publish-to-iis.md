---
title: Publikování aplikace ASP.NET Core ve službě IIS
author: rick-anderson
description: Naučte se hostovat aplikaci ASP.NET Core na serveru IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/03/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/publish-to-iis
ms.openlocfilehash: 40c47da472257862414ba33be582eb19d3f0b29c
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91754551"
---
# <a name="publish-an-aspnet-core-app-to-iis"></a>Publikování aplikace ASP.NET Core ve službě IIS

V tomto kurzu se dozvíte, jak hostovat aplikaci ASP.NET Core na serveru IIS.

Tento kurz se zabývá následujícími tématy:

> [!div class="checklist"]
> * Nainstalujte hostující sadu .NET Core na Windows Server.
> * Vytvořte web IIS ve Správci služby IIS.
> * Nasaďte aplikaci ASP.NET Core.

## <a name="prerequisites"></a>Předpoklady

* [.NET Core SDK](/dotnet/core/sdk) nainstalované na vývojovém počítači.
* Windows Server nakonfigurovaný pomocí role serveru **webový server (IIS)** . Pokud váš server není nakonfigurovaný na hostování webů se službou IIS, postupujte podle pokynů v části *Konfigurace služby IIS* v <xref:host-and-deploy/iis/index#iis-configuration> článku a pak se vraťte k tomuto kurzu.

> [!WARNING]
> **Konfigurace služby IIS a zabezpečení webu zahrnují koncepty, na které se tento kurz nevztahuje.** Další informace najdete v dokumentaci ke službě IIS v dokumentaci ke službě [Microsoft IIS](https://www.iis.net/) a v [ASP.NET Core článku o hostování se službou IIS](xref:host-and-deploy/iis/index) před hostováním produkčních aplikací ve službě IIS.
>
> Důležité scénáře pro hostování služby IIS, na které se nevztahuje tento kurz, zahrnují:
>
> * [Vytvoření podregistru pro ochranu ASP.NET Core dat](xref:host-and-deploy/iis/index#data-protection)
> * [Konfigurace seznamu Access Control fondu aplikací (ACL)](xref:host-and-deploy/iis/index#application-pool-identity)
> * V tomto kurzu nasadíte v rámci konceptů nasazení služby IIS aplikaci bez zabezpečení HTTPS nakonfigurovanou ve službě IIS. Další informace o hostování aplikace, která je povolená pro protokol HTTPS, najdete v tématech zabezpečení v části [Další zdroje](#additional-resources) tohoto článku. Další pokyny pro hostování ASP.NET Core aplikací jsou uvedené v <xref:host-and-deploy/iis/index> článku.

## <a name="install-the-net-core-hosting-bundle"></a>Instalace hostující sady .NET Core

Na server IIS nainstalujte *hostující sadu .NET Core* . Svazek nainstaluje modul runtime .NET Core, knihovnu .NET Core a [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module). Modul umožňuje, aby aplikace ASP.NET Core běžely za službou IIS.

Stáhněte instalační program pomocí následujícího odkazu:

[Aktuální instalační program sady hostujících sad .NET Core (přímé stahování)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

1. Spusťte instalační program na serveru IIS.

1. Restartujte server nebo spusťte `net stop was /y` příkaz a potom `net start w3svc` v příkazovém prostředí.

## <a name="create-the-iis-site"></a>Vytvoření webu služby IIS

1. Na serveru služby IIS vytvořte složku, která bude obsahovat publikované složky a soubory aplikace. V následujícím kroku je jako fyzická cesta k aplikaci služba IIS poskytována jako cesta k této složce. Další informace o složce nasazení aplikace a rozložení souborů naleznete v tématu <xref:host-and-deploy/directory-structure> .

1. Ve Správci služby IIS otevřete uzel serveru na panelu **připojení** . Klikněte pravým tlačítkem na složku **weby** . V místní nabídce vyberte **Přidat web** .

1. Zadejte **název lokality** a nastavte **fyzickou cestu** ke složce pro nasazení aplikace, kterou jste vytvořili. Zadejte konfiguraci **vazby** a vytvořte web výběrem **OK**.

   > [!WARNING]
   > Nelze použít vazby zástupných znaků na nejvyšší úrovni ( `http://*:80/` a `http://+:80` ). **not** Vazby zástupných znaků nejvyšší úrovně můžou aplikaci otevřít pro slabá místa zabezpečení. To platí pro silné i slabé zástupné znaky. Místo zástupných znaků použijte explicitní názvy hostitelů. Vazba zástupných znaků subdomény (například `*.mysub.com` ) nemá toto bezpečnostní riziko, pokud ovládáte celou nadřazenou doménu (na rozdíl od `*.com` , která je zranitelná). Další informace najdete v [části rfc7230 část-5,4](https://tools.ietf.org/html/rfc7230#section-5.4) .

1. Potvrďte, že identita modelu procesu má správná oprávnění.

   Pokud se výchozí identita fondu aplikací (**modelu procesu**  >  **Identity** ) změní z `ApplicationPoolIdentity` na jinou identitu, ověřte, že Nová identita má požadovaná oprávnění pro přístup ke složce, databázi a dalším požadovaným prostředkům aplikace. Například fond aplikací vyžaduje přístup pro čtení a zápis ke složkám, kde aplikace čte a zapisuje soubory.

## <a name="create-an-aspnet-core-no-locrazor-pages-app"></a>Vytvoření aplikace ASP.NET Core Razor Pages

Pokud <xref:getting-started> chcete vytvořit aplikaci Pages, postupujte podle kurzu Razor .

## <a name="publish-and-deploy-the-app"></a>Publikování a nasazení aplikace

*Publikování aplikace* znamená, že se vytvoří kompilovaná aplikace, která může být hostována serverem. *Nasazení aplikace* znamená přesunutí publikované aplikace do hostitelského systému. Krok publikování je zpracováván [.NET Core SDK](/dotnet/core/sdk), zatímco krok nasazení může být zpracován řadou přístupů. Tento kurz přijme přístup k nasazení *složky* , kde:
 
* Aplikace se publikuje do složky.
* Obsah složky se přesune do složky webu IIS ( **fyzická cesta** k lokalitě ve Správci služby IIS).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. V **Průzkumník řešení** klikněte pravým tlačítkem na projekt a vyberte **publikovat**.
1. V dialogovém okně **vybrat cíl publikování** vyberte možnost publikování **složky** .
1. Nastavte **složku nebo cestu sdílení souborů** .
   * Pokud jste vytvořili složku pro web IIS, která je k dispozici ve vývojovém počítači jako sdílená síťová složka, zadejte cestu ke sdílené složce. Aktuální uživatel musí mít oprávnění k zápisu pro publikování do sdílené složky.
   * Pokud se nemůžete přímo nasadit do složky webu IIS na serveru služby IIS, můžete publikovat do složky na vyměnitelném médiu a fyzicky přesunout publikovanou aplikaci do složky webu IIS na serveru, což je **fyzická cesta** lokality ve Správci služby IIS. Přesuňte obsah `bin/Release/{TARGET FRAMEWORK}/publish` složky do složky webu IIS na serveru, což je **fyzická cesta** lokality ve Správci služby IIS.

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

1. V příkazovém prostředí publikujte aplikaci v konfiguraci vydaných verzí pomocí příkazu [dotnet Publish](/dotnet/core/tools/dotnet-publish) :

   ```dotnetcli
   dotnet publish --configuration Release
   ```

1. Přesuňte obsah `bin/Release/{TARGET FRAMEWORK}/publish` složky do složky webu IIS na serveru, což je **fyzická cesta** lokality ve Správci služby IIS.

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

1. Klikněte pravým tlačítkem na projekt v **řešení** a vyberte **publikovat**  >  **publikovat do složky**.
1. Nastavte cestu ke **složce** .
   * Pokud jste vytvořili složku pro web IIS, která je k dispozici ve vývojovém počítači jako sdílená síťová složka, zadejte cestu ke sdílené složce. Aktuální uživatel musí mít oprávnění k zápisu pro publikování do sdílené složky.
   * Pokud se nemůžete přímo nasadit do složky webu IIS na serveru služby IIS, proveďte publikování do složky na neodstranitelné médium a fyzicky přesuňte publikovanou aplikaci do složky webu IIS na serveru, což je **fyzická cesta** k webu ve Správci služby IIS. Přesuňte obsah `bin/Release/{TARGET FRAMEWORK}/publish` složky do složky webu IIS na serveru, což je **fyzická cesta** lokality ve Správci služby IIS.

---

## <a name="browse-the-website"></a>Procházet web

Aplikace je v prohlížeči přístupná, až obdrží první požadavek. Vyžádejte aplikaci na vazbu koncového bodu, kterou jste navázali ve Správci služby IIS pro danou lokalitu.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Nainstalujte hostující sadu .NET Core na Windows Server.
> * Vytvořte web IIS ve Správci služby IIS.
> * Nasaďte aplikaci ASP.NET Core.

Další informace o hostování ASP.NET Corech aplikací ve službě IIS najdete v článku Přehled služby IIS:

> [!div class="nextstepaction"]
> <xref:host-and-deploy/iis/index>

## <a name="additional-resources"></a>Další zdroje informací

### <a name="articles-in-the-aspnet-core-documentation-set"></a>Články v sadě dokumentace k ASP.NET Core

* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:test/troubleshoot-azure-iis>
* <xref:security/enforcing-ssl>

### <a name="articles-pertaining-to-aspnet-core-app-deployment"></a>Články týkající se ASP.NET Coreho nasazení aplikace

* <xref:tutorials/publish-to-azure-webapp-using-vs>
* <xref:tutorials/publish-to-azure-webapp-using-vscode>
* <xref:host-and-deploy/visual-studio-publish-profiles>
* [Publikování webové aplikace do složky pomocí Visual Studio pro Mac](/visualstudio/mac/publish-folder)

### <a name="articles-on-iis-https-configuration"></a>Články týkající se konfigurace protokolu HTTPS služby IIS

* [Konfigurace SSL ve Správci služby IIS](/iis/manage/configuring-security/configuring-ssl-in-iis-manager)
* [Jak nastavit SSL v IIS](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)

### <a name="articles-on-iis-and-windows-server"></a>Články na IIS a Windows serveru

* [Oficiální web Microsoft IIS](https://www.iis.net/)
* [Knihovna technických obsahu pro Windows Server](/windows-server/windows-server)

### <a name="deployment-resources-for-iis-administrators"></a>Prostředky nasazení pro správce služby IIS

* [Dokumentace ke službě IIS](/iis)
* [Začínáme ve službě IIS pomocí Správce služby IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [Nasazení aplikace .NET Core](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

