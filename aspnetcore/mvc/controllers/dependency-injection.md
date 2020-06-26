---
title: Vkládání závislostí do řadičů v ASP.NET Core
author: ardalis
description: Zjistíte, jak ASP.NET Core řadiče MVC vyžádají své závislosti explicitně prostřednictvím jejich konstruktorů se vkládáním závislostí v ASP.NET Core.
ms.author: riande
ms.date: 02/24/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: 67861b2cdb946f7cd630770507181ee853b7fbfd
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408419"
---
# <a name="dependency-injection-into-controllers-in-aspnet-core"></a>Vkládání závislostí do řadičů v ASP.NET Core

<a name="dependency-injection-controllers"></a>

Od [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT)a [Steve Smith](https://github.com/ardalis)

ASP.NET Core řadiče MVC vyžádají závislosti explicitně prostřednictvím konstruktorů. ASP.NET Core obsahuje integrovanou podporu pro [vkládání závislostí (di)](xref:fundamentals/dependency-injection). DI usnadňuje testování a údržbu aplikací.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="constructor-injection"></a>Injektáže konstruktoru

Služby jsou přidány jako parametr konstruktoru a modul runtime tuto službu vyřeší z kontejneru služby. Služby jsou obvykle definovány pomocí rozhraní. Zvažte například aplikaci, která vyžaduje aktuální čas. Služba zpřístupňuje následující rozhraní `IDateTime` :

[!code-csharp[](dependency-injection/sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

Následující kód implementuje `IDateTime` rozhraní:

[!code-csharp[](dependency-injection/sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

Přidejte službu do kontejneru služby:

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

Další informace o <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> najdete v tématu [di životnosti služeb](xref:fundamentals/dependency-injection#service-lifetimes).

Následující kód zobrazuje pozdrav uživatele na základě denního času:

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

Spusťte aplikaci a zobrazí se zpráva na základě času.

## <a name="action-injection-with-fromservices"></a>Vkládání akcí pomocí FromServices

<xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute>Umožňuje vložení služby přímo do metody akce bez použití injektáže konstruktoru:

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a>Nastavení přístupu z kontroleru

Běžným vzorem je přístup k aplikaci nebo konfiguračnímu nastavení v rámci kontroleru. *Vzor možností* popsaný v tématu <xref:fundamentals/configuration/options> je preferovaný přístup ke správě nastavení. Obecně se nemusíte přímo vkládat <xref:Microsoft.Extensions.Configuration.IConfiguration> do kontroleru.

Vytvořte třídu, která představuje možnosti. Například:

[!code-csharp[](dependency-injection/sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

Přidejte třídu Configuration do kolekce Services:

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

Nakonfigurujte aplikaci tak, aby si přečetla nastavení ze souboru ve formátu JSON:

[!code-csharp[](dependency-injection/sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

Následující kód požádá o `IOptions<SampleWebSettings>` nastavení z kontejneru služby a použije je v `Index` metodě:

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a>Další zdroje

* V tématu <xref:mvc/controllers/testing> se dozvíte, jak usnadnit testování kódu tím, že explicitně vyžádáte závislosti v řadičích.

* [Nahraďte výchozí kontejner pro vkládání závislostí s implementací třetí strany](xref:fundamentals/dependency-injection#default-service-container-replacement).
