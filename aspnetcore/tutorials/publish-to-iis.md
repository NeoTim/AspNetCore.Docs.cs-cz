---
title: Publikování aplikace ASP.NET Core do iis
author: rick-anderson
description: Přečtěte si, jak hostovat aplikaci ASP.NET Core na serveru Služby IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/03/2019
uid: tutorials/publish-to-iis
ms.openlocfilehash: 47f78ba78741a8e0175ce801c0c0e51f091273a8
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511389"
---
# <a name="publish-an-aspnet-core-app-to-iis"></a>Publikování aplikace ASP.NET Core do iis

Tento kurz ukazuje, jak hostovat aplikaci ASP.NET Core na serveru služby IIS.

Tento kurz se zabývá následujícími tématy:

> [!div class="checklist"]
> * Nainstalujte sadu .NET Core Hosting Na Windows Server.
> * Vytvořte web služby IIS ve Správci služby IIS.
> * Nasaďte aplikaci ASP.NET Core.

## <a name="prerequisites"></a>Požadavky

* [Sada .NET Core SDK](/dotnet/core/sdk) nainstalovaná ve vývojovém počítači.
* Systém Windows Server je nakonfigurován s rolí serveru **Www Server (IIS).** Pokud váš server není nakonfigurován tak, aby hostoval weby se službou IIS, postupujte podle pokynů v části *konfigurace služby IIS* v <xref:host-and-deploy/iis/index#iis-configuration> článku a vraťte se k tomuto kurzu.

