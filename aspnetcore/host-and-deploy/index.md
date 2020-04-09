---
title: Hostování a nasazení ASP.NET core
author: rick-anderson
description: Přečtěte si, jak nastavit hostingová prostředí a nasadit ASP.NET aplikace Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/index
ms.openlocfilehash: 464d19bd63e1f0f06bd7d218e7644afde04a5672
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657918"
---
# <a name="host-and-deploy-aspnet-core"></a>Hostování a nasazení ASP.NET core

::: moniker range=">= aspnetcore-2.2"

Obecně platí, že nasazení aplikace ASP.NET Core do hostitelského prostředí:

* Nasadit publikovanou aplikaci do složky na hostitelském serveru.
* Nastavte správce procesů, který spustí aplikaci, když dorazí požadavky a restartuje aplikaci po pádu nebo restartování serveru.
* Pro konfiguraci reverzní proxy, nastavte reverzní proxy předat požadavky do aplikace.

## <a name="publish-to-a-folder"></a>Publikovat do složky

Příkaz [publikování dotnet](/dotnet/core/tools/dotnet-publish) zkompiluje kód aplikace a zkopíruje soubory potřebné ke spuštění aplikace do složky *publikování.* Při nasazování z `dotnet publish` visual studia dojde ke kroku automaticky před soubory zkopírovány do cíle nasazení.

### <a name="folder-contents"></a>Obsah složky

Složka *publikování* obsahuje jeden nebo více souborů sestavení aplikace, závislostí a volitelně zaběhu .NET.

Aplikaci .NET Core lze publikovat jako *samostatné nasazení* nebo nasazení závislé *na rámci*. Pokud je aplikace samostatná, jsou do složky *publikování* zahrnuty soubory sestavení, které obsahují zaběhu .NET. Pokud je aplikace závislá na rámci, soubory za běhu .NET nejsou zahrnuty, protože aplikace obsahuje odkaz na verzi rozhraní .NET, která je nainstalovaná na serveru. Výchozí model nasazení je závislý na rámci. Další informace naleznete v tématu [.NET Core application deployment](/dotnet/core/deploying/).

Kromě souborů *EXE* a *DLL* obsahuje složka *publikování* aplikace ASP.NET Core obvykle konfigurační soubory, statické datové zdroje a zobrazení MVC. Další informace naleznete v tématu <xref:host-and-deploy/directory-structure>.

## <a name="set-up-a-process-manager"></a>Nastavení vedoucího procesů

Aplikace ASP.NET Core je konzolová aplikace, která musí být spuštěna při spuštění serveru a restartování, pokud dojde k chybě. Chcete-li automatizovat spuštění a restartování, je vyžadován správce procesů. Nejběžnější procesní manažeři pro ASP.NET Core jsou:

* Linux
  * [Nginx](xref:host-and-deploy/linux-nginx)
  * [Apache](xref:host-and-deploy/linux-apache)
