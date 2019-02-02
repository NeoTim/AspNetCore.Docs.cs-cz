---
title: Začínáme s Razor komponenty
author: guardrex
description: Zjistěte, jak začít pracovat s rozhraní komponenty Razor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2019
uid: razor-components/get-started
ms.openlocfilehash: c83af10fd84bc8238f5fe20c66b91ba17de80ae3
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55668096"
---
# <a name="get-started-with-razor-components"></a>Začínáme s Razor komponenty

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

## <a name="setup"></a>Instalace

Nainstalujte následující:

1. [Sady SDK .NET core 2.1](https://go.microsoft.com/fwlink/?linkid=873092) (2.1.500 nebo novější).
1. [Visual Studio 2017](https://go.microsoft.com/fwlink/?linkid=873093) (15.9 nebo vyšší) se *vývoj pro ASP.NET a web* vybranou úlohou.
1. Nejnovější [rozšíření služeb jazyka Blazor](https://go.microsoft.com/fwlink/?linkid=870389) z webu Visual Studio Marketplace.
1. Šablony Blazor na příkazovém řádku:

   ```console
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates
   ```

Chcete-li vytvořit svůj první projekt v sadě Visual Studio:

1. Vyberte **souboru** > **nový projekt** > **webové** > **webová aplikace ASP.NET Core**.
1. Ujistěte se, že **.NET Core** a **ASP.NET Core 2.1** jsou vybrány v horní části.
1. Výběr šablony funkce Blazor a vyberte **OK**.

   ![Dialogové okno nové aplikace](https://msdnshared.blob.core.windows.net/media/2018/07/new-blazor-app-dialog-0.5.0.png)

1. Stisknutím klávesy **Ctrl-F5** a spusťte tak aplikaci *bez ladicího programu*. Spouštění v ladicím programu (**F5**) není v tuto chvíli nepodporuje.

Chcete-li vytvořit novou aplikaci Blazor z příkazového řádku:

```console
dotnet new blazor -o BlazorApp1
cd BlazorApp1
dotnet run
```

Gratulujeme! Právě jste spustili svou první aplikaci Blazor!

![Domovská stránka Blazor aplikace](https://msdnshared.blob.core.windows.net/media/2018/04/blazor-bootstrap-4.png)

## <a name="help--feedback"></a>Nápověda a zpětná vazba

Váš názor je pro nás obzvláště důležité v průběhu této fáze experimentální pro Blazor. Pokud narazíte na problémy nebo při pokusu o navýšení kapacity Blazor na něco zeptat, dejte nám vědět!

* [Soubor problémů na Githubu](https://github.com/aspnet/AspNetCore/issues) jakékoli problémy, které jste narazili nebo a nabízí návrhy pro zlepšení.
* Chat s námi a Blazor komunity na [Gitteru](https://gitter.im/aspnet/blazor) Pokud jste nevíte rady nebo sdílet Blazor fungování za vás.

Poté, co jste si vyzkoušeli Blazor, dejte nám prosím vědět co si myslíte o našem průzkumu v rámci produktu. Stačí kliknout na odkaz průzkumu zobrazený na domovské stránce aplikace, když s některým z šablony projektů Blazor:

![Blazor průzkumu](https://msdnshared.blob.core.windows.net/media/2018/05/blazor-survey-new.png)

## <a name="next-steps"></a>Další kroky

<xref:tutorials/first-blazor-app>
