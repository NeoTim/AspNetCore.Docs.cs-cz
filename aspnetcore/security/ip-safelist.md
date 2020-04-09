---
title: Seznam bezpečných IP adres klientů pro ASP.NET Core
author: damienbod
description: Přečtěte si, jak psát middleware nebo akční filtry pro ověření vzdálených IP adres podle seznamu schválených IP adres.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2020
uid: security/ip-safelist
ms.openlocfilehash: 2db879a6918245cbacff8b1a5dc15786ffab6a34
ms.sourcegitcommit: 196e4a36df5be5b04fedcff484a4261f8046ec57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80471790"
---
# <a name="client-ip-safelist-for-aspnet-core"></a>Seznam bezpečných IP adres klientů pro ASP.NET Core

Damien [Bowden](https://twitter.com/damien_bod) a [Tom Dykstra](https://github.com/tdykstra)
 
Tento článek ukazuje tři způsoby implementace seznamu bezpečných adres IP (označovaný také jako seznam povolených adres) v aplikaci ASP.NET Core. Doprovodná ukázková aplikace demonstruje všechny tři přístupy. Můžete použít:

* Middleware pro kontrolu vzdálené IP adresy každého požadavku.
* Filtry akcí MVC pro kontrolu vzdálené IP adresy požadavků na konkrétní řadiče nebo metody akce.
* Razor Pages filtry pro kontrolu vzdálené IP adresy žádostí o razor stránek.

V každém případě je řetězec obsahující schválené IP adresy klienta uložen v nastavení aplikace. Middleware nebo filtr:

* Analyzuje řetězec do pole. 
* Zkontroluje, zda v poli existuje vzdálená adresa IP.

Přístup je povolen, pokud pole obsahuje adresu IP. V opačném případě je vrácen zakázaný stavový kód HTTP 403.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="ip-address-safelist"></a>Seznam bezpečných ADRES IP

V ukázkové aplikaci je seznam bezpečných IP adres:

* Definováno `AdminSafeList` vlastností v souboru *appsettings.json.*
* Řetězec oddělený středníkem, který může obsahovat adresy [protokolu IP verze 4 (IPv4)](https://wikipedia.org/wiki/IPv4) i protokolu [IPv6.](https://wikipedia.org/wiki/IPv6)

[!code-json[](ip-safelist/samples/3.x/ClientIpAspNetCore/appsettings.json?range=1-3&highlight=2)]

V předchozím příkladu jsou povoleny adresy `127.0.0.1` IPv4 a `192.168.1.5` a adresa `::1` zpětné smyčky IPv6 (komprimovaný formát pro). `0:0:0:0:0:0:0:1`

## <a name="middleware"></a>Middleware

Metoda `Startup.Configure` přidá vlastní `AdminSafeListMiddleware` middleware typ kanálu žádosti aplikace. Safelist je načten s poskytovatelem konfigurace .NET Core a je předán jako parametr konstruktoru.

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureAddMiddleware)]

Middleware analyzuje řetězec do pole a hledá vzdálené IP adresy v poli. Pokud není nalezena vzdálená IP adresa, middleware vrátí HTTP 403 Forbidden. Tento proces ověření je vynechán pro požadavky HTTP GET.

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Middlewares/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a>Filtr akce

Pokud chcete bezpečné řízení přístupu řízené bezpečným seznamem pro konkrétní řadiče MVC nebo metody akce, použijte filtr akce. Příklad:

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckActionFilter.cs?name=snippet_ClassOnly)]

V `Startup.ConfigureServices`aplikacích přidejte filtr akcí do kolekce filtrů MVC. V následujícím příkladu `ClientIpCheckActionFilter` je přidán filtr akce. Safelist a instance protokolování konzoly jsou předány jako parametry konstruktoru.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

Filtr akce pak lze použít na řadič nebo metodu akce s atributem [[ServiceFilter]:](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute)

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_ActionFilter&highlight=1)]

V ukázkové aplikaci se filtr akce použije `Get` na metodu akce kontroleru. Při testování aplikace odesláním:

* Požadavek HTTP GET, `[ServiceFilter]` atribut ověří IP adresu klienta. Pokud je povolen `Get` přístup k metodě akce, je filtrem akce a metodou akce vytvořena varianta následujícího výstupu konzoly:

    ```
    dbug: ClientIpSafelistComponents.Filters.ClientIpCheckActionFilter[0]
          Remote IpAddress: ::1
    dbug: ClientIpAspNetCore.Controllers.ValuesController[0]
          successful HTTP GET    
    ```

* Sloveso požadavku HTTP jiné `AdminSafeListMiddleware` než GET, middleware ověří IP adresu klienta.

## <a name="razor-pages-filter"></a>Holicí strojek stránky, filtr

Pokud chcete pro aplikaci Razor Pages řídit přístup řízený bezpečným seznamem, použijte filtr Razor Pages. Příklad:

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckPageFilter.cs?name=snippet_ClassOnly)]

V `Startup.ConfigureServices`, povolte filtr Razor Pages přidáním do kolekce filtrů MVC. V následujícím příkladu `ClientIpCheckPageFilter` je přidán filtr Razor Pages. Safelist a instance protokolování konzoly jsou předány jako parametry konstruktoru.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

Když je požadována stránka *Index Razor* ukázkové aplikace, filtr Razor Pages ověří IP adresu klienta. Filtr vytváří variaci následujícího výstupu konzoly:

```
dbug: ClientIpSafelistComponents.Filters.ClientIpCheckPageFilter[0]
      Remote IpAddress: ::1
```

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/middleware/index>
* [Filtry akcí](xref:mvc/controllers/filters#action-filters)
* <xref:razor-pages/filter>
