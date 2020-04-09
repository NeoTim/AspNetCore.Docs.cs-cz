---
title: Nástroje kontejnerů visual studia s jádrem ASP.NET
author: spboyer
description: Přečtěte si, jak pomocí nástrojů Visual Studio a Dockeru pro Windows kontejnerizovat aplikaci ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 09/12/2018
uid: host-and-deploy/docker/visual-studio-tools-for-docker
ms.openlocfilehash: 0e6747a3de220b97cc7a84f9cd42b0da54b57ee9
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664064"
---
# <a name="visual-studio-container-tools-with-aspnet-core"></a>Nástroje kontejnerů visual studia s jádrem ASP.NET

Visual Studio 2017 a novější verze podporují vytváření, ladění a spouštění kontejnerizovaných ASP.NET základních aplikací zaměřených na elementační .NET Core. Jsou podporovány kontejnery Windows i Linux.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/docker/visual-studio-tools-for-docker/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="prerequisites"></a>Požadavky

* [Docker pro Windows](https://docs.docker.com/docker-for-windows/install/)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s **úlohou pro vývoj napříč platformami .NET Core**

## <a name="installation-and-setup"></a>Instalace a nastavení

V případě instalace Dockeru nejprve zkontrolujte informace v [Dockeru pro Windows: Co je třeba vědět před instalací](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install). Dále nainstalujte [Docker pro Windows](https://docs.docker.com/docker-for-windows/install/).

**[Sdílené jednotky](https://docs.docker.com/docker-for-windows/#shared-drives)** v Dockeru pro Windows musí být nakonfigurovány tak, aby podporovaly mapování a ladění svazků. Klepněte pravým tlačítkem myši na ikonu Dockeru na hlavním panelu systému, vyberte **nastavení**a vyberte **sdílené jednotky**. Vyberte jednotku, na kterou Docker ukládá soubory. Klikněte na **Použít**.

![Dialogové okno pro výběr místního sdílení jednotky C pro kontejnery](visual-studio-tools-for-docker/_static/settings-shared-drives-win.png)

> [!TIP]
> Visual Studio 2017 verze 15.6 a novější výzvu, pokud sdílené jednotky nejsou **nakonfigurovány.**

## <a name="add-a-project-to-a-docker-container"></a>Přidání projektu do kontejneru Dockeru

Chcete-li kontejnerizovat ASP.NET projektu Core, musí projekt cílit na jádro .NET Core. Jsou podporovány kontejnery Linux i Windows.

Při přidávání podpory Dockeru do projektu zvolte kontejner Windows nebo Linux. Hostitel Dockeru musí mít stejný typ kontejneru. Chcete-li změnit typ kontejneru v spuštěné instanci Dockeru, klikněte pravým tlačítkem myši na ikonu Dockeru na systémové majebru a zvolte **Přepnout do kontejnerů Windows...** nebo **Přepnout na linuxové kontejnery...**.

### <a name="new-app"></a>Nová aplikace

Při vytváření nové aplikace s šablonami projektu **ASP.NET Core Web Application** zaškrtněte políčko **Povolit podporu Dockeru:**

![Zaškrtávací políčko Povolit podporu Dockeru](visual-studio-tools-for-docker/_static/enable-docker-support-check-box.png)

Pokud je cílový rámec .NET Core, rozevírací seznam **operačního systému** umožňuje výběr typu kontejneru.

### <a name="existing-app"></a>Existující aplikace

Pro ASP.NET základní projekty zaměřené na .NET Core existují dvě možnosti pro přidání podpory Dockeru prostřednictvím nástrojů. Otevřete projekt v sadě Visual Studio a zvolte jednu z následujících možností:

* V nabídce **Project** vyberte **Podpora Dockeru.**
* Klikněte pravým tlačítkem myši na projekt v **Průzkumníku řešení** a vyberte **Přidat** > **podporu Dockeru**.

Nástroje kontejneru Visual Studio nepodporují přidávání Dockeru do existujícího ASP.NET základního projektu cílení .NET Framework.

## <a name="dockerfile-overview"></a>Přehled souborů Dockeru

*Dockerfile*, recept pro vytvoření konečné image Dockeru, je přidán do kořenového adresáře projektu. Odkazovat na [odkaz Dockerfile](https://docs.docker.com/engine/reference/builder/) pro pochopení příkazů v něm. Tento konkrétní *Dockerfile* používá [vícestupňové sestavení](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) se čtyřmi odlišnými, pojmenovanými fázemi sestavení:

::: moniker range=">= aspnetcore-2.1"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile.original?highlight=1,6,14,17)]

Předchozí *soubor Dockerfile* je založen na image [microsoft/dotnet.](https://hub.docker.com/r/microsoft/dotnet/) Tato základní image obsahuje ASP.NET balíčky Core runtime a NuGet. Balíčky jsou just-in-time (JIT) zkompilovány ke zlepšení výkonu při spuštění.

Když je zaškrtnuto políčko **Konfigurovat pro protokol HTTPS** nového dialogového okna projektu, *dockerfile* zpřístupní dva porty. Jeden port se používá pro přenosy HTTP; druhý port se používá pro protokol HTTPS. Pokud políčko není zaškrtnuto, jeden port (80) je vystaven pro přenosy HTTP.

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.0/HelloDockerTools/Dockerfile?highlight=1,5,13,16)]

Předchozí *soubor Dockerfile* je založen na image [Microsoft/aspnetcore.](https://hub.docker.com/r/microsoft/aspnetcore/) Tato základní bitová kopie obsahuje ASP.NET balíčky Core NuGet, které jsou zkompilovány za čase (JIT) ke zlepšení výkonu při spuštění.

::: moniker-end

## <a name="add-container-orchestrator-support-to-an-app"></a>Přidání podpory orchestrátoru kontejnerů do aplikace

Visual Studio 2017 verze 15.7 nebo starší podporují [Docker Compose](https://docs.docker.com/compose/overview/) jako jediné řešení orchestrace kontejnerů. Artefakty Docker Compose jsou přidány pomocí **přidat** > **podporu Dockeru**.

Visual Studio 2017 verze 15.8 nebo novější přidat řešení orchestrace pouze v pokynech. Klepněte pravým tlačítkem myši na projekt v **Průzkumníku řešení** a vyberte **přidat** > **podporu orchestrátoru kontejnerů**. K dispozici jsou dvě různé možnosti: [Docker Compose](#docker-compose) a [Service Fabric](#service-fabric).

### <a name="docker-compose"></a>Docker Compose

Nástroje kontejneru Visual Studio přidat *docker-compose* projekt do řešení s následujícími soubory:

* *docker-compose.dcproj* &ndash; Soubor představující projekt. Obsahuje `<DockerTargetOS>` prvek určující operační ho operačního prvku, který má být použit.
* *.dockerignore:* &ndash; Seznam vzorů souborů a adresářů, které mají být vyloučeny při generování kontextu sestavení.
* *docker-compose.yml* &ndash; Základní [soubor Docker Compose](https://docs.docker.com/compose/overview/) slouží k definování kolekce `docker-compose build` `docker-compose run`bitových kopií postavených a spustit s a , v uvedeném pořadí.
* *docker-compose.override.yml* &ndash; Volitelný soubor, přečtený Docker Empose, s přepsáním konfigurace pro služby. Visual Studio `docker-compose -f "docker-compose.yml" -f "docker-compose.override.yml"` spustí sloučit tyto soubory.

Soubor *docker-compose.yml* odkazuje na název bitové kopie, která je vytvořena při spuštění projektu:

[!code-yaml[](visual-studio-tools-for-docker/samples/2.0/docker-compose.yml?highlight=5)]

V předchozím příkladu `image: hellodockertools` generuje image `hellodockertools:dev` při spuštění aplikace v režimu **ladění.** Obrázek se `hellodockertools:latest` vygeneruje, když se aplikace spustí v režimu **vydání.**

Předpona názvu bitové kopie s uživatelským `dockerhubusername/hellodockertools`jménem Docker [Hub](https://hub.docker.com/) (například), pokud je bitová kopie zasunuta do registru. Případně změňte název obrázku tak, aby zahrnoval `privateregistry.domain.com/hellodockertools`adresu URL soukromého registru (například) v závislosti na konfiguraci.

Pokud chcete různé chování na základě konfigurace sestavení (například Ladění nebo Vydání), přidejte soubory *docker-compose* specifické pro konfiguraci. Soubory by měly být pojmenovány podle konfigurace sestavení (například *docker-compose.vs.debug.yml* a *docker-compose.vs.release.yml*) a umístěny ve stejném umístění jako soubor *docker-compose-override.yml.* 

Pomocí přepsání souborů specifických pro konfiguraci můžete určit různá nastavení konfigurace (například proměnné prostředí nebo vstupní body) pro konfigurace sestavení ladění a vydání.

Pro Docker Compose zobrazit možnost ke spuštění v sadě Visual Studio, projekt dockeru musí být spuštění projektu.

### <a name="service-fabric"></a>Service Fabric

Kromě základních [požadavků](#prerequisites)vyžaduje orchestrace [service fabric](/azure/service-fabric/) následující požadavky:

* [Sada Microsoft Azure Service Fabric SDK](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK) verze 2.6 nebo novější
* Úloha **Vývoje Azure** od Visual Studia

Service Fabric nepodporuje spouštění linuxových kontejnerů v clusteru místního vývoje v systému Windows. Pokud projekt již používá kontejner Linux, Visual Studio vyzve k přepnutí do kontejnerů systému Windows.

Nástroje kontejneru sady Visual Studio plní následující úkoly:

* Přidá do řešení projekt aplikace * &lt;&gt;project_name* **aplikace.**
* Přidá *dockerfile* a *soubor .dockerignore* do projektu ASP.NET Core. Pokud *soubor Dockerfile* již existuje v projektu ASP.NET Core, je přejmenován na *Dockerfile.original*. Vytvoří se nový *soubor Dockerfile*, podobný následujícímu:

    [!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile)]

* Přidá `<IsServiceFabricServiceProject>` prvek do souboru *.csproj* projektu ASP.NET Core:

    [!code-xml[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/HelloDockerTools.csproj?name=snippet_IsServiceFabricServiceProject)]

* Přidá složku *PackageRoot* do projektu ASP.NET Core. Složka obsahuje manifest služby a nastavení pro novou službu.

Další informace najdete [v tématu Nasazení aplikace .NET v kontejneru Windows do Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container).

## <a name="debug"></a>Ladit

Vyberte **Docker** z rozevíracího okna ladění na panelu nástrojů a začněte ladit aplikaci. Zobrazení **Dockeru** v okně **Výstup** zobrazuje následující akce:

::: moniker range=">= aspnetcore-2.1"

* Značka *2.1-aspnetcore-runtime* bitové kopie *microsoft/dotnet* runtime je získána (pokud již není v mezipaměti). Bitová kopie nainstaluje ASP.NET core a .NET Core runtimes a přidružené knihovny. Je optimalizovaný pro spouštění aplikací ASP.NET Core v produkčním prostředí.
* Proměnná `ASPNETCORE_ENVIRONMENT` prostředí je `Development` nastavena na v rámci kontejneru.
* Jsou vystaveny dva dynamicky přiřazené porty: jeden pro protokol HTTP a jeden pro protokol HTTPS. Port přiřazený localhost může být `docker ps` dotazován pomocí příkazu.
* Aplikace se zkopíruje do kontejneru.
* Výchozí prohlížeč se spustí s ladicím programem připojeným ke kontejneru pomocí dynamicky přiřazeného portu.

Výsledná image Dockeru aplikace je označena jako *dev*. Obrázek je založen na značce *2.1-aspnetcore-runtime* základní bitové kopie *microsoft/dotnet.* Spusťte `docker images` příkaz v okně **Konzola správce balíčků** (PMC). Obrázky na zařízení jsou zobrazeny:

```console
REPOSITORY        TAG                     IMAGE ID      CREATED         SIZE
hellodockertools  dev                     d72ce0f1dfe7  30 seconds ago  255MB
microsoft/dotnet  2.1-aspnetcore-runtime  fcc3887985bb  6 days ago      255MB
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* *Microsoft/aspnetcore* runtime image je získán (pokud již není v mezipaměti).
* Proměnná `ASPNETCORE_ENVIRONMENT` prostředí je `Development` nastavena na v rámci kontejneru.
* Port 80 je vystavena a mapována na dynamicky přiřazený port pro localhost. Port je určen hostitelem Dockeru a může `docker ps` být dotazován pomocí příkazu.
* Aplikace se zkopíruje do kontejneru.
* Výchozí prohlížeč se spustí s ladicím programem připojeným ke kontejneru pomocí dynamicky přiřazeného portu.

Výsledná image Dockeru aplikace je označena jako *dev*. Obrázek je založen na základní bitové kopii *microsoft/aspnetcore.* Spusťte `docker images` příkaz v okně **Konzola správce balíčků** (PMC). Obrázky na zařízení jsou zobrazeny:

```console
REPOSITORY            TAG  IMAGE ID      CREATED        SIZE
hellodockertools      dev  5fafe5d1ad5b  4 minutes ago  347MB
microsoft/aspnetcore  2.0  c69d39472da9  13 days ago    347MB
```

::: moniker-end

> [!NOTE]
> *Obrázek dev* postrádá obsah aplikace, protože konfigurace **ladění** používají připojení svazku k zajištění iterativního prostředí. Chcete-li obrázek stisknout, použijte konfiguraci **vydání.**

Spusťte `docker ps` příkaz v PMC. Všimněte si, že aplikace běží pomocí kontejneru:

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   21 seconds ago      Up 19 seconds       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="edit-and-continue"></a>Upravit a pokračovat

Změny statických souborů a zobrazení Razor jsou automaticky aktualizovány bez nutnosti kroku kompilace. Proveďte změnu, uložte a aktualizujte prohlížeč, abyste aktualizaci zobrazili.

Změny souboru kódu vyžadují kompilaci a restartování Kestrel v rámci kontejneru. Po provedení změny `CTRL+F5` použijte k provedení procesu a spuštění aplikace v kontejneru. Kontejner Dockeru není znovu sestaven nebo zastaven. Spusťte `docker ps` příkaz v PMC. Všimněte si, že původní kontejner je stále spuštěn před 10 minutami:

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   10 minutes ago      Up 10 minutes       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="publish-docker-images"></a>Publikovat imitace Dockeru

Po dokončení cyklu vývoje a ladění aplikace nástroje kontejneru Visual Studio pomáhají při vytváření produkční image aplikace. Změňte rozbalovací verzi konfigurace na **Uvolnění** a vytvořte aplikaci. Nástroj získá image kompilace nebo publikování z Docker Hubu (pokud již není v mezipaměti). Bitová kopie je vytvořena s *nejnovější* značkou, kterou lze zasunout do soukromého registru nebo Docker Hubu.

Spusťte `docker images` příkaz v PMC a zobrazte seznam obrázků. Zobrazí se výstup podobný následujícímu:

::: moniker range=">= aspnetcore-2.1"

```console
REPOSITORY        TAG                     IMAGE ID      CREATED             SIZE
hellodockertools  latest                  e3984a64230c  About a minute ago  258MB
hellodockertools  dev                     d72ce0f1dfe7  4 minutes ago       255MB
microsoft/dotnet  2.1-sdk                 9e243db15f91  6 days ago          1.7GB
microsoft/dotnet  2.1-aspnetcore-runtime  fcc3887985bb  6 days ago          255MB
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

```console
REPOSITORY                  TAG     IMAGE ID      CREATED         SIZE
hellodockertools            latest  cd28f0d4abbd  12 seconds ago  349MB
hellodockertools            dev     5fafe5d1ad5b  23 minutes ago  347MB
microsoft/aspnetcore-build  2.0     7fed40fbb647  13 days ago     2.02GB
microsoft/aspnetcore        2.0     c69d39472da9  13 days ago     347MB
```

`microsoft/aspnetcore-build` Obrázky `microsoft/aspnetcore` a uvedené v předchozím výstupu `microsoft/dotnet` jsou nahrazeny obrázky od .NET Core 2.1. Další informace naleznete [v oznámení o migraci úložišť Dockeru](https://github.com/aspnet/Announcements/issues/298).

::: moniker-end

> [!NOTE]
> Příkaz `docker images` vrátí zprostředkující bitové kopie s * \<* názvy úložišť a značkami označenými jako žádné>(nejsou uvedeny výše). Tyto nepojmenované bitové kopie jsou vytvářeny [vícestupňové sestavení](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) *Dockerfile*. Zlepšují účinnost vytváření konečného obrazu&mdash;pouze potřebné vrstvy jsou přestavěny, když dojde ke změnám. Pokud již nejsou potřebné mezilehlé bitové kopie, odstraňte je pomocí příkazu [rmi dockeru.](https://docs.docker.com/engine/reference/commandline/rmi/)

Může existovat očekávání, že produkční nebo release image bude menší ve srovnání s *obrázkem dev.* Z důvodu mapování svazku ladicí program a aplikace byly spuštěny z místního počítače a není v kontejneru. *Nejnovější* obrázek zabalil kód aplikace potřebný ke spuštění aplikace na hostitelském počítači. Delta je tedy velikost kódu aplikace.

## <a name="additional-resources"></a>Další zdroje

* [Vývoj kontejnerů pomocí sady Visual Studio](/visualstudio/containers)
* [Azure Service Fabric: Příprava vývojového prostředí](/azure/service-fabric/service-fabric-get-started)
* [Nasazení aplikace .NET v kontejneru Windows do Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container)
* [Řešení potíží při vývoji v sadě Visual Studio pomocí Dockeru](/azure/vs-azure-tools-docker-troubleshooting-docker-errors)
* [Úložiště GitHub nástrojů kontejneru Visual Studia](https://github.com/Microsoft/DockerTools)
