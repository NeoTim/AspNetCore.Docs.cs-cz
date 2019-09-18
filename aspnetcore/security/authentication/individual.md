---
title: Články založené na ASP.NET Core projektech vytvořených pomocí individuálních uživatelských účtů
author: rick-anderson
description: Objevte články na základě ASP.NET Core projektů vytvořených pomocí individuálních uživatelských účtů.
ms.author: riande
ms.date: 11/30/2017
uid: security/authentication/individual
ms.openlocfilehash: cf548417268a8587787471b9ed91c0ed109fbee9
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080705"
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

Ověřování je zadáno v .NET Core CLI s `-au` možností. V aplikaci Visual Studio je k dispozici dialog **Změna ověřování** pro nové webové aplikace. Výchozí pro nové webové aplikace v aplikaci Visual Studio není **ověřování**.

Projekty vytvořené bez ověřování:

* Neobsahujte webové stránky a uživatelské rozhraní pro přihlášení a odhlášení.
* Neobsahujte ověřovací kód.

<a name="win"></a>

## <a name="windows-authentication"></a>Ověřování systému Windows

Ověřování systému Windows je zadáno pro nové webové aplikace v .NET Core CLI s `-au Windows` možností. V aplikaci Visual Studio je v dialogovém okně pro **změnu ověřování** k dispozici možnost **ověřování systému Windows** .

Pokud je vybraná možnost ověřování systému Windows, aplikace je nakonfigurovaná tak, aby používala [modul služby IIS pro ověřování systému Windows](xref:host-and-deploy/iis/modules). Ověřování systému Windows je určeno pro intranetové weby.

## <a name="additional-resources"></a>Další zdroje

V následujících článcích se dozvíte, jak používat kód vygenerovaný v ASP.NET Core šablonách, které používají jednotlivé uživatelské účty:

* [Dvoufaktorové ověřování přes SMS](xref:security/authentication/2fa)
* [Potvrzení účtu a obnovení hesla v ASP.NET Core](xref:security/authentication/accconfirm)
* [Vytvoření aplikace ASP.NET Core s uživatelskými daty chráněnými autorizací](xref:security/authorization/secure-data)
