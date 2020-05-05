---
title: Migrace konfigurace do ASP.NET Core
author: ardalis
description: Naučte se migrovat konfiguraci z projektu ASP.NET MVC do projektu MVC ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/configuration
ms.openlocfilehash: f65db927d79224695861101aff00897315c6e0b2
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777225"
---
# <a name="migrate-configuration-to-aspnet-core"></a>Migrace konfigurace do ASP.NET Core

[Steve Smith](https://ardalis.com/) a [Scott Addie](https://scottaddie.com)

V předchozím článku jsme začali [migrovat projekt ASP.NET MVC na ASP.NET Core MVC](xref:migration/mvc). V tomto článku migrujeme konfiguraci.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/configuration/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="setup-configuration"></a>Konfigurace instalace

ASP.NET Core už nepoužívá soubory *Global. asax* a *Web. config* , které používají předchozí verze ASP.NET. V dřívějších verzích ASP.NET byla logika spouštění aplikace umístěna v `Application_StartUp` metodě v rámci *Global. asax*. Později v ASP.NET MVC byl do kořene projektu zahrnut soubor *Startup.cs* ; a bylo voláno při spuštění aplikace. ASP.NET Core úspěšně přijal tento přístup tím, že do souboru *Startup.cs* umístí veškerou spouštěcí logiku.

Soubor *Web. config* byl také nahrazen ASP.NET Core. Vlastní konfiguraci teď můžete nakonfigurovat jako součást postupu spuštění aplikace popsaného v *Startup.cs*. Konfigurace může stále využívat soubory XML, ale obvykle ASP.NET Core projekty umístí konfigurační hodnoty do souboru ve formátu JSON, například *appSettings. JSON*. Konfigurační systém ASP.NET Core může také snadno získat přístup k proměnným prostředí, což může poskytovat [bezpečnější a robustní umístění](xref:security/app-secrets) pro hodnoty specifické pro prostředí. To platí zejména pro tajné klíče, jako jsou připojovací řetězce a klíče rozhraní API, které by neměly být vráceny do správy zdrojového kódu. Další informace o konfiguraci v ASP.NET Core najdete v tématu o [konfiguraci](xref:fundamentals/configuration/index) .

V tomto článku Začínáme s částečně migrovaným ASP.NET Core projektem z [předchozího článku](xref:migration/mvc). Chcete-li nastavit konfiguraci, přidejte do souboru *Startup.cs* , který se nachází v kořenovém adresáři projektu, následující konstruktor a vlastnost:

[!code-csharp[](configuration/samples/WebApp1/src/WebApp1/Startup.cs?range=11-16)]

Všimněte si, že v tomto okamžiku se soubor *Startup.cs* nebude kompilovat, protože stále potřebujete přidat následující `using` příkaz:

```csharp
using Microsoft.Extensions.Configuration;
```

Přidejte soubor *appSettings. JSON* do kořenového adresáře projektu pomocí vhodné šablony položky:

![Přidat JSON pro AppSettings](configuration/_static/add-appsettings-json.png)

## <a name="migrate-configuration-settings-from-webconfig"></a>Migrovat konfigurační nastavení ze souboru Web. config

Náš projekt ASP.NET MVC zahrnoval požadovaný připojovací řetězec databáze v *souboru Web. config*v `<connectionStrings>` elementu. V našem ASP.NET Corem projektu budeme tyto informace ukládat do souboru *appSettings. JSON* . Otevřete *appSettings. JSON*a Všimněte si, že již obsahuje následující:

[!code-json[](../migration/configuration/samples/WebApp1/src/WebApp1/appsettings.json?highlight=4)]

Na zvýrazněný řádek zobrazený výše změňte název databáze z **_CHANGE_ME** na název vaší databáze.

## <a name="summary"></a>Souhrn

ASP.NET Core umístí veškerou spouštěcí logiku aplikace do jednoho souboru, ve kterém je možné definovat a konfigurovat potřebné služby a závislosti. Nahrazuje soubor *Web. config* flexibilní funkcí konfigurace, která může využívat celou řadu formátů souborů, jako je JSON a proměnné prostředí.
