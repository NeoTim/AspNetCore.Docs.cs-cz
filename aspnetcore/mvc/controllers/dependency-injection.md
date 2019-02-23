---
title: Injektáž závislostí do kontrolerů v ASP.NET Core
author: ardalis
description: Zjistěte, jak ASP.NET Core MVC řadiče vyžádat jejich závislosti explicitně prostřednictvím jejich konstruktory s injektáž závislostí v ASP.NET Core.
ms.author: riande
ms.date: 02/24/2019
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: 898e98f4c5d472ca96c6a8ad07dddd1a4ef54fe9
ms.sourcegitcommit: b3894b65e313570e97a2ab78b8addd22f427cac8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2019
ms.locfileid: "56743826"
---
# <a name="dependency-injection-into-controllers-in-aspnet-core"></a><span data-ttu-id="690a7-103">Injektáž závislostí do kontrolerů v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="690a7-103">Dependency injection into controllers in ASP.NET Core</span></span>

<a name="dependency-injection-controllers"></a>

<span data-ttu-id="690a7-104">Podle [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT), a [Steve Smith](https://github.com/ardalis)</span><span class="sxs-lookup"><span data-stu-id="690a7-104">By [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://github.com/ardalis)</span></span>

<span data-ttu-id="690a7-105">Kontrolery ASP.NET Core MVC požádat o závislosti explicitně pomocí konstruktorů.</span><span class="sxs-lookup"><span data-stu-id="690a7-105">ASP.NET Core MVC controllers request dependencies explicitly via constructors.</span></span> <span data-ttu-id="690a7-106">Má integrovanou podporu pro ASP.NET Core [injektáž závislostí (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="690a7-106">ASP.NET Core has built-in support for [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="690a7-107">DI usnadňuje aplikace pro testování a udržovat.</span><span class="sxs-lookup"><span data-stu-id="690a7-107">DI makes apps easier to test and maintain.</span></span>

<span data-ttu-id="690a7-108">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="690a7-108">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="constructor-injection"></a><span data-ttu-id="690a7-109">Vkládání konstruktor</span><span class="sxs-lookup"><span data-stu-id="690a7-109">Constructor Injection</span></span>

<span data-ttu-id="690a7-110">Služby jsou přidány jako parametr konstruktoru a modul runtime řeší službu service container.</span><span class="sxs-lookup"><span data-stu-id="690a7-110">Services are added as a constructor parameter, and the runtime resolves the service from the service container.</span></span> <span data-ttu-id="690a7-111">Služby jsou obvykle definovány pomocí rozhraní.</span><span class="sxs-lookup"><span data-stu-id="690a7-111">Services are typically defined using interfaces.</span></span> <span data-ttu-id="690a7-112">Zvažte například aplikaci, která vyžaduje aktuální čas.</span><span class="sxs-lookup"><span data-stu-id="690a7-112">For example, consider an app that requires the current time.</span></span> <span data-ttu-id="690a7-113">Následující rozhraní zpřístupňuje `IDateTime` služby:</span><span class="sxs-lookup"><span data-stu-id="690a7-113">The following interface exposes the `IDateTime` service:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

<span data-ttu-id="690a7-114">Následující kód implementuje `IDateTime` rozhraní:</span><span class="sxs-lookup"><span data-stu-id="690a7-114">The following code implements the `IDateTime` interface:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

<span data-ttu-id="690a7-115">Přidání služby do služby kontejneru:</span><span class="sxs-lookup"><span data-stu-id="690a7-115">Add the service to the service container:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

<span data-ttu-id="690a7-116">Další informace o <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, naleznete v tématu [životnosti služby DI](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="690a7-116">For more information on <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, see [DI service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="690a7-117">Následující kód zobrazí pozdrav uživateli na základě času dne:</span><span class="sxs-lookup"><span data-stu-id="690a7-117">The following code displays a greeting to the user based on the time of day:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="690a7-118">Spusťte aplikaci a zobrazí se zpráva, na základě času.</span><span class="sxs-lookup"><span data-stu-id="690a7-118">Run the app and a message is displayed based on the time.</span></span>

## <a name="action-injection-with-fromservices"></a><span data-ttu-id="690a7-119">Vkládání akce s FromServices</span><span class="sxs-lookup"><span data-stu-id="690a7-119">Action injection with FromServices</span></span>

<span data-ttu-id="690a7-120"><xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> Umožňuje přímo do metody akce bez použití konstruktoru vkládání vkládá služba:</span><span class="sxs-lookup"><span data-stu-id="690a7-120">The <xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> enables injecting a service directly into an action method without using constructor injection:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a><span data-ttu-id="690a7-121">Nastavení přístupu z kontroleru</span><span class="sxs-lookup"><span data-stu-id="690a7-121">Access settings from a controller</span></span>

<span data-ttu-id="690a7-122">Přístup k aplikaci nebo konfigurace nastavení z v rámci kontroleru je běžný vzor.</span><span class="sxs-lookup"><span data-stu-id="690a7-122">Accessing app or configuration settings from within a controller is a common pattern.</span></span> <span data-ttu-id="690a7-123">*Možnosti vzor* podle <xref:fundamentals/configuration/options> je oblíbený přístup ke správě nastavení.</span><span class="sxs-lookup"><span data-stu-id="690a7-123">The *options pattern* described in <xref:fundamentals/configuration/options> is the preferred approach to manage settings.</span></span> <span data-ttu-id="690a7-124">Obecně platí, není vložit přímo <xref:Microsoft.Extensions.Configuration.IConfiguration> do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="690a7-124">Generally, don't directly inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into a controller.</span></span>

<span data-ttu-id="690a7-125">Vytvořte třídu, která představuje možnosti.</span><span class="sxs-lookup"><span data-stu-id="690a7-125">Create a class that represents the options.</span></span> <span data-ttu-id="690a7-126">Příklad:</span><span class="sxs-lookup"><span data-stu-id="690a7-126">For example:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

<span data-ttu-id="690a7-127">Přidáte třídu konfigurace ke kolekci služby:</span><span class="sxs-lookup"><span data-stu-id="690a7-127">Add the configuration class to the services collection:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

<span data-ttu-id="690a7-128">Konfigurace aplikace pro čtení nastavení ze souboru ve formátu JSON:</span><span class="sxs-lookup"><span data-stu-id="690a7-128">Configure the app to read the settings from a JSON-formatted file:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

<span data-ttu-id="690a7-129">Následující kód požadavky `IOptions<SampleWebSettings>` nastavení z kontejneru služeb a použije je `Index` – metoda:</span><span class="sxs-lookup"><span data-stu-id="690a7-129">The following code requests the `IOptions<SampleWebSettings>` settings from the service container and uses them in the `Index` method:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="690a7-130">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="690a7-130">Additional resources</span></span>

* <span data-ttu-id="690a7-131">Zobrazit <xref:mvc/controllers/testing> se naučíte usnadňují otestovat explicitní vyžádání závislostí do kontrolerů kódu.</span><span class="sxs-lookup"><span data-stu-id="690a7-131">See <xref:mvc/controllers/testing> to learn how to make code easier to test by explicitly requesting dependencies in controllers.</span></span>

* <span data-ttu-id="690a7-132">[Nahraďte výchozí kontejner vkládání závislostí třetích stran implementace](xref:fundamentals/dependency-injection#default-service-container-replacement).</span><span class="sxs-lookup"><span data-stu-id="690a7-132">[Replace the default dependency injection container with a third party implementation](xref:fundamentals/dependency-injection#default-service-container-replacement).</span></span>
