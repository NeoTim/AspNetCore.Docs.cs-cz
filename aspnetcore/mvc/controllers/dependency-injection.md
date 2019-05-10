---
title: Injektáž závislostí do kontrolerů v ASP.NET Core
author: ardalis
description: Zjistěte, jak ASP.NET Core MVC řadiče vyžádat jejich závislosti explicitně prostřednictvím jejich konstruktory s injektáž závislostí v ASP.NET Core.
ms.author: riande
ms.date: 02/24/2019
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: 6b08c321f4cae1f4efd8ea40300eaf4dfc2f63a1
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64903069"
---
# <a name="dependency-injection-into-controllers-in-aspnet-core"></a>Injektáž závislostí do kontrolerů v ASP.NET Core

<a name="dependency-injection-controllers"></a>

Podle [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT), a [Steve Smith](https://github.com/ardalis)

Kontrolery ASP.NET Core MVC požádat o závislosti explicitně pomocí konstruktorů. Má integrovanou podporu pro ASP.NET Core [injektáž závislostí (DI)](xref:fundamentals/dependency-injection). DI usnadňuje aplikace pro testování a udržovat.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([stažení](xref:index#how-to-download-a-sample))

## <a name="constructor-injection"></a>Vkládání konstruktor

Služby jsou přidány jako parametr konstruktoru a modul runtime řeší službu service container. Služby jsou obvykle definovány pomocí rozhraní. Zvažte například aplikaci, která vyžaduje aktuální čas. Následující rozhraní zpřístupňuje `IDateTime` služby:

[!code-csharp[](dependency-injection/sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

Následující kód implementuje `IDateTime` rozhraní:

[!code-csharp[](dependency-injection/sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

Přidání služby do služby kontejneru:

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

Další informace o <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, naleznete v tématu [životnosti služby DI](xref:fundamentals/dependency-injection#service-lifetimes).

Následující kód zobrazí pozdrav uživateli na základě času dne:

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

Spusťte aplikaci a zobrazí se zpráva, na základě času.

## <a name="action-injection-with-fromservices"></a>Vkládání akce s FromServices

<xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> Umožňuje přímo do metody akce bez použití konstruktoru vkládání vkládá služba:

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a>Nastavení přístupu z kontroleru

Přístup k aplikaci nebo konfigurace nastavení z v rámci kontroleru je běžný vzor. *Možnosti vzor* podle <xref:fundamentals/configuration/options> je oblíbený přístup ke správě nastavení. Obecně platí, není vložit přímo <xref:Microsoft.Extensions.Configuration.IConfiguration> do kontroleru.

Vytvořte třídu, která představuje možnosti. Příklad:

[!code-csharp[](dependency-injection/sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

Přidáte třídu konfigurace ke kolekci služby:

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

Konfigurace aplikace pro čtení nastavení ze souboru ve formátu JSON:

[!code-csharp[](dependency-injection/sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

Následující kód požadavky `IOptions<SampleWebSettings>` nastavení z kontejneru služeb a použije je `Index` – metoda:

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a>Další zdroje

* Zobrazit <xref:mvc/controllers/testing> se naučíte usnadňují otestovat explicitní vyžádání závislostí do kontrolerů kódu.

* [Nahraďte výchozí kontejner vkládání závislostí třetích stran implementace](xref:fundamentals/dependency-injection#default-service-container-replacement).
