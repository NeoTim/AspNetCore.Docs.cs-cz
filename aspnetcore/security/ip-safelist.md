---
title: Safelist IP klienta pro ASP.NET Core
author: damienbod
description: Další informace o zápisu Middleware nebo akce filtry k ověření vzdálené IP adresy na seznam schválených IP adres.
ms.author: tdykstra
ms.custom: mvc
ms.date: 08/31/2018
uid: security/ip-safelist
ms.openlocfilehash: cfbb50ea33ae3af577f13b00bccc75fe0be57f79
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64903372"
---
# <a name="client-ip-safelist-for-aspnet-core"></a>Safelist IP klienta pro ASP.NET Core

Podle [Damien překážka](https://twitter.com/damien_bod) a [Petr Dykstra](https://github.com/tdykstra)
 
Tento článek popisuje tři způsoby, jak implementovat safelist IP (označované také jako seznam povolených) v aplikaci ASP.NET Core. Můžete použít:

* Middleware pro vzdálené IP adresy každé žádosti o kontrolu.
* Filtry akce ke kontrole Vzdálená IP adresa žádosti o konkrétní řadiče nebo metody akce.
* Filtry stránky Razor ke kontrole Vzdálená IP adresa požadavků pro stránky Razor.

Ukázková aplikace ukazuje oba přístupy. V obou případech je uložen řetězec obsahující schválených klientských IP adres v nastavení aplikace. Middleware nebo filtr analyzuje řetězec do seznam a zkontroluje, zda vzdálené IP je v seznamu. V opačném případě se vrátí stavový kód HTTP 403 Zakázáno.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples/2.x/ClientIpAspNetCore) ([stažení](xref:index#how-to-download-a-sample))

## <a name="the-safelist"></a>Safelist

V seznamu je nakonfigurovaný v *appsettings.json* souboru. Středníkem oddělený seznam a může obsahovat adresy IPv4 a IPv6.

[!code-json[](ip-safelist/samples/2.x/ClientIpAspNetCore/appsettings.json?highlight=2)]

## <a name="middleware"></a>Middleware

`Configure` Metoda přidá middleware a předá řetězec safelist v parametr konstruktoru.

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_Configure&highlight=7)]

Middleware analyzuje řetězec do pole a hledá vzdálenou IP adresu v poli. Pokud není nalezena Vzdálená IP adresa, middleware vrátí HTTP 401 zakázáno. Tento proces ověřování přeskočí pro požadavky HTTP Get.

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a>Akce filtru

Pokud chcete safelist pouze pro metody akce nebo konkrétní řadiče, použijte filtr akce. Tady je příklad: 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIdCheckFilter.cs)]

Filtr akce se přidá do kontejneru služby.

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

Filtr lze pak použít v kontroleru nebo metodě akce.

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_Filter&highlight=1)]

V ukázkové aplikaci filtr platí pro `Get` metody. Ano, při testování aplikace pomocí odesílání `Get` žádosti rozhraní API, atribut ověřuje IP adresu klienta. Při testování voláním rozhraní API pomocí jiné metody HTTP, middleware ověřování IP adresu klienta.

## <a name="razor-pages-filter"></a>Filtr stránek Razor 

Pokud chcete safelist pro aplikace Razor Pages, použijte filtr stránky Razor. Tady je příklad: 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIdCheckPageFilter.cs)]

Tento filtr je povoleno přidáním do kolekce filtrů MVC.

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

Při spuštění aplikace a požádat o stránku Razor, filtr stránky Razor ověřuje IP adresu klienta.

## <a name="next-steps"></a>Další kroky

[Další informace o ASP.NET Core Middleware](xref:fundamentals/middleware/index).
