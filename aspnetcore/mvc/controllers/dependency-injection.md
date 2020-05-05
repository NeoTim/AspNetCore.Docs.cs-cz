---
title: Vkládání závislostí do řadičů v ASP.NET Core
author: ardalis
description: Zjistíte, jak ASP.NET Core řadiče MVC vyžádají své závislosti explicitně prostřednictvím jejich konstruktorů se vkládáním závislostí v ASP.NET Core.
ms.author: riande
ms.date: 02/24/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: a7df6a5fa2d49efc332c4684ea8192f143cdebf5
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775697"
---
# <a name="dependency-injection-into-controllers-in-aspnet-core"></a><span data-ttu-id="ecedb-103">Vkládání závislostí do řadičů v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ecedb-103">Dependency injection into controllers in ASP.NET Core</span></span>

<a name="dependency-injection-controllers"></a>

<span data-ttu-id="ecedb-104">Od [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT)a [Steve Smith](https://github.com/ardalis)</span><span class="sxs-lookup"><span data-stu-id="ecedb-104">By [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://github.com/ardalis)</span></span>

<span data-ttu-id="ecedb-105">ASP.NET Core řadiče MVC vyžádají závislosti explicitně prostřednictvím konstruktorů.</span><span class="sxs-lookup"><span data-stu-id="ecedb-105">ASP.NET Core MVC controllers request dependencies explicitly via constructors.</span></span> <span data-ttu-id="ecedb-106">ASP.NET Core obsahuje integrovanou podporu pro [vkládání závislostí (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="ecedb-106">ASP.NET Core has built-in support for [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="ecedb-107">DI usnadňuje testování a údržbu aplikací.</span><span class="sxs-lookup"><span data-stu-id="ecedb-107">DI makes apps easier to test and maintain.</span></span>

<span data-ttu-id="ecedb-108">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ecedb-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="constructor-injection"></a><span data-ttu-id="ecedb-109">Injektáže konstruktoru</span><span class="sxs-lookup"><span data-stu-id="ecedb-109">Constructor Injection</span></span>

<span data-ttu-id="ecedb-110">Služby jsou přidány jako parametr konstruktoru a modul runtime tuto službu vyřeší z kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="ecedb-110">Services are added as a constructor parameter, and the runtime resolves the service from the service container.</span></span> <span data-ttu-id="ecedb-111">Služby jsou obvykle definovány pomocí rozhraní.</span><span class="sxs-lookup"><span data-stu-id="ecedb-111">Services are typically defined using interfaces.</span></span> <span data-ttu-id="ecedb-112">Zvažte například aplikaci, která vyžaduje aktuální čas.</span><span class="sxs-lookup"><span data-stu-id="ecedb-112">For example, consider an app that requires the current time.</span></span> <span data-ttu-id="ecedb-113">`IDateTime` Služba zpřístupňuje následující rozhraní:</span><span class="sxs-lookup"><span data-stu-id="ecedb-113">The following interface exposes the `IDateTime` service:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

<span data-ttu-id="ecedb-114">Následující kód implementuje `IDateTime` rozhraní:</span><span class="sxs-lookup"><span data-stu-id="ecedb-114">The following code implements the `IDateTime` interface:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

<span data-ttu-id="ecedb-115">Přidejte službu do kontejneru služby:</span><span class="sxs-lookup"><span data-stu-id="ecedb-115">Add the service to the service container:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

<span data-ttu-id="ecedb-116">Další informace o <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>najdete v tématu [di životnosti služeb](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="ecedb-116">For more information on <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, see [DI service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="ecedb-117">Následující kód zobrazuje pozdrav uživatele na základě denního času:</span><span class="sxs-lookup"><span data-stu-id="ecedb-117">The following code displays a greeting to the user based on the time of day:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="ecedb-118">Spusťte aplikaci a zobrazí se zpráva na základě času.</span><span class="sxs-lookup"><span data-stu-id="ecedb-118">Run the app and a message is displayed based on the time.</span></span>

## <a name="action-injection-with-fromservices"></a><span data-ttu-id="ecedb-119">Vkládání akcí pomocí FromServices</span><span class="sxs-lookup"><span data-stu-id="ecedb-119">Action injection with FromServices</span></span>

<span data-ttu-id="ecedb-120"><xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> Umožňuje vložení služby přímo do metody akce bez použití injektáže konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="ecedb-120">The <xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> enables injecting a service directly into an action method without using constructor injection:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a><span data-ttu-id="ecedb-121">Nastavení přístupu z kontroleru</span><span class="sxs-lookup"><span data-stu-id="ecedb-121">Access settings from a controller</span></span>

<span data-ttu-id="ecedb-122">Běžným vzorem je přístup k aplikaci nebo konfiguračnímu nastavení v rámci kontroleru.</span><span class="sxs-lookup"><span data-stu-id="ecedb-122">Accessing app or configuration settings from within a controller is a common pattern.</span></span> <span data-ttu-id="ecedb-123">*Vzor možností* popsaný v <xref:fundamentals/configuration/options> tématu je preferovaný přístup ke správě nastavení.</span><span class="sxs-lookup"><span data-stu-id="ecedb-123">The *options pattern* described in <xref:fundamentals/configuration/options> is the preferred approach to manage settings.</span></span> <span data-ttu-id="ecedb-124">Obecně se nemusíte <xref:Microsoft.Extensions.Configuration.IConfiguration> přímo vkládat do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="ecedb-124">Generally, don't directly inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into a controller.</span></span>

<span data-ttu-id="ecedb-125">Vytvořte třídu, která představuje možnosti.</span><span class="sxs-lookup"><span data-stu-id="ecedb-125">Create a class that represents the options.</span></span> <span data-ttu-id="ecedb-126">Příklad:</span><span class="sxs-lookup"><span data-stu-id="ecedb-126">For example:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

<span data-ttu-id="ecedb-127">Přidejte třídu Configuration do kolekce Services:</span><span class="sxs-lookup"><span data-stu-id="ecedb-127">Add the configuration class to the services collection:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

<span data-ttu-id="ecedb-128">Nakonfigurujte aplikaci tak, aby si přečetla nastavení ze souboru ve formátu JSON:</span><span class="sxs-lookup"><span data-stu-id="ecedb-128">Configure the app to read the settings from a JSON-formatted file:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

<span data-ttu-id="ecedb-129">Následující kód požádá o `IOptions<SampleWebSettings>` nastavení z kontejneru služby a použije je v `Index` metodě:</span><span class="sxs-lookup"><span data-stu-id="ecedb-129">The following code requests the `IOptions<SampleWebSettings>` settings from the service container and uses them in the `Index` method:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="ecedb-130">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="ecedb-130">Additional resources</span></span>

* <span data-ttu-id="ecedb-131">V <xref:mvc/controllers/testing> tématu se dozvíte, jak usnadnit testování kódu tím, že explicitně vyžádáte závislosti v řadičích.</span><span class="sxs-lookup"><span data-stu-id="ecedb-131">See <xref:mvc/controllers/testing> to learn how to make code easier to test by explicitly requesting dependencies in controllers.</span></span>

* <span data-ttu-id="ecedb-132">[Nahraďte výchozí kontejner pro vkládání závislostí s implementací třetí strany](xref:fundamentals/dependency-injection#default-service-container-replacement).</span><span class="sxs-lookup"><span data-stu-id="ecedb-132">[Replace the default dependency injection container with a third party implementation](xref:fundamentals/dependency-injection#default-service-container-replacement).</span></span>
