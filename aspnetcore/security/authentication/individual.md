---
title: Články založené na ASP.NET Core projektech vytvořených pomocí individuálních uživatelských účtů
author: rick-anderson
description: Objevte články na základě ASP.NET Core projektů vytvořených pomocí individuálních uživatelských účtů.
ms.author: riande
ms.date: 11/30/2017
uid: security/authentication/individual
ms.openlocfilehash: 91c5665dc50124b3ba09bdcfbf3ba501f684c604
ms.sourcegitcommit: 9e85c2562df5e108d7933635c830297f484bb775
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73463033"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a>Články založené na ASP.NET Core projektech vytvořených pomocí individuálních uživatelských účtů

ASP.NET Core identita je zahrnuta v šablonách projektů v aplikaci Visual Studio s možností jednotlivé uživatelské účty.

Šablony ověřování jsou dostupné v .NET Core CLI s `-au Individual`:

::: moniker range=">= aspnetcore-2.1"

```dotnetcli
dotnet new mvc -au Individual
dotnet new webapp -au Individual
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```dotnetcli
dotnet new mvc -au Individual
dotnet new razor -au Individual
```

::: moniker-end

Podívejte se na [Tento problém GitHubu](https://github.com/aspnet/AspNetCore/issues/5833) pro ověřování webového rozhraní API.

<a name="no"></a>

## <a name="no-authentication"></a>Bez ověřování

Ověřování je zadáno v .NET Core CLI s možností `-au`. V aplikaci Visual Studio je k dispozici dialog **Změna ověřování** pro nové webové aplikace. Výchozí pro nové webové aplikace v aplikaci Visual Studio není **ověřování**.

Projekty vytvořené bez ověřování:

* Neobsahujte webové stránky a uživatelské rozhraní pro přihlášení a odhlášení.
* Neobsahujte ověřovací kód.

<a name="win"></a>

## <a name="windows-authentication"></a>Ověřování systému Windows

Ověřování systému Windows je zadáno pro nové webové aplikace v .NET Core CLI s možností `-au Windows`. V aplikaci Visual Studio je v dialogovém okně pro **změnu ověřování** k dispozici možnost **ověřování systému Windows** .

Pokud je vybraná možnost ověřování systému Windows, aplikace je nakonfigurovaná tak, aby používala [modul služby IIS pro ověřování systému Windows](xref:host-and-deploy/iis/modules). Ověřování systému Windows je určeno pro intranetové weby.

## <a name="additional-resources"></a>Další zdroje

V následujících článcích se dozvíte, jak používat kód vygenerovaný v ASP.NET Core šablonách, které používají jednotlivé uživatelské účty:

* [Potvrzení účtu a obnovení hesla v ASP.NET Core](xref:security/authentication/accconfirm)
* [Vytvoření aplikace ASP.NET Core s uživatelskými daty chráněnými autorizací](xref:security/authorization/secure-data)
