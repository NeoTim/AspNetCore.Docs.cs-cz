---
title: Image dockeru pro ASP.NET Core
author: tdykstra
description: Další informace o použití publikovaných imagí Dockeru .NET Core z registru Dockeru. Stažení imagí a vytvoření vlastních imagí.
ms.author: tdykstra
ms.custom: mvc
ms.date: 04/09/2019
uid: host-and-deploy/docker/building-net-docker-images
ms.openlocfilehash: e2673e64e18f942515f17982ab374a23392eb473
ms.sourcegitcommit: 8a84ce880b4c40d6694ba6423038f18fc2eb5746
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60165102"
---
# <a name="docker-images-for-aspnet-core"></a>Image dockeru pro ASP.NET Core

Tento kurz ukazuje postupy při spuštění aplikace ASP.NET Core v kontejnerech Dockeru.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Další informace o imagích Dockeru rozhraní Microsoft .NET Core 
> * Stažení ukázkové aplikace ASP.NET Core
> * Místní spuštění ukázkové aplikace
> * Spuštění ukázkové aplikace v Linuxové kontejnery
> * Spuštění ukázkové aplikace v kontejnerech Windows
> * Sestavování a nasazování ručně

## <a name="aspnet-core-docker-images"></a>ASP.NET Core Docker images

Pro účely tohoto kurzu stáhněte si ukázková aplikace ASP.NET Core a spouštění v kontejnerech Dockeru. Ukázka funguje s kontejnery Windows i Linux.

Ukázkový soubor Dockerfile použije [Docker vícefázových sestavení funkce](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) sestavte a spusťte v různých kontejnerů. Sestavení a spuštění kontejnerů jsou vytvořené z imagí, které jsou k dispozici v Docker Hubu společností Microsoft:

* `dotnet/core/sdk`

  Ukázka používá tuto bitovou kopii pro sestavení aplikace. Bitová kopie obsahuje sady .NET Core SDK, která zahrnuje nástroje příkazového řádku (CLI). Na obrázku je optimalizovaná pro místní vývoj, ladění a testování částí. Nástroje nainstalované pro vývoj a sestavování vytvořit poměrně velký obrázek. 

* `dotnet/core/aspnet` 

   Ukázka používá tuto bitovou kopii pro spuštění aplikace. Na obrázku obsahuje modul runtime ASP.NET Core a knihovny a je optimalizovaná pro spouštění aplikací v produkčním prostředí. Je určená pro rychlost nasazení a spuštění aplikace a image je poměrně malý, proto je optimalizován výkon sítě z registru Dockeru do Docker hostitele. Binární soubory a obsah potřebný ke spuštění aplikace se zkopírují do kontejneru. Obsah je připraveno ke spuštění, povolení nejrychlejší doba od `Docker run` pro spuštění aplikace. Dynamický kód kompilace, není nutná v modelu Dockeru.

## <a name="prerequisites"></a>Požadavky

