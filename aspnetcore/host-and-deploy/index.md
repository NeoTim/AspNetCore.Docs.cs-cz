---
title: Hostitelství a nasazení ASP.NET Core
author: guardrex
description: Zjistěte, jak nastavit hostitelská prostředí a nasazení aplikace ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2018
uid: host-and-deploy/index
ms.openlocfilehash: f443a8ee28a859b5075a8bb03016407af9a3ddb1
ms.sourcegitcommit: b34b25da2ab68e6495b2460ff570468f16a9bf0d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53284523"
---
# <a name="host-and-deploy-aspnet-core"></a>Hostitelství a nasazení ASP.NET Core

Obecný postup nasazení aplikace ASP.NET Core do hostitelského prostředí:

* Publikovanou aplikaci nasadíte do složky na hostitelském serveru.
* Nastavte správce procesu, který se spustí aplikace při žádosti o doručení a restartuje aplikaci poté, co ho dojde k chybě nebo se server nerestartuje.
* Konfigurace reverzního proxy serveru nastavení reverzní proxy server pro směrování žádostí na aplikace.

## <a name="publish-to-a-folder"></a>Publikovat do složky

[Dotnet publikovat](/dotnet/core/tools/dotnet-publish) příkaz zkompiluje kód aplikace a zkopíruje soubory potřebné ke spuštění aplikace do *publikovat* složky. Při nasazení ze sady Visual Studio `dotnet publish` kroku dojde automaticky před soubory se zkopírují do cíle nasazení.

### <a name="folder-contents"></a>Obsah složky

*Publikovat* složka obsahuje jeden nebo více souborů sestavení aplikace, závislostí a volitelně modul .NET runtime.

Aplikace .NET Core můžete publikovat jako *samostatná nasazení* nebo *nasazení závisí na architektuře*. Pokud je samostatná aplikace, jsou součástí souborů sestavení obsahující modul .NET runtime *publikovat* složky. Pokud je aplikace závisí na architektuře, nejsou zahrnuté soubory modulu runtime .NET, protože aplikace obsahuje odkaz na verzi rozhraní .NET, která je nainstalována na serveru. Výchozí model nasazení je závisí na architektuře. Další informace najdete v tématu [nasazení aplikace .NET Core](/dotnet/core/deploying/).

Kromě *.exe* a *.dll* soubory, *publikovat* složku pro aplikace ASP.NET Core obvykle obsahuje konfigurační soubory, statických prostředků a zobrazení MVC. Další informace naleznete v tématu <xref:host-and-deploy/directory-structure>.

## <a name="set-up-a-process-manager"></a>Nastavit správce procesu

Aplikace ASP.NET Core je konzolová aplikace, které musí být spuštěna, když na server se spustí a restartovaný, pokud ho dojde k chybě. Chcete-li automatizovat spustí a restartuje, je potřeba správce procesu. Nejběžnější vedoucí proces pro ASP.NET Core jsou:

* Linux
  * [Server Nginx](xref:host-and-deploy/linux-nginx)
  * [Apache](xref:host-and-deploy/linux-apache)
* Windows
  * [SLUŽBA IIS](xref:host-and-deploy/iis/index)
  * [Windows Service](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a>Nastavit reverzní proxy server

::: moniker range=">= aspnetcore-2.0"

Pokud aplikace využívá [Kestrel](xref:fundamentals/servers/kestrel) serveru [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), nebo [IIS](xref:host-and-deploy/iis/index) může sloužit jako reverzní proxy server. Reverzní proxy server přijímá požadavky HTTP z Internetu a předává je na Kestrel.

Buď konfiguraci&mdash;s nebo bez něj reverzní proxy server&mdash;je hostování podporovanou konfiguraci pro ASP.NET Core 2.0 nebo novější. Další informace najdete v tématu [použití Kestrel s reverzní proxy server](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Pokud aplikace využívá [Kestrel](xref:fundamentals/servers/kestrel) server a budou zveřejněny na Internetu, použijte [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), nebo [IIS](xref:host-and-deploy/iis/index) jako reverzní proxy server. Reverzní proxy server přijímá požadavky HTTP z Internetu a předává je na Kestrel. Hlavním důvodem pro pomocí reverzního proxy je zabezpečení. Další informace najdete v tématu [použití Kestrel s reverzní proxy server](xref:fundamentals/servers/kestrel?tabs=aspnetcore1x#when-to-use-kestrel-with-a-reverse-proxy).

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a>Proxy server a scénáře pro nástroj pro vyrovnávání zatížení

Další konfigurace může být nezbytný pro aplikací hostovaných za službou proxy servery a nástroje pro vyrovnávání zatížení. Bez další konfigurace nemusí aplikaci mít přístup k schématu (HTTP/HTTPS) a Vzdálená IP adresa původu žádost. Další informace najdete v tématu [konfigurace ASP.NET Core práci se servery proxy a nástroje pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a>Automatizace nasazení pomocí sady Visual Studio a nástroje MSBuild

Nasazení často vyžaduje další úkoly kromě kopírování výstup z [dotnet publikovat](/dotnet/core/tools/dotnet-publish) na server. Například může být nutné nebo vyloučeny ze dalších souborů *publikovat* složky. Visual Studio používá MSBuild pro nasazení webu a je možné přizpůsobit MSBuild k provádění mnoha jiných úloh během nasazování. Další informace najdete v tématu <xref:host-and-deploy/visual-studio-publish-profiles> a [pomocí nástroje MSBuild a Team Foundation Build](http://msbuildbook.com/) knihy.

S použitím [funkci Publikovat Web](xref:tutorials/publish-to-azure-webapp-using-vs) nebo [integrovanou podporu Git](xref:host-and-deploy/azure-apps/azure-continuous-deployment), aplikace se dají nasadit přímo z Visual Studio do služby Azure App Service. Podporuje služby Azure DevOps [průběžné nasazování do služby Azure App Service](/azure/devops/pipelines/targets/webapp). Další informace najdete v tématu [DevOps s využitím ASP.NET Core a Azure](xref:azure/devops/index).

## <a name="publish-to-azure"></a>Publikování do Azure

Zobrazit <xref:tutorials/publish-to-azure-webapp-using-vs> pokyny o tom, jak publikovat aplikaci do Azure pomocí sady Visual Studio. Další příklad poskytuje [vytvoření webové aplikace ASP.NET Core v Azure](/azure/app-service/app-service-web-get-started-dotnet).

## <a name="publish-with-msdeploy-on-windows"></a>Publikování pomocí nástroje MSDeploy na Windows

V tématu <xref:host-and-deploy/visual-studio-publish-profiles> pro profil publikování pokyny o tom, jak publikovat aplikace pomocí sady Visual Studio, včetně pomocí příkazového řádku Windows [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) příkazu.

## <a name="host-in-a-web-farm"></a>Hostování ve webové farmě

Informace o konfiguraci pro hostování aplikací ASP.NET Core v prostředí webové farmy (například nasazení více instancí vaší aplikace pro zajištění škálovatelnosti), najdete v části <xref:host-and-deploy/web-farm>.

::: moniker range=">= aspnetcore-2.2"

## <a name="perform-health-checks"></a>Provádění kontroly stavu

Použití middlewaru zkontrolovat stav k provádění kontroly stavu aplikace a jeho závislosti. Další informace naleznete v tématu <xref:host-and-deploy/health-checks>.

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* <xref:host-and-deploy/docker/index>
* <xref:test/troubleshoot>
