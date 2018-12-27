---
uid: mvc/overview/deployment/docker
title: Migrace aplikací ASP.NET MVC do kontejnerů s Windows
description: Zjistěte, jak využít stávající aplikace ASP.NET MVC a spustíte ji v kontejneru Windows Docker
keywords: Windows Containers,Docker,ASP.NET MVC
author: BillWagner
ms.author: wiwagn
ms.date: 12/14/2018
ms.assetid: c9f1d52c-b4bd-4b5d-b7f9-8f9ceaf778c4
ms.openlocfilehash: ef184f4256c20e2a66de8fd2d4f8e67f07d9a086
ms.sourcegitcommit: 6548c19f345850ee22b50f7ef9fca732895d9e08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53425156"
---
# <a name="migrating-aspnet-mvc-applications-to-windows-containers"></a>Migrace aplikací ASP.NET MVC do kontejnerů s Windows

Spuštění existující aplikace založené na platformě .NET v kontejneru Windows nevyžaduje žádné změny do vaší aplikace. Ke spuštění vaší aplikace v kontejneru Windows vytvoření image Dockeru obsahující vaši aplikaci a spuštění kontejneru. Toto téma vysvětluje, jak využít stávající [aplikace ASP.NET MVC](http://www.asp.net/mvc) a nasaďte ji v kontejneru Windows.

Začněte s existující aplikaci ASP.NET MVC a potom sestavit publikované prostředky pomocí sady Visual Studio. Chcete-li vytvořit bitovou kopii, která obsahuje a spouští vaše aplikace pomocí Dockeru. Budete přejděte na web spuštěn v kontejneru Windows a ověřte, zda že aplikace funguje.

Tento článek předpokládá základní znalost Dockeru. Informace o Dockeru najdete [přehled Dockeru](https://docs.docker.com/engine/understanding-docker/).

Aplikace, které spustíte v kontejneru je jednoduchý web, který získáte odpovědi na otázky náhodně. Tato aplikace je základní aplikace MVC pomocí funkce bez ověření a databáze úložiště; To vám umožní zaměřit se na přesun webové vrstvy do kontejneru. Další témata vám ukáže, jak přesunout a spravovat trvalé úložiště v kontejnerizovaných aplikací.

Aplikace zahrnuje tyto kroky:

1. [Vytvoření úlohy Publikovat k vytváření prostředků pro bitovou kopii.](#publish-script)
1. [Sestavování image Dockeru, která se spustí aplikace.](#build-the-image)
1. [Spuštění, na kterém běží vaše image kontejneru Dockeru.](#start-a-container)
1. [Ověřuje se aplikace pomocí prohlížeče.](#verify-in-the-browser)

[Dokončené aplikace](https://github.com/dotnet/samples/tree/master/framework/docker/MVCRandomAnswerGenerator) je na Githubu.

## <a name="prerequisites"></a>Požadavky

Vývojovém počítači musíte mít následující software:

- [Windows 10 Anniversary Update](https://www.microsoft.com/software-download/windows10/) (nebo vyšší) nebo [Windows serveru 2016](https://www.microsoft.com/cloud-platform/windows-server) (nebo novější)
- [Docker pro Windows](https://docs.docker.com/docker-for-windows/) -Beta 26 verze stabilní 1.13.0 nebo 1.12 (nebo novější verze)
- [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017)

> [!IMPORTANT]
> Pokud používáte Windows Server 2016, postupujte podle pokynů pro [nasazení hostitele kontejneru – Windows Server](https://msdn.microsoft.com/virtualization/windowscontainers/deployment/deployment).

Po instalaci a spuštění Dockeru klikněte pravým tlačítkem myši na ikonu na hlavním panelu a vyberte **přepnout na kontejnery Windows**. To se vyžaduje pro spuštění imagí Dockeru založených na Windows. Tento příkaz má spustit během několika sekund:

![Kontejner Windows][windows-container]

## <a name="publish-script"></a>Publikování skriptu

Shromážděte všechny prostředky, které je potřeba načíst do image Dockeru na jednom místě. Můžete použít Visual Studio **publikovat** příkazu vytvořte profil publikování pro aplikaci. Tento profil zařadí všechny prostředky v jednom stromu adresáře, který je zkopírovat do bitové kopie cílové později v tomto kurzu.

**Postup publikování**

1. Klikněte pravým tlačítkem na webový projekt v sadě Visual Studio a vyberte **publikovat**.
1. Klikněte na tlačítko **tlačítko vlastní profil**a pak vyberte **systému souborů** jako metodu.
1. Vyberte adresář. Podle konvence stažené Ukázka používá `bin\Release\PublishOutput`.

![Publikování připojení][publish-connection]

Otevřít **možností publikování souboru** část **nastavení** kartu. Vyberte **Precompile během publikování**. Tyto optimalizace znamená, že budete kompilaci zobrazení v kontejneru Dockeru, kterou kopírujete předkompilované zobrazení.

![Nastavení publikování][publish-settings]

Klikněte na tlačítko **publikovat**, a sady Visual Studio zkopíruje všechny potřebné prostředky do cílové složky.

## <a name="build-the-image"></a>Sestavení image

Vytvořte nový soubor s názvem *soubor Dockerfile* k definování image Dockeru. *Soubor Dockerfile* obsahuje pokyny pro sestavení finální image a zahrnuje názvy základní image, požadované součásti, aplikace, kterou chcete spustit a další konfigurace Image. *Soubor Dockerfile* je vstupem `docker build` příkaz, který vytvoří bitovou kopii.

Pro účely tohoto cvičení bude sestavte image založenou na `microsoft/aspnet` image uložená na [Docker Hubu](https://hub.docker.com/r/microsoft/aspnet/).
Základní image `microsoft/aspnet`, je image Windows serveru. Obsahuje jádro systému Windows Server, služba IIS a ASP.NET 4.7.2. Při spuštění této image ve vašem kontejneru se automaticky spustí službu IIS a nainstalované weby.

Soubor Dockerfile, který vytvoří image vypadá takto:

```console
# The `FROM` instruction specifies the base image. You are
# extending the `microsoft/aspnet` image.

FROM microsoft/aspnet

# The final instruction copies the site you published earlier into the container.
COPY ./bin/Release/PublishOutput/ /inetpub/wwwroot
```

Neexistuje žádná `ENTRYPOINT` příkaz v tomto souboru Dockerfile. Žádný nepotřebujete. Při použití Windows serveru se službou IIS, proces služby IIS je vstupní bod, který je nakonfigurován ke spuštění v aspnet základní image.

Spusťte příkaz sestavení Dockeru vytvořte image spouštějící vaši aplikaci ASP.NET. Chcete-li to provést, v adresáři projektu otevřete okno Powershellu a zadejte následující příkaz v adresáři řešení:

```console
docker build -t mvcrandomanswers .
```

Tento příkaz sestaví novou image podle pokynů ve vašem souboru Dockerfile pojmenování (-t označování) bitové kopie jako mvcrandomanswers. To může zahrnovat stahování základní image z [Docker Hubu](http://hub.docker.com)a potom aplikaci přidáte do této bitové kopie.

Po dokončení tohoto příkazu můžete spustit `docker images` příkazu zobrazte informace o nové imagi:

```console
REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
mvcrandomanswers              latest              86838648aab6        2 minutes ago       10.1 GB
```

ID bitové kopie se liší na svém počítači. Teď aplikaci spustíme.

## <a name="start-a-container"></a>Spuštění kontejneru

Spuštění kontejneru spuštěním následujícího `docker run` příkaz:

```console
docker run -d --name randomanswers mvcrandomanswers
```

`-d` Argument říká Dockeru, spusťte na obrázku v režimu odpojení. To znamená, že se že Image Dockeru spustíte odpojené z aktuálního prostředí.

V mnoha příkladech dockeru může se zobrazit -p mapování portů kontejneru a hostitele. Výchozí aspnet image kontejneru pro naslouchání na portu 80 a zpřístupnit ji již nakonfigurována.

`--name randomanswers` Udává název spuštěného kontejneru. Tento název je můžete použít namísto ID kontejneru v většinu příkazů.

`mvcrandomanswers` Je název bitovou kopii k spouštění.

## <a name="verify-in-the-browser"></a>Ověřte v prohlížeči

Po spuštění kontejneru, připojte se k spuštěného kontejneru pomocí `http://localhost` , jak ukazuje příklad. Zadejte tuto adresu URL do prohlížeče a měli byste vidět web spuštěný.

> [!NOTE]
> Některé softwary VPN nebo proxy serveru brání přejdete na web.
> Je to, abyste měli jistotu, že kontejner fungují dočasně zakázat.

Obsahuje adresáře ukázka na Githubu [skript prostředí PowerShell](https://github.com/dotnet/samples/blob/master/framework/docker/MVCRandomAnswerGenerator/run.ps1) , který spouští tyto příkazy za vás. Otevřete okno Powershellu a změňte adresář na adresář řešení a typ:

```console
./run.ps1
```

Výše uvedeného příkazu sestaví image, se zobrazí seznam imagí na svém počítači a spustí kontejner.

Chcete kontejner zastavit, vydávání `docker stop` příkaz:

```console
docker stop randomanswers
```

Pokud chcete odebrat kontejner, vydávání `docker rm` příkaz:

```console
docker rm randomanswers
```

[windows-container]: media/aspnetmvc/SwitchContainer.png "Přepnout do kontejnerů Windows"
[publish-connection]: media/aspnetmvc/PublishConnection.png "Publikovat do systému souborů"
[publish-settings]: media/aspnetmvc/PublishSettings.png "Nastavení publikování"
