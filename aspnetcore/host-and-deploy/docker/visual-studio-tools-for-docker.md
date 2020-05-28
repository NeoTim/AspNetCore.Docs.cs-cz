---
Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

---
# <a name="visual-studio-container-tools-with-aspnet-core"></a>Nástroje kontejneru sady Visual Studio s ASP.NET Core

Visual Studio 2017 a novější verze podporují sestavování, ladění a spouštění kontejnerových ASP.NET Core aplikací cílících na .NET Core. Podporují se kontejnery Windows i Linux.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/docker/visual-studio-tools-for-docker/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Požadavky

* [Docker for Windows](https://docs.docker.com/docker-for-windows/install/)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s úlohou **vývoje .NET Core pro různé platformy**

## <a name="installation-and-setup"></a>Instalace a nastavení

V případě instalace Docker si nejdříve přečtěte informace v části [Docker for Windows: co potřebujete znát před instalací](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)nástroje. Dále nainstalujte [Docker pro Windows](https://docs.docker.com/docker-for-windows/install/).

**[Sdílené jednotky](https://docs.docker.com/docker-for-windows/#shared-drives)** v Docker for Windows musí být nakonfigurovány tak, aby podporovaly mapování svazků a ladění. Klikněte pravým tlačítkem myši na ikonu Docker systémového panelu, vyberte **Nastavení**a vyberte **sdílené jednotky**. Vyberte jednotku, kde Docker ukládá soubory. Klikněte na **Použít**.

![Dialog pro výběr místního sdílení jednotky C pro kontejnery](visual-studio-tools-for-docker/_static/settings-shared-drives-win.png)

> [!TIP]
> Visual Studio 2017 verze 15,6 a pozdější dotaz, pokud nejsou nakonfigurovány **sdílené jednotky** .

## <a name="add-a-project-to-a-docker-container"></a>Přidání projektu do kontejneru Docker

Aby bylo možné projekt ASP.NET Core kontejnerizace, musí projekt cílit na .NET Core. Jsou podporovány kontejnery Linux i Windows.

Když do projektu přidáte podporu Docker, vyberte buď kontejner Windows, nebo Linux. Hostitel Docker musí používat stejný typ kontejneru. Chcete-li změnit typ kontejneru v běžící instanci Docker, klikněte pravým tlačítkem myši na ikonu Docker panelu systému a vyberte možnost **Přepnout na kontejnery Windows...** nebo **Přepnout na kontejnery platformy Linux..**..

### <a name="new-app"></a>Nová aplikace

Při vytváření nové aplikace pomocí šablon projektů **ASP.NET Core webové aplikace** zaškrtněte políčko **Povolit podporu Docker** :

![Zaškrtávací políčko Povolit podporu Docker](visual-studio-tools-for-docker/_static/enable-docker-support-check-box.png)

Pokud je cílová architektura rozhraní .NET Core, rozevírací seznam **OS** umožňuje výběr typu kontejneru.

### <a name="existing-app"></a>Existující aplikace

Pro ASP.NET Core projekty cílené na .NET Core existují dvě možnosti, jak přidat podporu Docker prostřednictvím nástrojů. Otevřete projekt v aplikaci Visual Studio a vyberte jednu z následujících možností:

* V nabídce **projekt** vyberte možnost **Podpora Docker** .
* Klikněte pravým tlačítkem na projekt v **Průzkumník řešení** a vyberte **Přidat**  >  **podporu Docker**.

Nástroje kontejnerů sady Visual Studio nepodporují přidávání Docker do existující ASP.NET Core projektu cílících na .NET Framework.

## <a name="dockerfile-overview"></a>Souboru Dockerfile – přehled

Do kořenového adresáře projektu se přidá recept pro vytvoření finální image Docker *souboru Dockerfile*. Porozumění příkazům, které jsou v něm, najdete v [referenčních informacích k souboru Dockerfile](https://docs.docker.com/engine/reference/builder/) . Tento konkrétní *souboru Dockerfile* používá [sestavení s více fázemi](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) se čtyřmi odlišnými pojmenovanými fázemi sestavení:

::: moniker range=">= aspnetcore-2.1"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile.original?highlight=1,6,14,17)]

Předchozí *souboru Dockerfile* je založen na obrázku [Microsoft/dotNET](https://hub.docker.com/r/microsoft/dotnet/) . Tato základní image zahrnuje ASP.NET Core Runtime a balíčky NuGet. Balíčky jsou kompilovány JIT (just-in-time), aby se zlepšil výkon při spuštění.

Když je zaškrtnuté políčko **Konfigurovat pro protokol HTTPS** v dialogovém okně Nový projekt, *souboru Dockerfile* zpřístupňuje dva porty. Pro přenosy HTTP se používá jeden port; druhý port se používá pro protokol HTTPS. Pokud políčko není zaškrtnuté, bude pro přenosy HTTP vystaven jeden port (80).

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.0/HelloDockerTools/Dockerfile?highlight=1,5,13,16)]

Předchozí *souboru Dockerfile* vychází z image [Microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore/) . Tato základní image obsahuje ASP.NET Core balíčky NuGet, které jsou kompilovány JIT (just-in-time), aby se zlepšil výkon při spuštění.

::: moniker-end

## <a name="add-container-orchestrator-support-to-an-app"></a>Přidat do aplikace podporu nástroje Orchestrator pro kontejner

Sady Visual Studio 2017 verze 15,7 nebo novější podporují [Docker Compose](https://docs.docker.com/compose/overview/) jako jediné řešení orchestrace kontejnerů. Artefakty Docker Compose se přidávají prostřednictvím **Přidat**  >  **podporu Docker**.

Visual Studio 2017 verze 15,8 nebo novější přidat řešení orchestrace pouze v případě, že je pokyn. Klikněte pravým tlačítkem na projekt v **Průzkumník řešení** a vyberte **Přidat**  >  **kontejner Orchestrator support**. K dispozici jsou následující možnosti: 

* [Docker Compose](#docker-compose)
* [Service Fabric](#service-fabric)
* [Kubernetes/Helm](https://helm.sh/)

### <a name="docker-compose"></a>Docker Compose

Nástroje kontejnerů sady Visual Studio přidají do řešení projekt *Docker-sestavení* s následujícími soubory:

* *Docker-tváře. dcproj*: soubor představující projekt. Obsahuje `<DockerTargetOS>` element určující operační systém, který se má použít.
* *. dockerignore*: vypíše vzor souborů a adresářů, které mají být vyloučeny při generování kontextu sestavení.
* *Docker-Compose. yml*: základní [Docker Compose](https://docs.docker.com/compose/overview/) soubor, který slouží k definování kolekce imagí sestavených a spuštěných s `docker-compose build` a v `docker-compose run` uvedeném pořadí.
* *Docker-Compose. override. yml*: volitelný soubor, který čte Docker Compose, s přepsáním konfigurace pro služby. Visual Studio provede `docker-compose -f "docker-compose.yml" -f "docker-compose.override.yml"` sloučení těchto souborů.

Soubor *Docker-Compose. yml* odkazuje na název bitové kopie, která je vytvořena při spuštění projektu:

[!code-yaml[](visual-studio-tools-for-docker/samples/2.0/docker-compose.yml?highlight=5)]

V předchozím příkladu `image: hellodockertools` vygeneruje obrázek, `hellodockertools:dev` když aplikace běží v režimu **ladění** . `hellodockertools:latest`Obrázek se vygeneruje, když aplikace běží v režimu **vydání** .

Pokud je image vložená do registru, zadejte předponu názvu image k uživatelskému jménu [Docker](https://hub.docker.com/) (například `dockerhubusername/hellodockertools` ). Případně změňte název bitové kopie tak, aby zahrnoval adresu URL privátního registru (například `privateregistry.domain.com/hellodockertools` ) v závislosti na konfiguraci.

Pokud chcete jiné chování na základě konfigurace sestavení (například ladění nebo vydaná verze), přidejte soubory *Docker-skládání* specifické pro konfiguraci. Soubory by měly být pojmenovány podle konfigurace sestavení (například *Docker-Compose. vs. Debug. yml* a *Docker-Compose. vs. Release. yml*) a umístěny do stejného umístění jako soubor *Docker-Compose-override. yml* . 

Pomocí souborů přepsání specifických pro konfiguraci můžete pro konfigurace ladění a vydání zadat různá nastavení konfigurace (například proměnné prostředí nebo vstupní body).

Aby bylo možné Docker Compose zobrazit možnost spuštění v aplikaci Visual Studio, musí být projekt Docker spouštěn jako projekt po spuštění.

### <a name="service-fabric"></a>Service Fabric

Kromě základních [požadavků](#prerequisites)řešení orchestrace [Service Fabric](/azure/service-fabric/) vyžaduje následující požadavky:

* [Microsoft Azure Service Fabric SDK](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK) verze 2,6 nebo novější
* Úloha **vývoje Azure** v aplikaci Visual Studio

Service Fabric nepodporuje spouštění kontejnerů Linux v místním vývojovém clusteru ve Windows. Pokud projekt již používá kontejner pro Linux, Visual Studio zobrazí výzvu k přepnutí do kontejnerů Windows.

Nástroje kontejneru sady Visual Studio dělají následující úlohy:

* Přidá do řešení projekt aplikace * &lt; project_name &gt; aplikace* **Service Fabric** .
* Přidá do projektu ASP.NET Core soubor s příponou *souboru Dockerfile* a *. dockerignore* . Pokud *souboru Dockerfile* už v projektu ASP.NET Core existuje, přejmenuje se na *souboru Dockerfile. originál*. Vytvoří se nový *souboru Dockerfile*, který bude vypadat přibližně takto:

    [!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile)]

* Přidá `<IsServiceFabricServiceProject>` prvek do souboru *. csproj* ASP.NET Coreho projektu:

    [!code-xml[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/HelloDockerTools.csproj?name=snippet_IsServiceFabricServiceProject)]

* Přidá složku *PackageRoot* do projektu ASP.NET Core. Složka obsahuje manifest a nastavení služby pro novou službu.

Další informace najdete v tématu [nasazení aplikace .NET v kontejneru Windows do Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container).

## <a name="debug"></a>Ladění

V rozevíracím seznamu ladění na panelu nástrojů vyberte **Docker** a spusťte ladění aplikace. Zobrazení **Docker** okna **výstup** zobrazuje následující akce:

::: moniker range=">= aspnetcore-2.1"

* Byla získána značka *2,1-aspnetcore-runtime* bitové kopie modulu runtime *aplikace Microsoft/dotNET* (Pokud ještě není v mezipaměti). Bitová kopie nainstaluje ASP.NET Core a moduly runtime .NET Core a přidružené knihovny. Je optimalizovaná pro spouštění ASP.NET Corech aplikací v produkčním prostředí.
* `ASPNETCORE_ENVIRONMENT`Proměnná prostředí je nastavena na hodnotu `Development` v rámci kontejneru.
* K dispozici jsou dva dynamicky přiřazené porty: jeden pro HTTP a jeden pro protokol HTTPS. Pomocí příkazu se dá zadat dotaz na port přiřazený k hostiteli localhost `docker ps` .
* Aplikace se zkopíruje do kontejneru.
* Výchozí prohlížeč se spustí s ladicím programem připojeným ke kontejneru pomocí dynamicky přiřazeného portu.

Výsledná image Docker aplikace je označená jako *vývoj*. Obrázek je založen na značce *2,1-aspnetcore-runtime* základní image *Microsoft/dotNET* . Spusťte `docker images` příkaz v okně **konzoly Správce balíčků** (PMC). Zobrazí se obrázky na počítači:

```console
REPOSITORY        TAG                     IMAGE ID      CREATED         SIZE
hellodockertools  dev                     d72ce0f1dfe7  30 seconds ago  255MB
microsoft/dotnet  2.1-aspnetcore-runtime  fcc3887985bb  6 days ago      255MB
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* Byla získána bitová kopie modulu runtime *aplikace Microsoft/aspnetcore* (Pokud ještě není v mezipaměti).
* `ASPNETCORE_ENVIRONMENT`Proměnná prostředí je nastavena na hodnotu `Development` v rámci kontejneru.
* Port 80 je vystavený a mapovaný na dynamicky přiřazený port pro localhost. Port je určen hostitelem Docker a lze k němu zadat dotaz pomocí `docker ps` příkazu.
* Aplikace se zkopíruje do kontejneru.
* Výchozí prohlížeč se spustí s ladicím programem připojeným ke kontejneru pomocí dynamicky přiřazeného portu.

Výsledná image Docker aplikace je označená jako *vývoj*. Obrázek je založen na základní imagi *Microsoft/aspnetcore* . Spusťte `docker images` příkaz v okně **konzoly Správce balíčků** (PMC). Zobrazí se obrázky na počítači:

```console
REPOSITORY            TAG  IMAGE ID      CREATED        SIZE
hellodockertools      dev  5fafe5d1ad5b  4 minutes ago  347MB
microsoft/aspnetcore  2.0  c69d39472da9  13 days ago    347MB
```

::: moniker-end

> [!NOTE]
> K *vývojové* imagi chybí obsah aplikace, protože konfigurace **ladění** používá k zajištění iteračního prostředí připojení svazku. Pokud chcete odeslat image, použijte konfiguraci **vydané verze** .

Spusťte `docker ps` příkaz v PMC. Všimněte si, že aplikace je spuštěná pomocí kontejneru:

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   21 seconds ago      Up 19 seconds       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="edit-and-continue"></a>Upravit a pokračovat

Změny statických souborů a Razor zobrazení se automaticky aktualizují, aniž by bylo nutné krok kompilace. Pro zobrazení aktualizace proveďte změnu, uložte a aktualizujte prohlížeč.

Změny souborů kódu vyžadují kompilaci a restartování Kestrel v rámci kontejneru. Po provedení změny použijte `CTRL+F5` k provedení procesu a spustí aplikaci v rámci kontejneru. Kontejner Docker není znovu sestaven ani zastaven. Spusťte `docker ps` příkaz v PMC. Všimněte si, že původní kontejner pořád běží před 10 minutami:

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   10 minutes ago      Up 10 minutes       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="publish-docker-images"></a>Publikování imagí Docker

Až se cyklus vývoje a ladění aplikace dokončí, pomůže vám nástroje kontejnerů sady Visual Studio při vytváření produkční image aplikace. Změňte rozevírací seznam konfigurace na **vydaná** a sestavte aplikaci. Nástroj získá obrázek kompilace/publikování z Docker Hub (Pokud ještě není v mezipaměti). Obrázek se vytvoří s *nejnovější* značkou, která se dá vložit do privátního registru nebo Dock centra.

Pokud `docker images` chcete zobrazit seznam imagí, spusťte příkaz v PMC. Zobrazí se výstup podobný následujícímu:

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

`microsoft/aspnetcore-build`Image a `microsoft/aspnetcore` uvedené v předchozím výstupu se nahrazují `microsoft/dotnet` obrázky od .NET Core 2,1. Další informace najdete v tématu [oznámení o migraci úložiště Docker](https://github.com/aspnet/Announcements/issues/298).

::: moniker-end

> [!NOTE]
> `docker images`Příkaz vrátí zprostředkující image s názvy a značkami úložiště, které jsou označeny jako *\<none>* (nejsou uvedeny výše). Tyto nepojmenované image jsou vytvářeny pomocí *souboru Dockerfile* [sestavení s více fázemi](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) . Zlepšují efektivitu vytváření finálního obrázku. při změně dojde k jejich opětovnému vytvoření &mdash; pouze nezbytných vrstev. Když už nepotřebujete zprostředkující image, odstraňte je pomocí příkazu [Docker RMI](https://docs.docker.com/engine/reference/commandline/rmi/) .

Mohlo by se stát, že se v porovnání s *vývojovou* imagí bude zmenšovat velikost produkčního nebo produkčního obrazu. Z důvodu mapování svazků ladicí program a aplikace byly spuštěny z místního počítače, nikoli v rámci kontejneru. *Nejnovější* image zabalí potřebný kód aplikace ke spuštění aplikace na hostitelském počítači. Proto rozdíl je velikost kódu aplikace.

## <a name="additional-resources"></a>Další zdroje

* [Vývoj kontejnerů pomocí sady Visual Studio](/visualstudio/containers)
* [Azure Service Fabric: Příprava vývojového prostředí](/azure/service-fabric/service-fabric-get-started)
* [Nasazení aplikace .NET v kontejneru Windows do Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container)
* [Řešení potíží při vývoji v sadě Visual Studio pomocí Dockeru](/azure/vs-azure-tools-docker-troubleshooting-docker-errors)
* [Úložiště GitHub pro Visual Studio Container Tools](https://github.com/Microsoft/DockerTools)
* [GC s použitím Docker a malých kontejnerů](xref:performance/memory#sc)
