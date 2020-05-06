---
title: IP adresa klienta Safelist pro ASP.NET Core
author: damienbod
description: Naučte se psát middleware nebo filtry akcí pro ověření vzdálených IP adres pro seznam schválených IP adres.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/ip-safelist
ms.openlocfilehash: 7923a81e72124cfb0e11e3c1ac327c1e32194b21
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776497"
---
# <a name="client-ip-safelist-for-aspnet-core"></a>IP adresa klienta Safelist pro ASP.NET Core

[Damien Bowden](https://twitter.com/damien_bod) a [Dykstra](https://github.com/tdykstra)
 
Tento článek ukazuje tři způsoby implementace IP adresy Safelist (označované také jako seznam povolených) v aplikaci ASP.NET Core. Doprovodné ukázková aplikace předvádí všechny tři přístupy. Můžete použít:

* Middleware pro kontrolu vzdálené IP adresy každého požadavku.
* Filtry akcí MVC pro kontrolu vzdálené IP adresy žádostí o konkrétní řadiče nebo metody akcí.
* RazorStránky filtrují, aby kontrolovaly vzdálené IP adresy žádostí Razor o stránky.

V každém případě je řetězec, který obsahuje schválené IP adresy klienta, uložen v nastavení aplikace. Middleware nebo filtr:

* Analyzuje řetězec do pole. 
* Kontroluje, zda v poli existuje vzdálená IP adresa.

Přístup je povolený, pokud pole obsahuje IP adresu. V opačném případě se vrátí stavový kód HTTP 403 zakázáno.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="ip-address-safelist"></a>Safelist IP adres

V ukázkové aplikaci se IP adresa Safelist:

* Definováno `AdminSafeList` vlastností v souboru *appSettings. JSON* .
* Řetězec oddělený středníkem, který může obsahovat adresy [Internet Protocol verze 4 (IPv4)](https://wikipedia.org/wiki/IPv4) i [Internet Protocol verze 6 (IPv6)](https://wikipedia.org/wiki/IPv6) .

[!code-json[](ip-safelist/samples/3.x/ClientIpAspNetCore/appsettings.json?range=1-3&highlight=2)]

V předchozím příkladu jsou povoleny adresy `127.0.0.1` IPv4 a `192.168.1.5` adresa zpětné smyčky IPv6 `::1` (komprimovaný formát pro `0:0:0:0:0:0:0:1`).

## <a name="middleware"></a>Middleware

`Startup.Configure` Metoda přidá vlastní `AdminSafeListMiddleware` typ middlewaru do kanálu požadavků aplikace. Safelist se načte pomocí poskytovatele konfigurace .NET Core a předává se jako parametr konstruktoru.

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureAddMiddleware)]

Middleware analyzuje řetězec do pole a vyhledá vzdálenou IP adresu v poli. Pokud se vzdálená IP adresa nenajde, middleware vrátí HTTP 403 zakázáno. Tento proces ověřování se pro požadavky HTTP GET nepoužívá.

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Middlewares/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a>Filtr akcí

Pokud chcete řízení přístupu řízenému Safelist pro konkrétní řadiče MVC nebo metody akcí, použijte filtr akcí. Příklad:

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckActionFilter.cs?name=snippet_ClassOnly)]

V `Startup.ConfigureServices`přidejte filtr akcí do kolekce filtry MVC. V následujícím příkladu je přidán filtr `ClientIpCheckActionFilter` akcí. Safelist a instance protokolovacího nástroje jsou předány jako parametry konstruktoru.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

Filtr akce lze potom použít pro metodu kontroleru nebo akce s atributem [[ServiceFilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) :

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_ActionFilter&highlight=1)]

V ukázkové aplikaci se filtr akcí aplikuje na metodu `Get` akce kontroleru. Při testování aplikace odesláním:

* Požadavek `[ServiceFilter]` HTTP GET ověří IP adresu klienta. Pokud je povolen přístup k metodě `Get` Action, variace následujícího výstupu konzoly je vytvořena metodou Action Filter a Action:

    ```
    dbug: ClientIpSafelistComponents.Filters.ClientIpCheckActionFilter[0]
          Remote IpAddress: ::1
    dbug: ClientIpAspNetCore.Controllers.ValuesController[0]
          successful HTTP GET    
    ```

* Příkaz žádosti HTTP jiný než GET, `AdminSafeListMiddleware` middleware ověří IP adresu klienta.

## <a name="razor-pages-filter"></a>RazorFiltr stránek

Pokud chcete ovládací prvek přístupu řízený Safelist pro aplikaci Razor Pages, použijte filtr Razor stránky. Příklad:

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckPageFilter.cs?name=snippet_ClassOnly)]

V `Startup.ConfigureServices`nástroji Povolte filtr Razor stránky přidáním do kolekce filtry MVC. V následujícím příkladu `ClientIpCheckPageFilter` Razor je přidán filtr stránky. Safelist a instance protokolovacího nástroje jsou předány jako parametry konstruktoru.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

Po vyžádání stránky *indexu* Razor ukázkové aplikace ověří filtr Razor stránek IP adresu klienta. Filtr vytváří variaci následujícího výstupu konzoly:

```
dbug: ClientIpSafelistComponents.Filters.ClientIpCheckPageFilter[0]
      Remote IpAddress: ::1
```

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/middleware/index>
* [Filtry akcí](xref:mvc/controllers/filters#action-filters)
* <xref:razor-pages/filter>
