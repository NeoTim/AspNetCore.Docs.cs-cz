---
title: Články podle projekty ASP.NET Core, které jsou vytvořené pomocí jednotlivých uživatelských účtů
author: rick-anderson
description: Vyhledat články založené na projekty ASP.NET Core, které jsou vytvořené pomocí jednotlivých uživatelských účtů.
ms.author: riande
ms.date: 11/30/2017
uid: security/authentication/individual
ms.openlocfilehash: f9c1be16386da935382275815bb5fd5c72894b1c
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58265425"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a>Články podle projekty ASP.NET Core, které jsou vytvořené pomocí jednotlivých uživatelských účtů

ASP.NET Core Identity je součástí šablony projektů v sadě Visual Studio s parametrem "Jednotlivých uživatelských účtů".

Ověřování šablony jsou k dispozici v rozhraní .NET Core CLI s `-au Individual`:

::: moniker range=">= aspnetcore-2.1"

```console
dotnet new mvc -au Individual
dotnet new webapp -au Individual
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```console
dotnet new mvc -au Individual
dotnet new razor -au Individual
```

::: moniker-end

Zobrazit [tento problém Githubu](https://github.com/aspnet/AspNetCore/issues/5833) pro ověřování webové rozhraní API.

<a name="no"></a>

## <a name="no-authentication"></a>Bez ověřování

Ověřování je zadaný v rozhraní příkazového řádku .NET Core s `-au` možnost. V sadě Visual Studio **změna ověřování** dialogové okno je k dispozici pro nové webové aplikace. Výchozí pro nové webové aplikace v sadě Visual Studio je **bez ověřování**.

Projekty vytvořené bez ověřování:

* Neobsahují webových stránek a uživatelského rozhraní pro přihlášení a odhlášení.
* Neobsahují ověřovacího kódu.

<a name="win"></a>

## <a name="windows-authentication"></a>Ověřování systému Windows

Ověřování Windows je určená pro nové webové aplikace v rozhraní příkazového řádku .NET Core s `-au Windows` možnost. V sadě Visual Studio **změna ověřování** dialogové okno obsahuje **ověřování Windows** možnosti.

Pokud je vybrána možnost ověření Windows, aplikace je nakonfigurovaná pro používání [modul IIS ověřování Windows](xref:host-and-deploy/iis/modules). Ověřování Windows je určená pro intranetové weby.

## <a name="additional-resources"></a>Další zdroje

Následující články popisují, jak používat kód vygenerovaný v šablony ASP.NET Core, které používají jednotlivých uživatelských účtů:

* [Dvoufaktorové ověřování přes SMS](xref:security/authentication/2fa)
* [Potvrzení účtu a obnovení hesla v ASP.NET Core](xref:security/authentication/accconfirm)
* [Vytvoření aplikace ASP.NET Core s uživatelskými daty chráněnými autorizací](xref:security/authorization/secure-data)