* Windows
  * [IIS](xref:host-and-deploy/iis/index)
  * [Služba systému Windows](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a>Nastavení reverzního proxy serveru

Pokud aplikace používá [kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache)nebo [IIS](xref:host-and-deploy/iis/index) lze použít jako reverzní proxy server. Reverzní proxy server přijímá požadavky HTTP z Internetu a předá je ke strel.

Konfigurace&mdash;s reverzním proxy&mdash;serverem nebo bez něj je podporovaná konfigurace hostování. Další informace naleznete v tématu [Kdy použít Kestrel s reverzním proxy serverem](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scénáře proxy serveru a vyrovnávání zatížení

Další konfigurace může být vyžadována pro aplikace hostované za proxy servery a nástroji pro vyrovnávání zatížení. Bez další konfigurace nemusí mít aplikace přístup k schématu (HTTP/HTTPS) a vzdálené ADRESE IP, ze které pochází požadavek. Další informace naleznete v [tématu Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a>Automatizace nasazení pomocí sady Visual Studio a MSBuild

Nasazení často vyžaduje další úkoly kromě kopírování výstupu z [dotnet publikovat](/dotnet/core/tools/dotnet-publish) na server. Například další soubory mohou být požadovány nebo vyloučeny ze složky *publikování.* Visual Studio používá MSBuild pro nasazení webu a MSBuild lze přizpůsobit tak, aby během nasazení dělal mnoho dalších úloh. Další informace najdete v tématu <xref:host-and-deploy/visual-studio-publish-profiles> a pomocí knihy sestavení [MSBuild a Team Foundation.](http://msbuildbook.com/)

Pomocí [funkce Publikovat web](xref:tutorials/publish-to-azure-webapp-using-vs) nebo [integrované podpory Gitu](xref:host-and-deploy/azure-apps/azure-continuous-deployment)lze aplikace nasadit přímo z Visual Studia do služby Azure App Service. Azure DevOps Services podporuje [průběžné nasazení do služby Azure App Service](/azure/devops/pipelines/targets/webapp). Další informace najdete [v tématu DevOps s ASP.NET core a Azure](xref:azure/devops/index).

## <a name="publish-to-azure"></a>Publikování aplikací do Azure

Pokyny <xref:tutorials/publish-to-azure-webapp-using-vs> k publikování aplikace do Azure pomocí Visual Studia najdete. Další příklad poskytuje [vytvoření webové aplikace ASP.NET Core v Azure](/azure/app-service/app-service-web-get-started-dotnet).

## <a name="publish-with-msdeploy-on-windows"></a>Publikovat pomocí msdeploy v systému Windows

Pokyny <xref:host-and-deploy/visual-studio-publish-profiles> k publikování aplikace pomocí profilu publikování v sadě Visual Studio, včetně příkazového řádku systému Windows pomocí příkazu [dotnet msbuild,](/dotnet/core/tools/dotnet-msbuild) najdete v tématu.

## <a name="internet-information-services-iis"></a>Internet Information Services (IIS)

Nasazení do Internetové informační služby (IIS) s konfigurací poskytovanou <xref:host-and-deploy/iis/index>souborem *web.config* naleznete v článcích v části .

## <a name="host-in-a-web-farm"></a>Hostování ve webové farmě

Informace o konfiguraci pro hostování aplikací ASP.NET Core v prostředí webové farmy (například nasazení <xref:host-and-deploy/web-farm>více instancí aplikace pro škálovatelnost) najdete v tématu .

## <a name="host-on-docker"></a>Hostitel v Dockeru

Další informace naleznete v tématu <xref:host-and-deploy/docker/index>.

## <a name="perform-health-checks"></a>Provádět kontroly stavu

Pomocí middlewaru kontroly stavu můžete provádět kontroly stavu aplikace a jejích závislostí. Další informace naleznete v tématu <xref:host-and-deploy/health-checks>.

## <a name="additional-resources"></a>Další zdroje

* <xref:test/troubleshoot>
* [ASP.NET Hosting](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Obecně platí, že nasazení aplikace ASP.NET Core do hostitelského prostředí:

* Nasadit publikovanou aplikaci do složky na hostitelském serveru.
* Nastavte správce procesů, který spustí aplikaci, když dorazí požadavky a restartuje aplikaci po pádu nebo restartování serveru.
* Pro konfiguraci reverzní proxy, nastavte reverzní proxy předat požadavky do aplikace.

## <a name="publish-to-a-folder"></a>Publikovat do složky

Příkaz [publikování dotnet](/dotnet/core/tools/dotnet-publish) zkompiluje kód aplikace a zkopíruje soubory potřebné ke spuštění aplikace do složky *publikování.* Při nasazování z `dotnet publish` visual studia dojde ke kroku automaticky před soubory zkopírovány do cíle nasazení.

### <a name="folder-contents"></a>Obsah složky

Složka *publikování* obsahuje jeden nebo více souborů sestavení aplikace, závislostí a volitelně zaběhu .NET.

Aplikaci .NET Core lze publikovat jako *samostatné nasazení* nebo nasazení závislé *na rámci*. Pokud je aplikace samostatná, jsou do složky *publikování* zahrnuty soubory sestavení, které obsahují zaběhu .NET. Pokud je aplikace závislá na rámci, soubory za běhu .NET nejsou zahrnuty, protože aplikace obsahuje odkaz na verzi rozhraní .NET, která je nainstalovaná na serveru. Výchozí model nasazení je závislý na rámci. Další informace naleznete v tématu [.NET Core application deployment](/dotnet/core/deploying/).

Kromě souborů *EXE* a *DLL* obsahuje složka *publikování* aplikace ASP.NET Core obvykle konfigurační soubory, statické datové zdroje a zobrazení MVC. Další informace naleznete v tématu <xref:host-and-deploy/directory-structure>.

## <a name="set-up-a-process-manager"></a>Nastavení vedoucího procesů

Aplikace ASP.NET Core je konzolová aplikace, která musí být spuštěna při spuštění serveru a restartování, pokud dojde k chybě. Chcete-li automatizovat spuštění a restartování, je vyžadován správce procesů. Nejběžnější procesní manažeři pro ASP.NET Core jsou:

* Linux
  * [Nginx](xref:host-and-deploy/linux-nginx)
  * [Apache](xref:host-and-deploy/linux-apache)
* Windows
  * [IIS](xref:host-and-deploy/iis/index)
  * [Služba systému Windows](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a>Nastavení reverzního proxy serveru

Pokud aplikace používá [kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache)nebo [IIS](xref:host-and-deploy/iis/index) lze použít jako reverzní proxy server. Reverzní proxy server přijímá požadavky HTTP z Internetu a předá je ke strel.

Konfigurace&mdash;s reverzním proxy&mdash;serverem nebo bez něj je podporovaná konfigurace hostování. Další informace naleznete v tématu [Kdy použít Kestrel s reverzním proxy serverem](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scénáře proxy serveru a vyrovnávání zatížení

Další konfigurace může být vyžadována pro aplikace hostované za proxy servery a nástroji pro vyrovnávání zatížení. Bez další konfigurace nemusí mít aplikace přístup k schématu (HTTP/HTTPS) a vzdálené ADRESE IP, ze které pochází požadavek. Další informace naleznete v [tématu Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a>Automatizace nasazení pomocí sady Visual Studio a MSBuild

Nasazení často vyžaduje další úkoly kromě kopírování výstupu z [dotnet publikovat](/dotnet/core/tools/dotnet-publish) na server. Například další soubory mohou být požadovány nebo vyloučeny ze složky *publikování.* Visual Studio používá MSBuild pro nasazení webu a MSBuild lze přizpůsobit tak, aby během nasazení dělal mnoho dalších úloh. Další informace najdete v tématu <xref:host-and-deploy/visual-studio-publish-profiles> a pomocí knihy sestavení [MSBuild a Team Foundation.](http://msbuildbook.com/)

Pomocí [funkce Publikovat web](xref:tutorials/publish-to-azure-webapp-using-vs) nebo [integrované podpory Gitu](xref:host-and-deploy/azure-apps/azure-continuous-deployment)lze aplikace nasadit přímo z Visual Studia do služby Azure App Service. Azure DevOps Services podporuje [průběžné nasazení do služby Azure App Service](/azure/devops/pipelines/targets/webapp). Další informace najdete [v tématu DevOps s ASP.NET core a Azure](xref:azure/devops/index).

## <a name="publish-to-azure"></a>Publikování aplikací do Azure

Pokyny <xref:tutorials/publish-to-azure-webapp-using-vs> k publikování aplikace do Azure pomocí Visual Studia najdete. Další příklad poskytuje [vytvoření webové aplikace ASP.NET Core v Azure](/azure/app-service/app-service-web-get-started-dotnet).

## <a name="publish-with-msdeploy-on-windows"></a>Publikovat pomocí msdeploy v systému Windows

Pokyny <xref:host-and-deploy/visual-studio-publish-profiles> k publikování aplikace pomocí profilu publikování v sadě Visual Studio, včetně příkazového řádku systému Windows pomocí příkazu [dotnet msbuild,](/dotnet/core/tools/dotnet-msbuild) najdete v tématu.

## <a name="internet-information-services-iis"></a>Internet Information Services (IIS)

Nasazení do Internetové informační služby (IIS) s konfigurací poskytovanou <xref:host-and-deploy/iis/index>souborem *web.config* naleznete v článcích v části .

## <a name="host-in-a-web-farm"></a>Hostování ve webové farmě

Informace o konfiguraci pro hostování aplikací ASP.NET Core v prostředí webové farmy (například nasazení <xref:host-and-deploy/web-farm>více instancí aplikace pro škálovatelnost) najdete v tématu .

## <a name="host-on-docker"></a>Hostitel v Dockeru

Další informace naleznete v tématu <xref:host-and-deploy/docker/index>.

## <a name="additional-resources"></a>Další zdroje

* <xref:test/troubleshoot>
* [ASP.NET Hosting](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end
