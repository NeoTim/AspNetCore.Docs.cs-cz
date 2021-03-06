---
title: Migrace konfigurace do ASP.NET Core
author: ardalis
description: Naučte se migrovat konfiguraci z projektu ASP.NET MVC do projektu MVC ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/configuration
ms.openlocfilehash: 8cbb174539234039a05306ec5ab89026bc90da23
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631092"
---
# <a name="migrate-configuration-to-aspnet-core"></a>Migrace konfigurace do ASP.NET Core

[Steve Smith](https://ardalis.com/) a [Scott Addie](https://scottaddie.com)

V předchozím článku jsme začali [migrovat projekt ASP.NET MVC na ASP.NET Core MVC](xref:migration/mvc). V tomto článku migrujeme konfiguraci.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/configuration/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="setup-configuration"></a>Konfigurace instalace

ASP.NET Core už nepoužívá soubory *Global. asax* a *web.config* , které využily předchozí verze ASP.NET. V dřívějších verzích ASP.NET byla logika spouštění aplikace umístěna v `Application_StartUp` metodě v rámci *Global. asax*. Později v ASP.NET MVC byl do kořene projektu zahrnut soubor *Startup.cs* ; a bylo voláno při spuštění aplikace. ASP.NET Core úspěšně přijal tento přístup tím, že do souboru *Startup.cs* umístí veškerou spouštěcí logiku.

*web.config* soubor byl také v ASP.NET Core nahrazen. Vlastní konfiguraci teď můžete nakonfigurovat jako součást postupu spuštění aplikace popsaného v *Startup.cs*. Konfigurace může stále využívat soubory XML, ale obvykle ASP.NET Core projekty umístí konfigurační hodnoty do souboru ve formátu JSON, jako je například *appsettings.js*. Konfigurační systém ASP.NET Core může také snadno získat přístup k proměnným prostředí, což může poskytovat [bezpečnější a robustní umístění](xref:security/app-secrets) pro hodnoty specifické pro prostředí. To platí zejména pro tajné klíče, jako jsou připojovací řetězce a klíče rozhraní API, které by neměly být vráceny do správy zdrojového kódu. Další informace o konfiguraci v ASP.NET Core najdete v tématu o [konfiguraci](xref:fundamentals/configuration/index) .

V tomto článku Začínáme s částečně migrovaným ASP.NET Core projektem z [předchozího článku](xref:migration/mvc). Chcete-li nastavit konfiguraci, přidejte do souboru *Startup.cs* , který se nachází v kořenovém adresáři projektu, následující konstruktor a vlastnost:

[!code-csharp[](configuration/samples/WebApp1/src/WebApp1/Startup.cs?range=11-16)]

Všimněte si, že v tomto okamžiku se soubor *Startup.cs* nebude kompilovat, protože stále potřebujete přidat následující `using` příkaz:

```csharp
using Microsoft.Extensions.Configuration;
```

Přidejte *appsettings.js* do souboru do kořenového adresáře projektu pomocí vhodné šablony položky:

![Přidat JSON pro AppSettings](configuration/_static/add-appsettings-json.png)

## <a name="migrate-configuration-settings-from-webconfig"></a>Migrovat nastavení konfigurace z web.config

Náš projekt ASP.NET MVC zahrnoval požadovaný připojovací řetězec databáze v *web.config* `<connectionStrings>` elementu. V našem ASP.NET Corem projektu budeme tyto informace ukládat do *appsettings.jsv* souboru. Otevřete *appsettings.jsna*a Všimněte si, že již obsahuje následující:

[!code-json[](../migration/configuration/samples/WebApp1/src/WebApp1/appsettings.json?highlight=4)]

Na zvýrazněný řádek zobrazený výše změňte název databáze z **_CHANGE_ME** na název vaší databáze.

## <a name="summary"></a>Shrnutí

ASP.NET Core umístí veškerou spouštěcí logiku aplikace do jednoho souboru, ve kterém je možné definovat a konfigurovat potřebné služby a závislosti. Nahrazuje soubor *web.config* pomocí flexibilní konfigurační funkce, která může využít celou řadu formátů souborů, jako je JSON a proměnné prostředí.
