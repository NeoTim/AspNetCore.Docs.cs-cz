---
title: Začínáme s ASP.NET Core MVC a sady Visual Studio pro Mac
author: rick-anderson
description: Zjistěte, jak začít pracovat s ASP.NET Core MVC a sady Visual Studio
ms.author: riande
ms.date: 8/23/2017
uid: tutorials/first-mvc-app-mac/start-mvc
ms.openlocfilehash: 059ac1f7fa94d97adc958be3c0b936cdfa7f6d3e
ms.sourcegitcommit: fc7eb4243188950ae1f1b52669edc007e9d0798d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51225470"
---
# <a name="get-started-with-aspnet-core-mvc-and-visual-studio-for-mac"></a>Začínáme s ASP.NET Core MVC a sady Visual Studio pro Mac

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

V tomto kurzu se naučíte se základy vytváření webové aplikace ASP.NET Core MVC pomocí [Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac/). 

[!INCLUDE [consider RP](../../includes/razor.md)]

Existují 3 verze tohoto kurzu:

* macOS: [sestavení aplikace ASP.NET Core MVC v jazyce Visual Studio pro Mac](xref:tutorials/first-mvc-app-mac/start-mvc)
* Windows: [sestavit aplikaci MVC ASP.NET Core pomocí sady Visual Studio](xref:tutorials/first-mvc-app/start-mvc)
* Linux, macOS a Windows: [sestavení aplikace ASP.NET Core MVC v jazyce Visual Studio Code](xref:tutorials/first-mvc-app-xplat/start-mvc)

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [](~/includes/net-core-prereqs-macos.md)]

## <a name="create-a-web-app"></a>Vytvoření webové aplikace

Ze sady Visual Studio, vyberte **soubor > Nový řešení**.

![macOS nové řešení](../first-web-api-mac/_static/sln.png)

Vyberte **aplikace .NET Core > ASP.NET Core > webové aplikace ASP.NET Core (MVC) > Další**.

![macOS dialogové okno nového projektu](start-mvc/1.png)

Pojmenujte projekt **MvcMovie**a pak vyberte **vytvořit**.

![macOS dialogové okno nového projektu](start-mvc/2.png)

### <a name="launch-the-app"></a>Spuštění aplikace

V sadě Visual Studio, vyberte **spuštění > Spustit bez ladění** aplikaci spustit. Visual Studio spustí [Kestrel](xref:fundamentals/servers/index#kestrel), spustí se prohlížeč a přejde na `http://localhost:port`, kde *port* je číslo portu náhodně vybrané.

![Prohlížeč s nový projekt](start-mvc/b1.png)

* Zobrazí se panel Adresa `localhost:port#` a nemít něco podobného `example.com`. Důvodem je, že `localhost` je standardní název hostitele místního počítače. Když Visual Studio vytvoří webový projekt, náhodný port se používá pro webový server. Při spuštění aplikace se zobrazí jiné číslo portu.
* Spustíte ji v ladění nebo v režimu bez ladění z **spustit** nabídky.

Výchozí šablony vám **domácí o** a **kontakt** odkazy. Výše uvedené prohlížeče obraz se nezobrazí těchto odkazů. V závislosti na velikosti vašeho prohlížeče mohou muset kliknout na ikonu navigace se budou zobrazovat.

![Prohlížeč s nový projekt](start-mvc/b2.png)

V další části tohoto kurzu přečtěte si o MVC a začít psát kód.

> [!div class="step-by-step"]
> [Next](adding-controller.md)  
