---
title: Image Dockeru pro ASP.NET jádro
author: rick-anderson
description: Zjistěte, jak používat publikované image .NET Core Docker u registru Dockeru. Vytáhněte obrázky a vytvořte si vlastní obrázky.
ms.author: riande
ms.custom: mvc
ms.date: 01/15/2020
uid: host-and-deploy/docker/building-net-docker-images
ms.openlocfilehash: ced0cb7cbeed1b8811813a70035c2e0b42c3e35a
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440776"
---
# <a name="docker-images-for-aspnet-core"></a>Image Dockeru pro ASP.NET jádro

Tento kurz ukazuje, jak spustit aplikaci ASP.NET Core v kontejnerech Dockeru.

V tomto kurzu jste:
> [!div class="checklist"]
> * Další informace o inicicích Microsoft .NET Core Docker
> * Stažení ukázkové aplikace ASP.NET Core
> * Spuštění ukázkové aplikace místně
> * Spuštění ukázkové aplikace v kontejnerech Linuxu
> * Spuštění ukázkové aplikace v kontejnerech windows
> * Ruční sestavení a nasazení

## <a name="aspnet-core-docker-images"></a>ASP.NET image Core Dockeru

V tomto kurzu si stáhnete ukázkovou aplikaci ASP.NET Core a spustíte ji v kontejnerech Dockeru. Ukázka pracuje s linuxovými i windows kontejnery.

Ukázkový soubor Dockerfile používá [funkci vícefázového sestavení Dockeru](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) k sestavení a spuštění v různých kontejnerech. Kontejnery sestavení a spuštění jsou vytvořeny z bitových kopií, které jsou k dispozici v Docker Hub u Microsoftu:

* `dotnet/core/sdk`

  Ukázka používá tento obrázek pro vytváření aplikace. Obrázek obsahuje sadku .NET Core SDK, která obsahuje nástroje příkazového řádku (CLI). Bitová kopie je optimalizována pro místní vývoj, ladění a testování částí. Nástroje nainstalované pro vývoj a kompilaci z toho činí poměrně velký obraz. 

* `dotnet/core/aspnet`

   Ukázka používá tento obrázek pro spuštění aplikace. Bitová kopie obsahuje ASP.NET core runtime a knihovny a je optimalizovánpro spouštění aplikací v produkčním prostředí. Image je navržena pro rychlost nasazení a spuštění aplikace a je relativně malá, takže je optimalizovaný výkon sítě od registru Dockeru po hostitele Dockeru. Do kontejneru se zkopírují pouze binární soubory a obsah potřebný ke spuštění aplikace. Obsah je připraven ke spuštění, což `Docker run` umožňuje nejrychlejší čas od spuštění aplikace. Kompilace dynamického kódu není v modelu Dockeru potřebná.

## <a name="prerequisites"></a>Požadavky
::: moniker range="< aspnetcore-3.0"

