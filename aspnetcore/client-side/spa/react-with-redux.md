---
title: Šablona projektu React s Redux pomocí ASP.NET Core
author: SteveSandersonMS
description: Zjistěte, jak začít pracovat s ASP.NET Core jedné stránky aplikace (SPA) šablona projektu pro React s Reduxem a vytvořit aplikaci react.
monikerRange: '>= aspnetcore-2.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/21/2018
uid: spa/react-with-redux
ms.openlocfilehash: c1aedcf1e14a9e7b339b60dd02c4267cd5945a49
ms.sourcegitcommit: 42a8164b8aba21f322ffefacb92301bdfb4d3c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54341605"
---
# <a name="use-the-react-with-redux-project-template-with-aspnet-core"></a>Šablona projektu React s Redux pomocí ASP.NET Core

::: moniker range="= aspnetcore-2.0"

> [!NOTE]
> Tato dokumentace nemá týkají React s Redux šablony projektu, které jsou součástí ASP.NET Core 2.0. To se vztahují na novější React s Redux šablonu, kterou můžete aktualizovat ručně. Šablona je k dispozici v ASP.NET Core 2.1 nebo novější.

::: moniker-end

Aktualizovaná šablona projektu React s Reduxem poskytuje příhodný výchozí bod pro aplikace ASP.NET Core pomocí React, Reduxem, a [vytvořit aplikaci react](https://github.com/facebookincubator/create-react-app) konvence (CRA) k implementaci bohatě vybaveným a na straně klienta uživatelské rozhraní (UI).

S výjimkou příkazu pro vytvoření projektu všechny informace o šabloně React s Redux je stejný jako šablona React. Chcete-li vytvořit tento typ projektu, spusťte `dotnet new reactredux` místo `dotnet new react`. Další informace o funkcích, které jsou společné pro obě šablony založené na React najdete v tématu [React dokumentaci k šabloně](xref:spa/react).

Informace o konfiguraci React s Redux dílčí aplikace ve službě IIS najdete v tématu [2.1 šablony ReactRedux: Nelze použít jednostránková aplikace ve službě IIS (aspnet/Šablonování &num;555)](https://github.com/aspnet/Templating/issues/555).