> [!WARNING]
> **Konfigurace služby IIS a zabezpečení webu zahrnují koncepty, na které se tento kurz nevztahuje.** Před hostováním produkčních aplikací ve službě IIS naleznete pokyny služby IIS v [dokumentaci služby Microsoft IIS](https://www.iis.net/) a [v článku ASP.NET Core o hostování se službou IIS.](xref:host-and-deploy/iis/index)
>
> Mezi důležité scénáře pro hostování služby IIS, na které se tento kurz nevztahuje, patří:
>
> * [Vytvoření podregistru pro ASP.NET základní ochrany dat](xref:host-and-deploy/iis/index#data-protection)
> * [Konfigurace seznamu řízení přístupu fondu aplikací (ACL)](xref:host-and-deploy/iis/index#application-pool-identity)
> * Chcete-li se zaměřit na koncepty nasazení služby IIS, tento kurz nasadí aplikaci bez zabezpečení HTTPS nakonfigurovaného ve službě IIS. Další informace o hostování aplikace povolené pro protokol HTTPS najdete v tématech zabezpečení v části [Další prostředky](#additional-resources) v tomto článku. Další pokyny pro hostování ASP.NET aplikace <xref:host-and-deploy/iis/index> Core jsou uvedeny v článku.

## <a name="install-the-net-core-hosting-bundle"></a>Instalace balíčku hostingu jádra .NET

Nainstalujte *sadu .NET Core Hosting Na* server služby IIS. Balíček nainstaluje modul .NET Core Runtime, knihovnu .NET Core Library a [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module). Modul umožňuje ASP.NET aplikace Core běžet za službou IIS.

Stáhněte si instalační program pomocí následujícího odkazu:

[Aktuální instalační program .NET Core Hosting Bundle (přímé stahování)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

1. Spusťte instalační program na serveru služby IIS.

1. Restartujte server nebo spusťte **net stop byl /y** následovaný **net start w3svc** v příkazovém prostředí.

## <a name="create-the-iis-site"></a>Vytvoření webu iis

1. Na serveru Služby IIS vytvořte složku obsahující publikované složky a soubory aplikace. V následujícím kroku je cesta ke složce k dispozici službu IIS jako fyzická cesta k aplikaci.

1. Ve Správci služby IIS otevřete uzel serveru v panelu **Připojení.** Klikněte pravým tlačítkem myši na složku **Weby.** V kontextové nabídce vyberte **Přidat web.**

1. Zadejte **název webu** a nastavte **fyzickou cestu** ke složce nasazení aplikace, kterou jste vytvořili. Zadejte konfiguraci **vazby** a vytvořte web výběrem **možnosti OK**.

## <a name="create-an-aspnet-core-razor-pages-app"></a>Vytvoření aplikace ASP.NET Core Razor Pages

Podle <xref:getting-started> kurzu vytvořte aplikaci Razor Pages.

## <a name="publish-and-deploy-the-app"></a>Publikování a nasazení aplikace

*Publikování aplikace* znamená vytvoření kompilované aplikace, kterou může hostovat server. *Nasazení aplikace* znamená přesunout publikovanou aplikaci do hostitelského systému. Krok publikování je zpracován sadou [.NET Core SDK](/dotnet/core/sdk), zatímco krok nasazení lze zpracovat pomocí různých přístupů. Tento kurz přijímá přístup nasazení *složky,* kde:

* Aplikace se publikuje do složky.
* Obsah složky je přesunut do složky webu služby IIS **(fyzická cesta** k webu ve Správci služby IIS).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Klikněte pravým tlačítkem myši na projekt v **Průzkumníku řešení** a vyberte **publikovat**.
1. V **dialogovém** okně Vybrat cíl publikování vyberte možnost Publikovat **složku.**
1. Nastavte cestu **ke složce nebo sdílené složce.**
   * Pokud jste vytvořili složku pro web služby IIS, která je k dispozici ve vývojovém počítači jako sdílená síťová složka, zadejte cestu ke sdílené položce. Aktuální uživatel musí mít přístup pro zápis, aby mohl publikovat do sdílené položky.
   * Pokud se vám nedaří nasadit přímo do složky webu služby IIS na serveru služby IIS, publikujte do složky na odstranitelném médiu a fyzicky přesuňte publikovanou aplikaci do složky webu služby IIS na serveru, což je **fyzická cesta** webu ve Správci služby IIS. Přesuňte obsah složky *bin/Release/{TARGET FRAMEWORK}/publish* do složky webu služby IIS na serveru, což je **fyzická cesta** webu ve Správci služby IIS.

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

1. V příkazovém prostředí publikujte aplikaci v konfiguraci release pomocí příkazu [dotnet publish:](/dotnet/core/tools/dotnet-publish)

   ```dotnetcli
   dotnet publish --configuration Release
   ```

1. Přesuňte obsah složky *bin/Release/{TARGET FRAMEWORK}/publish* do složky webu služby IIS na serveru, což je **fyzická cesta** webu ve Správci služby IIS.

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

1. Klikněte pravým tlačítkem myši na projekt v **části Řešení** a vyberte **publikovat** > **publikovat do složky**.
1. Nastavte možnost Zvolit cestu **ke složce.**
   * Pokud jste vytvořili složku pro web služby IIS, která je k dispozici ve vývojovém počítači jako sdílená síťová složka, zadejte cestu ke sdílené položce. Aktuální uživatel musí mít přístup pro zápis, aby mohl publikovat do sdílené položky.
   * Pokud se vám nedaří nasadit přímo do složky webu služby IIS na serveru služby IIS, publikujte do složky na odstranitelném médiu a fyzicky přesuňte publikovanou aplikaci do složky webu služby IIS na serveru, což je **fyzická cesta** webu ve Správci služby IIS. Přesuňte obsah složky *bin/Release/{TARGET FRAMEWORK}/publish* do složky webu služby IIS na serveru, což je **fyzická cesta** webu ve Správci služby IIS.

---

## <a name="browse-the-website"></a>Procházení webu

Aplikace je přístupná v prohlížeči poté, co obdrží první požadavek. Požádejte o aplikaci na vazbě koncového bodu, kterou jste vytvořili ve Správci služby IIS pro web.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Nainstalujte sadu .NET Core Hosting Na Windows Server.
> * Vytvořte web služby IIS ve Správci služby IIS.
> * Nasaďte aplikaci ASP.NET Core.

Další informace o hostování aplikací ASP.NET Core ve službě IIS naleznete v článku Přehled služby IIS:

> [!div class="nextstepaction"]
> <xref:host-and-deploy/iis/index>

## <a name="additional-resources"></a>Další zdroje

### <a name="articles-in-the-aspnet-core-documentation-set"></a>Články v sadě dokumentace ASP.NET Core

* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:test/troubleshoot-azure-iis>
* <xref:security/enforcing-ssl>

### <a name="articles-pertaining-to-aspnet-core-app-deployment"></a>Články týkající se nasazení aplikace ASP.NET Core

* <xref:tutorials/publish-to-azure-webapp-using-vs>
* <xref:tutorials/publish-to-azure-webapp-using-vscode>
* <xref:host-and-deploy/visual-studio-publish-profiles>
* [Publikování webové aplikace do složky pomocí Visual Studia pro Mac](/visualstudio/mac/publish-folder)

### <a name="articles-on-iis-https-configuration"></a>Články o konfiguraci protokolu HTTPS služby IIS

* [Konfigurace ssl ve Správci služby IIS](/iis/manage/configuring-security/configuring-ssl-in-iis-manager)
* [Jak nastavit ssl ve službě IIS](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)

### <a name="articles-on-iis-and-windows-server"></a>Články o službě IIS a systému Windows Server

* [Oficiální web služby Microsoft IIS](https://www.iis.net/)
* [Knihovna technického obsahu systému Windows Server](/windows-server/windows-server)