* [.NET Core 2.2 SDK](https://www.microsoft.com/net/core)

* Klient docker 18.03 nebo novější

  * Linuxové distribuce
    * [CentOS](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [Debian](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [Fedora](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [macOS](https://docs.docker.com/docker-for-mac/install/)
  * [Windows](https://docs.docker.com/docker-for-windows/install/)

* [git](https://git-scm.com/download)

## <a name="download-the-sample-app"></a>Stáhněte si ukázkovou aplikaci

* Stáhněte si ukázku naklonováním [úložiště .NET Core Dockeru](https://github.com/dotnet/dotnet-docker): 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a>Místní spuštění aplikace

* Přejděte do složky projektu v *dotnet dockeru/samples/aspnetapp/aspnetapp*.

* Spuštěním následujícího příkazu sestavte a spusťte aplikaci místně:

  ```console
  dotnet run
  ```

* Přejděte na `http://localhost:5000` v prohlížeči a otestujte aplikaci.

* Na příkazovém řádku se zastavit aplikaci stisknutím Ctrl + C.

## <a name="run-in-a-linux-container"></a>Spuštění v kontejneru Linuxu

* V klientovi Docker přepněte na kontejnery Linux.

* Přejděte do složky soubor Dockerfile v *dotnet dockeru/samples/aspnetapp*.

* Spusťte následující příkazy k vytvoření a spuštění ukázky v Dockeru:

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  `build` Argumenty příkazu:
  * Název aspnetapp bitové kopie.
  * Vyhledejte soubor Dockerfile v aktuální složce (tečky na konci).

  Argumenty spuštění příkazu:
  * Přidělit pseudo-TTY a nechte ho otevřený i v případě, že nejsou připojené. (Stejný vliv jako `--interactive --tty`.)
  * Automaticky odstranit kontejneru při jeho ukončení.
  * Mapování portu 5000 místního počítače na port 80 v kontejneru.
  * Název kontejneru aspnetcore_sample.
  * Zadejte bitovou kopii aspnetapp.

* Přejděte na `http://localhost:5000` v prohlížeči a otestujte aplikaci.

## <a name="run-in-a-windows-container"></a>Spuštění v kontejneru Windows

* V klientovi Docker přepněte na kontejnery Windows.

Přejděte do složky souboru docker na `dotnet-docker/samples/aspnetapp`.

* Spusťte následující příkazy k vytvoření a spuštění ukázky v Dockeru:

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* Pro kontejnery Windows, budete potřebovat IP adresu kontejneru (přejdete na adresu `http://localhost:5000` nebude fungovat):
  * Otevřete jiného příkazového řádku.
  * Spustit `docker ps` zobrazte spuštěné kontejnery. Ověřte, že "aspnetcore_sample" kontejneru existuje.
  * Spustit `docker exec aspnetcore_sample ipconfig` zobrazit IP adresu kontejneru. Výstup příkazu by měl vypadat jako v tomto příkladu:

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* Kontejner adresa protokolu IPv4 (například 172.29.245.43) zkopírujte a vložte do adresního řádku prohlížeče a otestujte aplikaci.

## <a name="build-and-deploy-manually"></a>Sestavování a nasazování ručně

V některých případech můžete chtít nasadit aplikace do kontejneru tak, že zkopírujete do něj soubory aplikace, které jsou potřebné v době běhu. Tato část ukazuje, jak ručně nasadit.

* Přejděte do složky projektu v *dotnet dockeru/samples/aspnetapp/aspnetapp*.

* Spustit [dotnet publikovat](https://docs.microsoft.com/dotnet/core/tools/dotnet-publish.md) příkaz:

  ```console
  dotnet publish -c Release -o published
  ```

  Argumenty příkazu:
  * Vytvoření aplikace v režimu vydání (výchozí hodnota je režim ladění).
  * Vytvoření souborů v *publikované* složky.

* Spusťte aplikaci.

  * Windows:

    ```console
    dotnet published\aspnetapp.dll
    ```

  * Linux:

    ```bash
    dotnet published/aspnetapp.dll
    ```

* Přejděte do `http://localhost:5000` zobrazíte na domovské stránce.

### <a name="the-dockerfile"></a>Soubor Dockerfile

Tady je soubor Dockerfile používané `docker build` příkaz předtím.  Používá `dotnet publish` stejně jako jste to udělali v této části sestavíte a nasadíte.  

```console
FROM mcr.microsoft.com/dotnet/core/sdk:2.2 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out


FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

## <a name="additional-resources"></a>Další zdroje

* [Příkaz sestavení dockeru](https://docs.docker.com/engine/reference/commandline/build)
* [Docker, spusťte příkaz](https://docs.docker.com/engine/reference/commandline/run)
* [Ukázka technologie ASP.NET Core Docker](https://github.com/dotnet/dotnet-docker) (použitým v tomto kurzu.)
* [Konfigurace ASP.NET Core práci se servery proxy a nástroje pro vyrovnávání zatížení](/aspnet/core/host-and-deploy/proxy-load-balancer)
* [Práce s nástroji Dockeru pro Visual Studio](https://docs.microsoft.com/aspnet/core/publishing/visual-studio-tools-for-docker)
* [Ladění ve Visual Studiu Code](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers) 

## <a name="next-steps"></a>Další kroky

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Dozvěděli jste se o imagích Dockeru rozhraní Microsoft .NET Core 
> * Stažení ukázkové aplikace ASP.NET Core
> * Místní spuštění ukázkové aplikace
> * Spuštění ukázkové aplikace v Linuxové kontejnery
> * Spusťte ukázku pomocí v kontejnerech Windows
> * Sestavíte a nasadíte ručně

V dalším kroku informace o nástrojích, které Visual Studio pro práci s Dockerem.

> [!div class="nextstepaction"]
> <xref:host-and-deploy/docker/visual-studio-tools-for-docker>
