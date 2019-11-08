---
title: ASP.NET Core hostitele a nasazení
author: guardrex
description: Naučte se nastavit hostující prostředí a nasazovat aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
uid: host-and-deploy/index
ms.openlocfilehash: aa6e3da5f40211535037c671de913ab3219f063a
ms.sourcegitcommit: 67116718dc33a7a01696d41af38590fdbb58e014
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73799339"
---
# <a name="host-and-deploy-aspnet-core"></a>ASP.NET Core hostitele a nasazení

Obecně nasazení aplikace ASP.NET Core do hostitelského prostředí:

* Nasaďte publikovanou aplikaci do složky na hostitelském serveru.
* Nastavte správce procesů, který spustí aplikaci, když žádosti dorazí a restartuje aplikaci po jejím zhroucení nebo restartování serveru.
* Pro konfiguraci reverzního proxy serveru nastavte reverzní proxy server pro přeposílání požadavků do aplikace.

## <a name="publish-to-a-folder"></a>Publikovat do složky

Příkaz [dotnet Publish](/dotnet/core/tools/dotnet-publish) zkompiluje kód aplikace a zkopíruje soubory potřebné ke spuštění aplikace do složky pro *publikování* . Při nasazování ze sady Visual Studio probíhá `dotnet publish` krok automaticky předtím, než se soubory zkopírují do cíle nasazení.

### <a name="folder-contents"></a>Obsah složky

Složka *Publish* obsahuje jeden nebo více souborů sestavení aplikace, závislosti a volitelně rozhraní .NET Runtime.

Aplikace .NET Core se dá publikovat jako *samostatné nasazení* nebo *nasazení závislé na rozhraní*. Pokud je aplikace samostatná, soubory sestavení obsahující modul runtime .NET jsou zahrnuty ve složce pro *publikování* . Pokud je aplikace závislá na rozhraní, soubory modulu runtime .NET nejsou zahrnuty, protože aplikace odkazuje na verzi rozhraní .NET, která je nainstalována na serveru. Výchozí model nasazení je závislý na rozhraní. Další informace najdete v tématu [nasazení aplikace .NET Core](/dotnet/core/deploying/).

Kromě souborů *. exe* a *. dll* složka pro *publikování* pro aplikaci ASP.NET Core obvykle obsahuje konfigurační soubory, statické prostředky a zobrazení MVC. Další informace najdete v tématu <xref:host-and-deploy/directory-structure>.

## <a name="set-up-a-process-manager"></a>Nastavení správce procesů

ASP.NET Core aplikace je Konzolová aplikace, která se musí spustit, když se Server spustí a restartuje, pokud dojde k chybě. Pro automatizaci spuštění a restartování je vyžadován správce procesů. Nejběžnějšími správci procesů pro ASP.NET Core jsou:

* Linux
  * [Nginx](xref:host-and-deploy/linux-nginx)
  * [Webový](xref:host-and-deploy/linux-apache)
* Windows
  * [SLUŽBU](xref:host-and-deploy/iis/index)
  * [Služba systému Windows](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a>Nastavení reverzního proxy serveru

Pokud aplikace používá server [Kestrel](xref:fundamentals/servers/kestrel) , [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache)nebo [IIS](xref:host-and-deploy/iis/index) , dá se použít jako reverzní proxy server. Reverzní proxy server přijímá požadavky HTTP z Internetu a přesměruje je na Kestrel.

Konfigurace hostování je&mdash;s nebo bez&mdash;proxy server s reverzním. Další informace najdete v tématu [kdy používat Kestrel s reverzním proxy serverem](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scénáře proxy serveru a nástroje pro vyrovnávání zatížení

Pro aplikace hostované za proxy servery a nástroji pro vyrovnávání zatížení může být vyžadována další konfigurace. Bez další konfigurace může aplikace mít přístup ke schématu (HTTP/HTTPS) a vzdálené IP adrese, kde požadavek vznikl. Další informace najdete v tématu [konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a>Použití sady Visual Studio a nástroje MSBuild k automatizaci nasazení

Nasazení často vyžaduje další úlohy kromě kopírování výstupu z [dotnet Publish](/dotnet/core/tools/dotnet-publish) na server. Například další soubory mohou být vyžadovány nebo vyloučeny ze složky pro *publikování* . Visual Studio používá MSBuild pro nasazení webu a MSBuild se dá přizpůsobit tak, aby během nasazení provedl mnoho dalších úloh. Další informace naleznete v tématu <xref:host-and-deploy/visual-studio-publish-profiles> a v příručce pro [sestavení pomocí nástroje MSBuild a Team Foundation](http://msbuildbook.com/) .

Pomocí [funkce Publikovat web](xref:tutorials/publish-to-azure-webapp-using-vs) nebo [integrovanou podporu pro Git](xref:host-and-deploy/azure-apps/azure-continuous-deployment)je možné aplikace nasadit přímo ze sady Visual Studio do Azure App Service. Azure DevOps Services podporuje [průběžné nasazování do Azure App Service](/azure/devops/pipelines/targets/webapp). Další informace najdete v tématu [DevOps with ASP.NET Core a Azure](xref:azure/devops/index).

## <a name="publish-to-azure"></a>Publikování do Azure

Pokyny k publikování aplikace do Azure pomocí sady Visual Studio najdete v tématu <xref:tutorials/publish-to-azure-webapp-using-vs>. K dispozici je další příklad [Vytvoření webové aplikace ASP.NET Core v Azure](/azure/app-service/app-service-web-get-started-dotnet).

## <a name="publish-with-msdeploy-on-windows"></a>Publikování pomocí MSDeploy ve Windows

Pokyny k publikování aplikace pomocí profilu publikování sady Visual Studio, včetně příkazového řádku Windows pomocí příkazu [dotnet MSBuild](/dotnet/core/tools/dotnet-msbuild) , najdete v tématu <xref:host-and-deploy/visual-studio-publish-profiles>.

## <a name="internet-information-services-iis"></a>Internet Information Services (IIS)

Pro nasazení, která se Internetová informační služba (IIS) s konfigurací poskytnutou souborem *Web. config* , se podívejte na články v části <xref:host-and-deploy/iis/index>.

## <a name="host-in-a-web-farm"></a>Hostování ve webové farmě

Informace o konfiguraci pro hostování ASP.NET Core aplikací v prostředí webové farmy (například nasazení více instancí aplikace v rámci škálovatelnosti) najdete v tématu <xref:host-and-deploy/web-farm>.

::: moniker range=">= aspnetcore-2.2"

## <a name="perform-health-checks"></a>Provádět kontroly stavu

Pomocí middleware pro kontrolu stavu můžete provádět kontroly stavu aplikace a jejích závislostí. Další informace najdete v tématu <xref:host-and-deploy/health-checks>.

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* <xref:host-and-deploy/docker/index>
* <xref:test/troubleshoot>
