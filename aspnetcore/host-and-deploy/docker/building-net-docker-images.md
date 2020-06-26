---
title: Image Docker pro ASP.NET Core
author: rick-anderson
description: Naučte se používat publikované image Docker .NET Core z registru Docker. Vyžádání imagí a sestavení vlastních imagí.
ms.author: riande
ms.custom: mvc
ms.date: 05/12/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/docker/building-net-docker-images
ms.openlocfilehash: 2b77b4fbb960a8d2dbcb67c3f4f32ba9d9459d78
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408081"
---
# <a name="docker-images-for-aspnet-core"></a>Image Docker pro ASP.NET Core

V tomto kurzu se dozvíte, jak spustit aplikaci ASP.NET Core v kontejnerech Docker.

V tomto kurzu jste:
> [!div class="checklist"]
> * Další informace o imagích Docker Microsoft .NET Core
> * Stažení ukázkové aplikace ASP.NET Core
> * Spustit ukázkovou aplikaci místně
> * Spuštění ukázkové aplikace v kontejnerech Linux
> * Spuštění ukázkové aplikace v kontejnerech Windows
> * Ruční sestavení a nasazení

## <a name="aspnet-core-docker-images"></a>Image ASP.NET Core Docker

Pro tento kurz si stáhnete ukázkovou aplikaci ASP.NET Core a spustíte ji v kontejnerech Docker. Ukázka funguje s kontejnery pro Linux i Windows.

Vzorový souboru Dockerfile využívá [funkci buildu pro více fází](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) pro sestavování a spouštění v různých kontejnerech. Kontejnery sestavení a spuštění jsou vytvořeny z imagí, které jsou k dispozici v Docker Hub od společnosti Microsoft:

* `dotnet/core/sdk`

  Ukázka používá tuto image k sestavení aplikace. Obrázek obsahuje .NET Core SDK, která obsahuje nástroje příkazového řádku (CLI). Obrázek je optimalizován pro místní vývoj, ladění a testování částí. Nástroje nainstalované pro vývoj a kompilaci vytvářejí poměrně velký obrázek. 

* `dotnet/core/aspnet`

   Ukázka používá tuto image ke spuštění aplikace. Image obsahuje modul runtime a knihovny ASP.NET Core a je optimalizovaný pro spuštěné aplikace v produkčním prostředí. Bitová kopie je navržena pro rychlost nasazení a spouštění aplikací, takže je optimalizován výkon sítě z registru Docker na hostitele Docker. Do kontejneru se zkopírují jenom binární soubory a obsah potřebný ke spuštění aplikace. Obsah je připravený ke spuštění, což umožňuje nejrychlejší čas od `Docker run` spuštění aplikace. Dynamická kompilace kódu není v modelu Docker nutná.

## <a name="prerequisites"></a>Požadavky
::: moniker range="< aspnetcore-3.0"

