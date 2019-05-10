---
title: Migrace konfigurace do ASP.NET Core
author: ardalis
description: Zjistěte, jak migrovat konfigurace z projektu aplikace ASP.NET MVC do projektu aplikace ASP.NET Core MVC.
ms.author: riande
ms.date: 10/14/2016
uid: migration/configuration
ms.openlocfilehash: c379f1f64dc5ab8aeb48055124e86e4e60d93785
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64900993"
---
# <a name="migrate-configuration-to-aspnet-core"></a>Migrace konfigurace do ASP.NET Core

Podle [Steve Smith](https://ardalis.com/) a [Scott Addie](https://scottaddie.com)

V předchozím článku jsme začali [migrace projektu aplikace ASP.NET MVC do ASP.NET Core MVC](xref:migration/mvc). V tomto článku budeme migrovat konfiguraci.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/migration/configuration/samples) ([stažení](xref:index#how-to-download-a-sample))

## <a name="setup-configuration"></a>Nastavení konfigurace

ASP.NET Core se už používá *Global.asax* a *web.config* soubory, které používá předchozí verzí technologie ASP.NET. V předchozích verzích technologie ASP.NET, aplikace logiky po spuštění byl umístěn v `Application_StartUp` metody v rámci *Global.asax*. Později v ASP.NET MVC *Startup.cs* byl soubor zahrnut v kořenovém adresáři projektu; a byla volána při spuštění aplikace. ASP.NET Core přijala tento přístup úplně tak, že celé spouštěcí logiky *Startup.cs* souboru.

*Web.config* souboru nahradila také v ASP.NET Core. Konfigurace samotného lze nyní nastavit, je popsáno v postupu při spuštění aplikace v rámci *Startup.cs*. Konfigurace můžete stále využít soubory XML, ale obvykle projekty ASP.NET Core, umístí hodnoty konfigurace v souboru ve formátu JSON, jako například *appsettings.json*. Systém konfigurace ASP.NET Core můžete také snadno přistupovat k proměnné prostředí, které můžete zadat [zabezpečené a robustní umístění](xref:security/app-secrets) pro hodnoty v závislosti na prostředí. To platí zejména pro tajné kódy jako jsou připojovací řetězce a klíče rozhraní API, které by neměly být zařazeno do správy zdrojového kódu. Zobrazit [konfigurace](xref:fundamentals/configuration/index) získat další informace o konfiguraci v ASP.NET Core.

Pro účely tohoto článku jsme začínáte s částečně migrovaného projektu ASP.NET Core z [předchozím článku](xref:migration/mvc). Chcete-li nastavit konfiguraci, přidejte následující konstruktor a nastavte *Startup.cs* souboru umístěného v kořenovém adresáři projektu:

[!code-csharp[](configuration/samples/WebApp1/src/WebApp1/Startup.cs?range=11-16)]

Všimněte si, který v tomto okamžiku *Startup.cs* soubor nebude kompilovat, protože stále je potřeba přidat následující `using` – příkaz:

```csharp
using Microsoft.Extensions.Configuration;
```

Přidat *appsettings.json* souboru do kořenového adresáře projektu pomocí šablony položky odpovídající:

![Přidat AppSettings JSON](configuration/_static/add-appsettings-json.png)

## <a name="migrate-configuration-settings-from-webconfig"></a>Migrace nastavení konfigurace ze souboru web.config

Zahrnuté připojovací řetězec databáze v našem projektu ASP.NET MVC *web.config*v `<connectionStrings>` elementu. V našem projektu ASP.NET Core, budeme k ukládání příslušných informací v *appsettings.json* souboru. Otevřít *appsettings.json*a Všimněte si, že už obsahuje následující:

[!code-json[](../migration/configuration/samples/WebApp1/src/WebApp1/appsettings.json?highlight=4)]

V zvýrazněný řádek uvedené výše, změňte název databáze z **_CHANGE_ME** na název vaší databáze.

## <a name="summary"></a>Souhrn

ASP.NET Core umístí všechny spouštěcí logiky aplikace v jediném souboru, ve kterém může být nezbytných služeb a závislosti definované a nakonfigurovaná. Nahradí *web.config* soubor s flexibilní konfiguraci funkce, která můžete využít širokou škálu formátů souborů, jako je JSON, stejně jako proměnné prostředí.