* [.NET Jádro 2.2 SDK](https://dotnet.microsoft.com/download/dotnet-core)
::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* [Sada .NET Core SDK 3.0](https://dotnet.microsoft.com/download)

::: moniker-end

* Klient Dockeru 18.03 nebo novější

  * Linuxové distribuce
    * [CentOS](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [Debian](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [Fedora](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [macOS](https://docs.docker.com/docker-for-mac/install/)
  * [Windows](https://docs.docker.com/docker-for-windows/install/)

* [Git](https://git-scm.com/download)

## <a name="download-the-sample-app"></a>Stažení ukázkové aplikace

* Stáhněte si ukázku klonováním [úložiště .NET Core Docker](https://github.com/dotnet/dotnet-docker): 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a>Místní spuštění aplikace

* Přejděte do složky projektu na *dotnet-docker/samples/aspnetapp/aspnetapp*.

* Spusťte následující příkaz k sestavení a spuštění aplikace místně:

  ```dotnetcli
  dotnet run
  ```

* Přejděte `http://localhost:5000` do prohlížeče a otestujte aplikaci.

* Stisknutím kláves Ctrl+C na příkazovém řádku aplikaci zastavte.

## <a name="run-in-a-linux-container"></a>Spuštění v kontejneru Linuxu

* V klientovi Dockeru přepněte do kontejnerů Linuxu.

* Přejděte do složky Dockerfile na *adrese dotnet-docker/samples/aspnetapp*.

* Spusťte následující příkazy pro sestavení a spuštění ukázky v Dockeru:

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  Argumenty `build` příkazu:
  * Pojmenujte obraz aspnetapp.
  * Vyhledejte Dockerfile v aktuální složce (tečka na konci).

  Argumenty příkazu run:
  * Přidělit pseudo-TTY a udržet ji otevřenou, i když není připojen. (Stejný efekt `--interactive --tty`jako .)
  * Automaticky odeberte kontejner při jeho ukončení.
  * Mapovat port 5000 v místním počítači na port 80 v kontejneru.
  * Pojmenujte aspnetcore_sample kontejneru.
  * Určete obraz aspnetapp.

* Přejděte `http://localhost:5000` do prohlížeče a otestujte aplikaci.

## <a name="run-in-a-windows-container"></a>Spuštění v kontejneru windows

* V klientovi Dockeru přepněte do kontejnerů windows.

Přejděte do složky `dotnet-docker/samples/aspnetapp`souborů dockeru na adrese .

* Spusťte následující příkazy pro sestavení a spuštění ukázky v Dockeru:

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* U kontejnerů se systémem Windows potřebujete IP `http://localhost:5000` adresu kontejneru (procházení nebude fungovat):
  * Otevřete jiný příkazový řádek.
  * Spuštěním `docker ps` zobrazíte spuštěné kontejnery. Ověřte, zda je kontejner "aspnetcore_sample".
  * Spuštěním `docker exec aspnetcore_sample ipconfig` zobrazíte IP adresu kontejneru. Výstup z příkazu vypadá takto:

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* Zkopírujte adresu IPv4 kontejneru (například 172.29.245.43) a vložte ji do adresního řádku prohlížeče a otestujte aplikaci.

## <a name="build-and-deploy-manually"></a>Ruční sestavení a nasazení

V některých případech můžete chtít nasadit aplikaci do kontejneru zkopírováním do něj soubory aplikace, které jsou potřebné v době běhu. Tato část ukazuje, jak nasadit ručně.

* Přejděte do složky projektu na *dotnet-docker/samples/aspnetapp/aspnetapp*.

* Spusťte příkaz [publikovat dotnet:](/dotnet/core/tools/dotnet-publish)

  ```dotnetcli
  dotnet publish -c Release -o published
  ```

  Argumenty příkazu:
  * Vytvořte aplikaci v režimu vydání (výchozí je režim ladění).
  * Vytvořte soubory v *publikované* složce.

* Spusťte aplikaci.

  * Windows:

    ```dotnetcli
    dotnet published\aspnetapp.dll
    ```

  * Linux:

    ```dotnetcli
    dotnet published/aspnetapp.dll
    ```

* Přejděte `http://localhost:5000` na domovskou stránku.

Chcete-li použít ručně publikovanou aplikaci v kontejneru Dockeru, vytvořte nový soubor Dockerfile a pomocí příkazu `docker build .` vytvořte kontejner.

::: moniker range="< aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a>Soubor Dockerfile

Zde je *Dockerfile* používá `docker build` příkaz, který jste spustili dříve.  Používá `dotnet publish` stejným způsobem jako v této části k sestavení a nasazení.  

```dockerfile
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

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a>Soubor Dockerfile

Zde je *Dockerfile* používá `docker build` příkaz, který jste spustili dříve.  Používá `dotnet publish` stejným způsobem jako v této části k sestavení a nasazení.  

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:3.0 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out


FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

::: moniker-end

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

## <a name="additional-resources"></a>Další zdroje

* [Docker sestavení příkaz](https://docs.docker.com/engine/reference/commandline/build)
* [Docker spustit, příkaz](https://docs.docker.com/engine/reference/commandline/run)
* [ASP.NET ukázka Core Dockeru](https://github.com/dotnet/dotnet-docker) (ta použitá v tomto kurzu.)
* [Konfigurace ASP.NET Core pro práci s proxy servery a vyrovnáváním zatížení](/aspnet/core/host-and-deploy/proxy-load-balancer)
* [Práce s nástroji Visual Studio Docker](https://docs.microsoft.com/aspnet/core/publishing/visual-studio-tools-for-docker)
* [Ladění pomocí kódu sady Visual Studio](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers)
* [GC pomocí Dockeru a malých kontejnerů](xref:performance/memory#sc)

## <a name="next-steps"></a>Další kroky

Úložiště Git, které obsahuje ukázkovou aplikaci, obsahuje také dokumentaci. Přehled prostředků dostupných v úložišti naleznete v [souboru README](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md). Zejména se dozvíte, jak implementovat HTTPS:

> [!div class="nextstepaction"]
> [Vývoj základních aplikací ASP.NET s Dockerem přes protokol HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md)
