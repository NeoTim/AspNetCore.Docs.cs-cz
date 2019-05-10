---
title: Visual Studio Tools for Docker s ASP.NET Core
author: spboyer
description: Zjistěte, jak kontejnerizovat aplikace ASP.NET Core pomocí nástroje Visual Studio 2017 a Docker pro Windows.
ms.author: scaddie
ms.custom: mvc
ms.date: 09/12/2018
uid: host-and-deploy/docker/visual-studio-tools-for-docker
ms.openlocfilehash: 3bf3d8d0a627d97090e3ce9fef7e380f03c7626d
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64901308"
---
# <a name="visual-studio-tools-for-docker-with-aspnet-core"></a>Visual Studio Tools for Docker s ASP.NET Core

Visual Studio 2017 podporuje vytváření, ladění a spouštění kontejnerizovaných ASP.NET Core aplikace cílí na .NET Core. Jsou podporovány kontejnery Windows i Linuxu.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/docker/visual-studio-tools-for-docker/samples) ([stažení](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Požadavky

* [Docker pro Windows](https://docs.docker.com/docker-for-windows/install/)
* [Visual Studio 2017](https://visualstudio.microsoft.com) s **vývoj pro různé platformy .NET Core** pracovního vytížení

## <a name="installation-and-setup"></a>Instalace a nastavení

Pro instalaci Dockeru, nejprve zkontrolujte informace na [Docker pro Windows: Co potřebujete vědět před instalací](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install). Dále nainstalujte [Docker pro Windows](https://docs.docker.com/docker-for-windows/install/).

**[Sdílené jednotky](https://docs.docker.com/docker-for-windows/#shared-drives)**  v Docker pro Windows musí být nakonfigurované pro podporu mapování svazku a ladění. Klikněte pravým tlačítkem na ikonu Dockeru na hlavním panelu, vyberte **nastavení**a vyberte **sdílené jednotky**. Vyberte jednotku, kde Docker ukládá soubory. Klikněte na tlačítko **Použít**.

![Dialogové okno pro výběr místní jednotky C sdílení pro kontejnery](visual-studio-tools-for-docker/_static/settings-shared-drives-win.png)

> [!TIP]
> Visual Studio 2017 verze 15.6 a vyšší výzvu, při **sdílené jednotky** nejsou nakonfigurované.

## <a name="add-a-project-to-a-docker-container"></a>Přidat projekt do kontejneru Dockeru

Kontejnerizaci projektu aplikace ASP.NET Core, musí projekt cílit na .NET Core. Jsou podporovány kontejnery Linux i Windows.

Při přidávání podpory Docker do projektu, zvolte Windows nebo Linuxem kontejneru. Hostitele Docker musí běžet stejný typ kontejneru. Chcete-li změnit typ kontejneru v běžící instanci Dockeru, klikněte pravým tlačítkem na ikonu Dockeru na hlavním panelu a vyberte **přepnout na kontejnery Windows...**  nebo **přepnout na kontejnery Linuxu...** .

### <a name="new-app"></a>Nová aplikace

Při vytváření nové aplikace s **webové aplikace ASP.NET Core** šablony projektu, vyberte **povolit podporu Dockeru** zaškrtávací políčko:

![Povolit podporu Dockeru zaškrtávací políčko](visual-studio-tools-for-docker/_static/enable-docker-support-check-box.png)

Pokud je verze cílového rozhraní .NET Core **OS** umožňuje rozevírací seznam pro výběr typu kontejneru.

### <a name="existing-app"></a>Existující aplikace

Pro projekty ASP.NET Core, jejichž cílem je .NET Core existují dvě možnosti pro přidání podpory Dockeru pomocí nástrojů. Otevřete projekt v sadě Visual Studio a zvolte jednu z následujících možností:

* Vyberte **podporu Dockeru** z **projektu** nabídky.
* Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a vyberte **přidat** > **podporu Dockeru**.

Visual Studio Tools for Docker nepodporuje přidávání Dockeru do existujícího projektu ASP.NET Core cílí na rozhraní .NET Framework.

## <a name="dockerfile-overview"></a>Přehled souboru Dockerfile

A *soubor Dockerfile*, předpisu pro vytvoření finální image Dockeru, je přidán do kořenového adresáře projektu. Odkazovat na [odkaz na soubor Dockerfile](https://docs.docker.com/engine/reference/builder/) pro pochopení příkazy v rámci něj. Tento konkrétní *soubor Dockerfile* používá [vícefázových sestavení](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) s čtyřmi distinct, s názvem fáze sestavení umísťují:

::: moniker range=">= aspnetcore-2.1"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile.original?highlight=1,6,14,17)]

Předchozí *soubor Dockerfile* vychází [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet/) bitové kopie. Tato základní image obsahuje modul runtime ASP.NET Core a balíčky NuGet. Balíčky jsou just-in-time (JIT) zkompilována do zlepšit výkon při spuštění.

Při nové dialogu projekt **konfigurace pro protokol HTTPS** zaškrtávací políčko zaškrtnuto, *soubor Dockerfile* poskytuje dva porty. Jeden port se používá pro přenos HTTP; jiný port se používá pro protokol HTTPS. Pokud políčko není zaškrtnuto, jeden port (80) je přístupný pro přenosy pomocí protokolu HTTP.

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.0/HelloDockerTools/Dockerfile?highlight=1,5,13,16)]

Předchozí *soubor Dockerfile* vychází [microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore/) bitové kopie. Tato základní image obsahuje balíčky ASP.NET Core NuGet, které jsou just-in-time (JIT) zkompilována do zlepšit výkon při spuštění.

::: moniker-end

## <a name="add-container-orchestrator-support-to-an-app"></a>Přidat podporu orchestrátoru kontejnerů do aplikace

Visual Studio 2017 verze 15.7 nebo starší podporují [Docker Compose](https://docs.docker.com/compose/overview/) jako řešení Orchestrace kontejnerů jediným. Docker Compose artefakty se přidají přes **přidat** > **podporu Dockeru**.

Visual Studio 2017 verze 15,8 nebo novější přidat řešení Orchestrace jenom v případě, že vydal pokyn pro nástroje. Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a vyberte **přidat** > **podporu Orchestrátoru kontejnerů**. Nabízíme dvě různé možnosti: [Docker Compose](#docker-compose) a [Service Fabric](#service-fabric).

### <a name="docker-compose"></a>Docker Compose

Přidejte Visual Studio Tools for Docker *docker-compose* projektu do řešení se následující soubory:

* *docker compose.dcproj* &ndash; soubor, který projekt. Zahrnuje `<DockerTargetOS>` prvek určující operačního systému, který se má použít.
* *.dockerignore* &ndash; obsahuje seznam vzorů souborů a adresářů pro vyloučení při generování kontext sestavení.
* *docker-compose.yml* &ndash; základní [Docker Compose](https://docs.docker.com/compose/overview/) soubor se používá k definování kolekce imagí vytvořili a spustili pomocí `docker-compose build` a `docker-compose run`v uvedeném pořadí.
* *docker-compose.override.yml* &ndash; volitelný soubor ke čtení pomocí Docker Compose, s konfigurací přepsání pro služby. Visual Studio provede `docker-compose -f "docker-compose.yml" -f "docker-compose.override.yml"` sloučit tyto soubory.

*Docker-compose.yml* soubor odkazuje na název obrázku, který je vytvořen při spuštění projektu:

[!code-yaml[](visual-studio-tools-for-docker/samples/2.0/docker-compose.yml?highlight=5)]

V předchozím příkladu `image: hellodockertools` generuje obrázek `hellodockertools:dev` při spuštění aplikace **ladění** režimu. `hellodockertools:latest` Image se vygeneruje, když aplikace běží v **vydání** režimu.

Před názvem obrázku [Docker Hubu](https://hub.docker.com/) uživatelské jméno (třeba `dockerhubusername/hellodockertools`) Pokud je image do registru. Můžete také změnit název image přidat adresu URL privátního registru (například `privateregistry.domain.com/hellodockertools`) v závislosti na konfiguraci.

Pokud chcete různé chování v závislosti na konfiguraci sestavení (například ladění nebo vydání), přidejte určených pro konfigurace *docker-compose* soubory. Soubory by měly být pojmenovány podle konfigurace sestavení (například *docker compose.vs.debug.yml* a *docker compose.vs.release.yml*) a je umístěná ve stejném umístění jako *docker compose override.yml* souboru. 

Použití souborů určených pro konfigurace přepsání, můžete zadat jinou konfiguraci nastavení (jako jsou proměnné prostředí a vstupní body) u sestavení konfigurace Debug a Release.

### <a name="service-fabric"></a>Service Fabric

Kromě základní [požadavky](#prerequisites), [Service Fabric](/azure/service-fabric/) řešení Orchestrace požadavky splněné následující požadavky:

* [Microsoft Azure Service Fabric SDK](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK) version 2.6 nebo novější
* Visual Studio 2017 **vývoj pro Azure** pracovního vytížení

Service Fabric nepodporuje spuštěné kontejnery Linuxu v místním vývojovém clusteru na Windows. Pokud projekt již používá kontejner Linuxu, Visual Studio vyzve k přepnout na kontejnery Windows.

Visual Studio Tools for Docker provádět následující úlohy:

* Přidá  *&lt;project_name&gt;aplikace* **aplikace Service Fabric** projektu do řešení.
* Přidá *soubor Dockerfile* a *.dockerignore* soubor do projektu ASP.NET Core. Pokud *soubor Dockerfile* již existuje v projektu ASP.NET Core se přejmenovalo na *Dockerfile.original*. Nový *soubor Dockerfile*, podobně jako následujícím vytvoření:

    [!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile)]

* Přidá `<IsServiceFabricServiceProject>` prvek do projektu ASP.NET Core *.csproj* souboru:

    [!code-xml[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/HelloDockerTools.csproj?name=snippet_IsServiceFabricServiceProject)]

* Přidá *PackageRoot* složku pro projekt ASP.NET Core. Složka obsahuje manifest služby a nastavení pro novou službu.

Další informace najdete v tématu [nasazení aplikace .NET v kontejneru Windows do Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container).

## <a name="debug"></a>Ladit

Vyberte **Docker** z ladění rozevírací seznam v panelu nástrojů a spusťte ladění aplikace. **Docker** zobrazení **výstup** okno zobrazuje probíhají následující akce:

::: moniker range=">= aspnetcore-2.1"

* *Modulu runtime 2.1 aspnetcore* značku *microsoft/dotnet* bitové kopie modulu runtime je získali (pokud ještě není v mezipaměti). Na obrázku nainstaluje moduly runtime ASP.NET Core a .NET Core a přidruženými knihovnami. Je optimalizovaný pro spouštění aplikací ASP.NET Core v produkčním prostředí.
* `ASPNETCORE_ENVIRONMENT` Proměnná prostředí je nastavená na `Development` v kontejneru.
* Dva porty dynamicky přidělovanou vystaveny: jeden pro HTTP a jeden pro protokol HTTPS. Port přiřazen k místnímu hostiteli může být dotázán pomocí `docker ps` příkazu.
* Aplikace se zkopíruje do kontejneru.
* Spustí výchozí prohlížeč se ladicí program připojený ke kontejneru pomocí dynamicky přiřazeného portu.

Výsledný image Dockeru aplikace je označený jako *dev*. Na obrázku je založen na *modulu runtime 2.1 aspnetcore* značku *microsoft/dotnet* základní image. Spustit `docker images` v příkaz **Konzola správce balíčků** okno (PMC). Image na počítači se zobrazí:

```console
REPOSITORY        TAG                     IMAGE ID      CREATED         SIZE
hellodockertools  dev                     d72ce0f1dfe7  30 seconds ago  255MB
microsoft/dotnet  2.1-aspnetcore-runtime  fcc3887985bb  6 days ago      255MB
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* *Microsoft/aspnetcore* bitové kopie modulu runtime je získali (pokud ještě není v mezipaměti).
* `ASPNETCORE_ENVIRONMENT` Proměnná prostředí je nastavená na `Development` v kontejneru.
* Port 80 je vystavena a mapují na dynamicky přidělovanou port pro místního hostitele. Port, který je určený hostitelem Dockeru a může být dotázán pomocí `docker ps` příkazu.
* Aplikace se zkopíruje do kontejneru.
* Spustí výchozí prohlížeč se ladicí program připojený ke kontejneru pomocí dynamicky přiřazeného portu.

Výsledný image Dockeru aplikace je označený jako *dev*. Na obrázku je založen na *microsoft/aspnetcore* základní image. Spustit `docker images` v příkaz **Konzola správce balíčků** okno (PMC). Image na počítači se zobrazí:

```console
REPOSITORY            TAG  IMAGE ID      CREATED        SIZE
hellodockertools      dev  5fafe5d1ad5b  4 minutes ago  347MB
microsoft/aspnetcore  2.0  c69d39472da9  13 days ago    347MB
```

::: moniker-end

> [!NOTE]
> *Dev* image chybí obsah aplikace jako **ladění** konfigurace používají připojení svazku pro zajištění iterativní prostředí. Chcete-li odeslat image, použijte **vydání** konfigurace.

Spustit `docker ps` příkazu v konzole PMC. Všimněte si, že je aplikace spuštěna pomocí kontejneru:

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   21 seconds ago      Up 19 seconds       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="edit-and-continue"></a>Upravit a pokračovat

Změny statických souborů a zobrazeními Razor se automaticky aktualizují bez nutnosti kompilační krok. Proveďte požadovanou změnu, uložte a aktualizujte prohlížeč, chcete-li zobrazit aktualizace.

Úpravy souborů kódu vyžadují kompilace a restartování Kestrel v kontejneru. Po provedení změn, použijte `CTRL+F5` provést proces a spusťte aplikaci v rámci kontejneru. Kontejner Dockeru se znovu sestavit nebo zastavená. Spustit `docker ps` příkazu v konzole PMC. Všimněte si, že původní kontejneru je stále spuštěna od 10 minut před:

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   10 minutes ago      Up 10 minutes       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="publish-docker-images"></a>Publikování Image Dockeru

Po dokončení cyklu vývoje a ladění aplikace Visual Studio Tools for Docker pomáhají při vytváření bitové kopie produkční aplikace. Změna konfigurace rozevíracího seznamu **vydání** a sestavení aplikace. Nástroje získá kompilace/publikování image z Docker Hubu (pokud to nebude již v mezipaměti). Image se vytvářejí s *nejnovější* značky, který může doručit bez vyžádání do privátního registru nebo centra Dockeru.

Spustit `docker images` příkazu v konzole PMC zobrazíte seznam imagí. Zobrazí se výstup podobný následujícímu:

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

`microsoft/aspnetcore-build` a `microsoft/aspnetcore` kopiemi uvedenými v předchozím výstupu jsou nahrazeny `microsoft/dotnet` Image od .NET Core 2.1. Další informace najdete v tématu [oznámení migrace úložiště Dockeru](https://github.com/aspnet/Announcements/issues/298).

::: moniker-end

> [!NOTE]
> `docker images` Příkaz vrátí zprostředkující obrázků s názvy úložišť a značky identifikována jako  *\<žádný >* (které nejsou uvedené výše). Tyto nepojmenované bitové kopie jsou vytvářeny [vícefázových sestavení](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) *soubor Dockerfile*. Pomáhají zvýšit efektivitu vytváření konečném obrazu&mdash;při změnách se znovu sestavit pouze nezbytné vrstvy. Pokud zprostředkující bitové kopie jsou už je nepotřebujete, odstraňte je pomocí [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) příkazu.

Může být očekávání pro produkci nebo verze image, která se má menší velikosti oproti tomu na *dev* bitové kopie. Z důvodu mapování svazku byly spuštěné ladicího programu a aplikace z místního počítače a není v rámci kontejneru. *Nejnovější* image se zabalit kód potřebné aplikace a spusťte tak aplikaci na hostitelském počítači. Delta proto je velikost kódu aplikace.

## <a name="additional-resources"></a>Další zdroje

* [Kontejner vývoj pomocí sady Visual Studio](/visualstudio/containers)
* [Azure Service Fabric: Příprava vývojového prostředí](/azure/service-fabric/service-fabric-get-started)
* [Nasazení aplikace .NET v kontejneru Windows do Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container)
* [Řešení potíží s Visual Studio 2017 vývoj aplikací pomocí Dockeru](/azure/vs-azure-tools-docker-troubleshooting-docker-errors)
* [Visual Studio Tools pro úložiště GitHub Dockeru](https://github.com/Microsoft/DockerTools)