* [Sada .NET Core 2,2 SDK](https://dotnet.microsoft.com/download/dotnet-core)
::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* [.NET Core SDK 3,0](https://dotnet.microsoft.com/download)

::: moniker-end

* Klient Docker 18,03 nebo novější

  * Distribuce systému Linux
    * [CentOS](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [Debian](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [Fedora](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [macOS](https://docs.docker.com/docker-for-mac/install/)
  * [Windows](https://docs.docker.com/docker-for-windows/install/)

* [Git](https://git-scm.com/download)

## <a name="download-the-sample-app"></a>Stažení ukázkové aplikace

* Stáhněte si ukázku klonování [úložiště Docker .NET Core](https://github.com/dotnet/dotnet-docker): 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a>Místní spuštění aplikace

* Přejděte do složky projektu v *dotnet-Docker/Samples/aspnetapp/aspnetapp*.

* Spuštěním následujícího příkazu Sestavte a spusťte aplikaci místně:

  ```dotnetcli
  dotnet run
  ```

* `http://localhost:5000`V prohlížeči otestujte aplikaci.

* Stisknutím kombinace kláves CTRL + C na příkazovém řádku zastavte aplikaci.

## <a name="run-in-a-linux-container"></a>Spuštění v kontejneru Linux

* V klientovi Docker přepněte na kontejnery Linux.

* Přejděte do složky souboru Dockerfile v *dotnet-Docker/Samples/aspnetapp*.

* Spuštěním následujících příkazů Sestavte a spusťte ukázku v Docker:

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  `build`Argumenty příkazu:
  * Pojmenujte bitovou kopii aspnetapp.
  * Vyhledejte souboru Dockerfile v aktuální složce (tečka na konci).

  Argumenty příkazu Run:
  * Přidělte pseudo-TTY a nechte ho otevřený i v případě, že není připojený. (Stejný efekt jako `--interactive --tty` .)
  * Kontejner se po ukončení automaticky odebere.
  * Namapujte port 5000 na místním počítači na port 80 v kontejneru.
  * Pojmenujte aspnetcore_sample kontejneru.
  * Zadejte bitovou kopii aspnetapp.

* `http://localhost:5000`V prohlížeči otestujte aplikaci.

## <a name="run-in-a-windows-container"></a>Spuštění v kontejneru Windows

* V klientovi Docker přepněte do kontejnerů Windows.

Přejděte do složky Docker File v `dotnet-docker/samples/aspnetapp` .

* Spuštěním následujících příkazů Sestavte a spusťte ukázku v Docker:

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* V případě kontejnerů Windows budete potřebovat IP adresu kontejneru (procházení `http://localhost:5000` nebude fungovat):
  * Otevřete další příkazový řádek.
  * Spusťte, `docker ps` aby se zobrazily spuštěné kontejnery. Ověřte, že je kontejner aspnetcore_sample.
  * Spusťte `docker exec aspnetcore_sample ipconfig` pro zobrazení IP adresy kontejneru. Výstup příkazu vypadá jako v tomto příkladu:

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* Zkopírujte adresu IPv4 kontejneru (například 172.29.245.43) a vložte ji do adresního řádku prohlížeče, aby se aplikace otestovala.

## <a name="build-and-deploy-manually"></a>Ruční sestavení a nasazení

V některých scénářích můžete chtít nasadit aplikaci do kontejneru tak, že do ní nakopírujete soubory aplikace, které jsou potřeba v době běhu. V této části se dozvíte, jak ručně nasadit.

* Přejděte do složky projektu v *dotnet-Docker/Samples/aspnetapp/aspnetapp*.

* Spusťte příkaz [dotnet Publish](/dotnet/core/tools/dotnet-publish) :

  ```dotnetcli
  dotnet publish -c Release -o published
  ```

  Argumenty příkazu:
  * Sestavte aplikaci v režimu vydání (výchozí je režim ladění).
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

* Přejděte k `http://localhost:5000` zobrazení domovské stránky.

Chcete-li použít manuálně publikovanou aplikaci v kontejneru Docker, vytvořte novou souboru Dockerfile a pomocí `docker build .` příkazu Sestavte kontejner.

::: moniker range="< aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a>Souboru Dockerfile

Tady je *souboru Dockerfile* , `docker build` který používá příkaz, který jste spustili dříve.  `dotnet publish`Pro sestavování a nasazování používá stejný způsob jako v tomto oddílu.  

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

### <a name="the-dockerfile"></a>Souboru Dockerfile

Tady je *souboru Dockerfile* , `docker build` který používá příkaz, který jste spustili dříve.  `dotnet publish`Pro sestavování a nasazování používá stejný způsob jako v tomto oddílu.  

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

Jak je uvedeno v předchozím souboru Dockerfile, `*.csproj` soubory se zkopírují a obnoví jako samostatné *vrstvy*. Když `docker build` příkaz vytvoří image, použije vestavěnou mezipaměť. Pokud se `*.csproj` soubory od `docker build` posledního spuštění příkazu nezměnily, `dotnet restore` příkaz se nemusí znovu spustit. Místo toho se znovu použije vestavěná mezipaměť pro odpovídající `dotnet restore` vrstvu. Další informace najdete v tématu [osvědčené postupy pro psaní fázemi](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* [Docker – příkaz buildu](https://docs.docker.com/engine/reference/commandline/build)
* [Příkaz Spustit jako Docker](https://docs.docker.com/engine/reference/commandline/run)
* [Ukázka docker ASP.NET Core](https://github.com/dotnet/dotnet-docker) (ten, který jste použili v tomto kurzu.)
* [Konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení](/aspnet/core/host-and-deploy/proxy-load-balancer)
* [Práce s nástroji Docker sady Visual Studio](https://docs.microsoft.com/aspnet/core/publishing/visual-studio-tools-for-docker)
* [Ladění pomocí Visual Studio Code](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers)
* [GC s použitím Docker a malých kontejnerů](xref:performance/memory#sc)

## <a name="next-steps"></a>Další kroky

Úložiště Git, které obsahuje ukázkovou aplikaci, také obsahuje dokumentaci. Přehled prostředků, které jsou k dispozici v úložišti, najdete v [souboru Readme](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md). Konkrétně se dozvíte, jak implementovat protokol HTTPS:

> [!div class="nextstepaction"]
> [Vývoj aplikací ASP.NET Core pomocí Docker přes HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md)
