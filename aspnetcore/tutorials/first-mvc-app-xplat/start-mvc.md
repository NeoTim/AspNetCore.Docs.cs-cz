---
title: Úvod do ASP.NET Core MVC v systému macOS, Linux nebo Windows
author: rick-anderson
description: Zjistěte, jak začít pracovat s ASP.NET Core MVC a Visual Studio Code na macOS, Linux a Windows
ms.author: riande
ms.date: 07/07/2017
uid: tutorials/first-mvc-app-xplat/start-mvc
ms.openlocfilehash: b25ee29541e345a3bf6700b67d992409c02b183a
ms.sourcegitcommit: ecf2cd4e0613569025b28e12de3baa21d86d4258
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2018
ms.locfileid: "36275272"
---
# <a name="introduction-to-aspnet-core-mvc-on-macos-linux-or-windows"></a>Úvod do ASP.NET Core MVC v systému macOS, Linux nebo Windows

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

V tomto kurzu se seznámíte se základy vytváření webové aplikace ASP.NET Core MVC pomocí [Visual Studio Code](https://code.visualstudio.com) (VS Code). Kurz předpokládá familarity s VS Code. Zobrazit [Začínáme s VS Code](https://code.visualstudio.com/docs) a [nápovědy Visual Studio Code](#visual-studio-code-help) Další informace. 

[!INCLUDE [consider RP](../../includes/razor.md)]

Existují 3 verze tohoto kurzu:

* macOS: [vytvoření aplikace ASP.NET Core MVC se sadou Visual Studio pro Mac](xref:tutorials/first-mvc-app-mac/start-mvc)
* Windows: [vytvořit v aplikaci MVC ASP.NET Core pomocí sady Visual Studio](xref:tutorials/first-mvc-app/start-mvc)
* macOS, Linux a Windows: [vytvoření aplikace ASP.NET Core MVC pomocí Visual Studio Code](xref:tutorials/first-mvc-app-xplat/start-mvc) 

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [](~/includes/net-core-prereqs-vscode.md)]

## <a name="create-a-web-app-with-dotnet"></a>Vytvoření webové aplikace s dotnet

Z terminálu spusťte následující příkazy:

```console
mkdir MvcMovie
cd MvcMovie
dotnet new mvc
```

Otevřít *MvcMovie* složky ve Visual Studio Code (VS Code) a vyberte *Startup.cs* souboru.

- Vyberte **Ano** k **upozornit** zpráva "požadované prostředky pro vytvoření a odladění chybí"MvcMovie". Přidáním?"
- Vyberte **obnovení** k **informace** zpráva "Neexistují nevyřešené závislosti".

![VS Code s upozornit požadované prostředky pro sestavování a ladění 'MvcMovie' chybí. Přidat? Neptat se znovu, nyní ne, Ano a také informace o - neexistují nevyřešené závislosti - Restore - zavřít](../web-api-vsc/_static/vsc_restore.png)

Stisknutím klávesy **ladění** (F5) a sestavte a spusťte program.

![spuštění aplikace](../first-mvc-app/start-mvc/_static/1.png)

Spuštění VS Code [Kestrel](xref:fundamentals/servers/kestrel) webového serveru vaší aplikace a spustí. Všimněte si, že do adresního řádku ukazuje `localhost:5000` a nemít něco podobného `example.com`. Důvodem je, že `localhost` je standardní název hostitele místního počítače.

Výchozí šablony vám práci **domácí o** a **kontakt** odkazy. Výše uvedené prohlížeče obraz se nezobrazí těchto odkazů. V závislosti na velikosti vašeho prohlížeče mohou muset kliknout na ikonu navigace se budou zobrazovat.

![Ikona navigace v vpravo nahoře](../first-mvc-app/start-mvc/_static/2.png)

V další části tohoto kurzu přidáme další informace o MVC a začít psát kód.

## <a name="visual-studio-code-help"></a>Visual Studio Code – Nápověda

- [Začínáme](https://code.visualstudio.com/docs)
- [Ladění](https://code.visualstudio.com/docs/editor/debugging)
- [Integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal)
- [Klávesové zkratky](https://code.visualstudio.com/docs/getstarted/keybindings#_keyboard-shortcuts-reference)

  - [macOS klávesové zkratky](https://code.visualstudio.com/shortcuts/keyboard-shortcuts-macos.pdf)
  - [Linux klávesové zkratky](https://code.visualstudio.com/shortcuts/keyboard-shortcuts-linux.pdf)
  - [Windows klávesové zkratky](https://code.visualstudio.com/shortcuts/keyboard-shortcuts-windows.pdf)

> [!div class="step-by-step"]
> [Další – Přidání kontroleru](adding-controller.md)
