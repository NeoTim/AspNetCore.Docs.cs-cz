---
title: Hostitelství a nasazení Blazor
author: guardrex
description: Objevte, jak hostovat a nasazovat aplikace Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: host-and-deploy/blazor/index
ms.openlocfilehash: a7739e2b240d7fd6c85e68105892c802228ebeb5
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614777"
---
# <a name="host-and-deploy-blazor"></a>Hostitelství a nasazení Blazor

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

* <xref:host-and-deploy/blazor/client-side>
* <xref:host-and-deploy/blazor/server-side>
