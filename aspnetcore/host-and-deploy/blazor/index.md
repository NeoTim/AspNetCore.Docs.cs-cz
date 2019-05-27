---
title: Hostitelství a nasazení Blazor
author: guardrex
description: Objevte, jak hostovat a nasazovat aplikace Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/23/2019
uid: host-and-deploy/blazor/index
ms.openlocfilehash: 5def0356d13975211dd234f6a6a9f5a993d003b7
ms.sourcegitcommit: e1623d8279b27ff83d8ad67a1e7ef439259decdf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/25/2019
ms.locfileid: "66223183"
---
# <a name="host-and-deploy-blazor"></a>Hostitelství a nasazení Blazor

Podle [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), a [Daniel Roth](https://github.com/danroth27)

## <a name="publish-the-app"></a>Publikování aplikace

Aplikace se publikují v konfiguraci vydané verze pro nasazení.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Vyberte **sestavení** > **publikování {aplikace}** z navigačního panelu.
1. Vyberte *publikovat cílové*. Chcete-li publikovat místně, vyberte **složky**.
1. Přijměte výchozí umístění v **zvolte složku** pole nebo zadejte jiné umístění. Vyberte tlačítko **Publikovat**.


# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[Visual Studio Code / .NET Core CLI](#tab/visual-studio-code+netcore-cli)

Použití [dotnet publikovat](/dotnet/core/tools/dotnet-publish) příkaz pro publikování aplikací s konfiguraci vydané verze:

```console
dotnet publish -c Release
```

---

Publikování aktivační události aplikace [obnovení](/dotnet/core/tools/dotnet-restore) závislostí projektu a [sestavení](/dotnet/core/tools/dotnet-build) projektu před vytvořením prostředků pro nasazení. Jako součást procesu sestavení jsou odebrány nepoužívané metod a sestavení snížit velikost ke stažení aplikace a časy načtení.

Aplikace na straně klienta Blazor publikovaná */bin/vydání / {CÍLOVÁ ARCHITEKTURA} / dist* složky. Aplikace na straně serveru Blazor publikovaná */bin/vydání / {CÍLOVÁ ARCHITEKTURA} / publish* složky.

Prostředky ve složce jsou nasazené na webový server. Nasazení může být ruční nebo automatizované proces v závislosti na vývojové nástroje, které používá.

## <a name="deployment"></a>Nasazení

Pokyny k nasazení naleznete v následujících tématech:

* <xref:host-and-deploy/blazor/client-side>
* <xref:host-and-deploy/blazor/server-side>

## <a name="blazor-serverless-hosting-with-azure-storage"></a>Blazor bez serveru, hostování v Azure Storage

Blazor aplikace na straně klienta je možné dodávat z [služby Azure Storage](https://azure.microsoft.com/services/storage/) jako statického obsahu přímo z kontejneru úložiště.

Další informace najdete v tématu [hostitele a nasadit Blazor straně klienta (samostatné nasazení): Azure Storage](xref:host-and-deploy/blazor/client-side#azure-storage).
