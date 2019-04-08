---
title: Hostitelství a nasazení komponent Razor a Blazor
author: guardrex
description: Objevte, jak hostovat a nasazení komponent Razor a Blazor aplikací.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/28/2019
uid: host-and-deploy/razor-components-blazor/index
ms.openlocfilehash: eeaa27bef584523b77d8b47000b310fe0cac7341
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59069777"
---
# <a name="host-and-deploy-razor-components-and-blazor"></a>Hostitelství a nasazení komponent Razor a Blazor

Podle [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), a [Daniel Roth](https://github.com/danroth27)

## <a name="publish-the-app"></a>Publikování aplikace

Aplikace budou publikovány pro nasazení v rámci konfigurace verze se [dotnet publikovat](/dotnet/core/tools/dotnet-publish) příkazu. Integrované vývojové prostředí (IDE) může zpracovat provádění `dotnet publish` příkaz automaticky pomocí jeho integrované funkce pro publikování, takže nemusí být potřeba ručně spusťte příkaz z příkazového řádku v závislosti na vývoj nástroje používané.

```console
dotnet publish -c Release
```

`dotnet publish` aktivační události [obnovení](/dotnet/core/tools/dotnet-restore) závislostí projektu a [sestavení](/dotnet/core/tools/dotnet-build) projektu před vytvořením prostředků pro nasazení. Jako součást procesu sestavení jsou odebrány nepoužívané metod a sestavení snížit velikost ke stažení aplikace a časy načtení. Nasazení se vytvoří v */bin/vydání / {CÍLOVÁ ARCHITEKTURA} / publish* složky.

Prostředky v *publikovat* složky jsou nasazené na webovém serveru. Nasazení může být ruční nebo automatizované proces v závislosti na vývojové nástroje, které používá.

## <a name="deployment"></a>Nasazení

Pokyny k nasazení naleznete v následujících tématech:

* [Blazor na straně klienta](xref:host-and-deploy/razor-components-blazor/blazor)
* [Komponenty na straně serveru ASP.NET Core Razor](xref:host-and-deploy/razor-components-blazor/razor-components)
