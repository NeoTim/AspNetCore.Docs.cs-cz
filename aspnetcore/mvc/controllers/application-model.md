---
title: Práce s modelem aplikace v ASP.NET Core
author: ardalis
description: Zjistěte, jak číst a manipulace s modelem aplikace k úpravě chování prvky MVC v ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: mvc/controllers/application-model
ms.openlocfilehash: 6b0591a877c0d82e0ee6ab002eb6a6650753677b
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58208593"
---
# <a name="work-with-the-application-model-in-aspnet-core"></a><span data-ttu-id="58983-103">Práce s modelem aplikace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="58983-103">Work with the application model in ASP.NET Core</span></span>

<span data-ttu-id="58983-104">Podle [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="58983-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="58983-105">ASP.NET Core MVC definuje *aplikační model* představující součásti aplikace MVC.</span><span class="sxs-lookup"><span data-stu-id="58983-105">ASP.NET Core MVC defines an *application model* representing the components of an MVC app.</span></span> <span data-ttu-id="58983-106">Může číst a manipulaci s Tento model k úpravě chování prvky MVC.</span><span class="sxs-lookup"><span data-stu-id="58983-106">You can read and manipulate this model to modify how MVC elements behave.</span></span> <span data-ttu-id="58983-107">Ve výchozím nastavení MVC dodržovat určité konvence určíte, které třídy jsou považovány za řadičích, které metody na tyto třídy jsou akce a chování parametry a směrování.</span><span class="sxs-lookup"><span data-stu-id="58983-107">By default, MVC follows certain conventions to determine which classes are considered to be controllers, which methods on those classes are actions, and how parameters and routing behave.</span></span> <span data-ttu-id="58983-108">Toto chování tak, aby odpovídaly potřebám vaší aplikace tak, že vytvoření vlastní zásady odvíjející a jejich použití globálně, nebo jako atributy můžete přizpůsobit.</span><span class="sxs-lookup"><span data-stu-id="58983-108">You can customize this behavior to suit your app's needs by creating your own conventions and applying them globally or as attributes.</span></span>

## <a name="models-and-providers"></a><span data-ttu-id="58983-109">Modely a poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="58983-109">Models and Providers</span></span>

<span data-ttu-id="58983-110">Model aplikace ASP.NET Core MVC obsahovat abstraktní rozhraní a třídy konkrétní implementace, které popisují aplikaci MVC.</span><span class="sxs-lookup"><span data-stu-id="58983-110">The ASP.NET Core MVC application model include both abstract interfaces and concrete implementation classes that describe an MVC application.</span></span> <span data-ttu-id="58983-111">Tento model je výsledkem zjišťování kontrolerů, akce, parametry akce, trasy a filtry podle výchozích konvencí aplikace MVC.</span><span class="sxs-lookup"><span data-stu-id="58983-111">This model is the result of MVC discovering the app's controllers, actions, action parameters, routes, and filters according to default conventions.</span></span> <span data-ttu-id="58983-112">Při práci s modelem aplikace, můžete upravit svou aplikaci do různých odpovídají konvencím výchozí chování MVC.</span><span class="sxs-lookup"><span data-stu-id="58983-112">By working with the application model, you can modify your app to follow different conventions from the default MVC behavior.</span></span> <span data-ttu-id="58983-113">Parametry, názvy, cesty a filtry se používají jako konfigurační data pro akce a kontrolery.</span><span class="sxs-lookup"><span data-stu-id="58983-113">The parameters, names, routes, and filters are all used as configuration data for actions and controllers.</span></span>

<span data-ttu-id="58983-114">Model aplikace ASP.NET Core MVC zahrnuje následující strukturu:</span><span class="sxs-lookup"><span data-stu-id="58983-114">The ASP.NET Core MVC Application Model has the following structure:</span></span>

* <span data-ttu-id="58983-115">ApplicationModel</span><span class="sxs-lookup"><span data-stu-id="58983-115">ApplicationModel</span></span>
  * <span data-ttu-id="58983-116">Kontrolery (ControllerModel)</span><span class="sxs-lookup"><span data-stu-id="58983-116">Controllers (ControllerModel)</span></span>
    * <span data-ttu-id="58983-117">Akce (ActionModel)</span><span class="sxs-lookup"><span data-stu-id="58983-117">Actions (ActionModel)</span></span>
      * <span data-ttu-id="58983-118">Parametry (ParameterModel)</span><span class="sxs-lookup"><span data-stu-id="58983-118">Parameters (ParameterModel)</span></span>

<span data-ttu-id="58983-119">Každou úroveň modelu má přístup do společného `Properties` kolekce a nižších úrovních můžete používat a přepsat hodnoty vlastností nastavené ve vyšší úrovně v hierarchii.</span><span class="sxs-lookup"><span data-stu-id="58983-119">Each level of the model has access to a common `Properties` collection, and lower levels can access and overwrite property values set by higher levels in the hierarchy.</span></span> <span data-ttu-id="58983-120">Vlastnosti jsou zachované `ActionDescriptor.Properties` při vytváření akce.</span><span class="sxs-lookup"><span data-stu-id="58983-120">The properties are persisted to the `ActionDescriptor.Properties` when the actions are created.</span></span> <span data-ttu-id="58983-121">Potom při zpracování požadavku, všechny vlastnosti konvence přidá nebo upraví přístupné prostřednictvím `ActionContext.ActionDescriptor.Properties`.</span><span class="sxs-lookup"><span data-stu-id="58983-121">Then when a request is being handled, any properties a convention added or modified can be accessed through `ActionContext.ActionDescriptor.Properties`.</span></span> <span data-ttu-id="58983-122">Pomocí vlastností je skvělý způsob, jak nakonfigurovat na základě akcích filtry, vazače modelů a podobně.</span><span class="sxs-lookup"><span data-stu-id="58983-122">Using properties is a great way to configure your filters, model binders, etc. on a per-action basis.</span></span>

> [!NOTE]
> <span data-ttu-id="58983-123">`ActionDescriptor.Properties` Kolekce není bezpečná (pro zápis) po dokončení spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="58983-123">The `ActionDescriptor.Properties` collection isn't thread safe (for writes) once app startup has finished.</span></span> <span data-ttu-id="58983-124">Konvence jsou nejlepší způsob, jak bezpečně přidat data do této kolekce.</span><span class="sxs-lookup"><span data-stu-id="58983-124">Conventions are the best way to safely add data to this collection.</span></span>

### <a name="iapplicationmodelprovider"></a><span data-ttu-id="58983-125">IApplicationModelProvider</span><span class="sxs-lookup"><span data-stu-id="58983-125">IApplicationModelProvider</span></span>

<span data-ttu-id="58983-126">ASP.NET Core MVC načte aplikaci modelu s použitím vzoru zprostředkovatele určené [IApplicationModelProvider](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelprovider) rozhraní.</span><span class="sxs-lookup"><span data-stu-id="58983-126">ASP.NET Core MVC loads the application model using a provider pattern, defined by the [IApplicationModelProvider](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelprovider) interface.</span></span> <span data-ttu-id="58983-127">Tato část popisuje některé podrobnosti interní implementace toho, jak tato funkce poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="58983-127">This section covers some of the internal implementation details of how this provider functions.</span></span> <span data-ttu-id="58983-128">Toto je rozšířená – většina aplikací, které využívají model aplikace by to tak, že práce s konvencí.</span><span class="sxs-lookup"><span data-stu-id="58983-128">This is an advanced topic - most apps that leverage the application model should do so by working with conventions.</span></span>

<span data-ttu-id="58983-129">Implementace `IApplicationModelProvider` rozhraní "zabalení" mezi sebou, s každé volání implementace `OnProvidersExecuting` ve vzestupném pořadí na základě jeho `Order` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="58983-129">Implementations of the `IApplicationModelProvider` interface "wrap" one another, with each implementation calling `OnProvidersExecuting` in ascending order based on its `Order` property.</span></span> <span data-ttu-id="58983-130">`OnProvidersExecuted` Metoda je volána poté v obráceném pořadí.</span><span class="sxs-lookup"><span data-stu-id="58983-130">The `OnProvidersExecuted` method is then called in reverse order.</span></span> <span data-ttu-id="58983-131">Rozhraní definuje několik poskytovatelů:</span><span class="sxs-lookup"><span data-stu-id="58983-131">The framework defines several providers:</span></span>

<span data-ttu-id="58983-132">První (`Order=-1000`):</span><span class="sxs-lookup"><span data-stu-id="58983-132">First (`Order=-1000`):</span></span>

* [`DefaultApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.internal.defaultapplicationmodelprovider)

<span data-ttu-id="58983-133">Potom (`Order=-990`):</span><span class="sxs-lookup"><span data-stu-id="58983-133">Then (`Order=-990`):</span></span>

* [`AuthorizationApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.internal.authorizationapplicationmodelprovider)
* [`CorsApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.cors.internal.corsapplicationmodelprovider)

> [!NOTE]
> <span data-ttu-id="58983-134">Pořadí, ve které dva poskytovatelé se stejnou hodnotou pro `Order` se nazývají není definována a proto byste se neměli spoléhat.</span><span class="sxs-lookup"><span data-stu-id="58983-134">The order in which two providers with the same value for `Order` are called is undefined, and therefore shouldn't be relied upon.</span></span>

> [!NOTE]
> <span data-ttu-id="58983-135">`IApplicationModelProvider` je rozšířené koncept pro autory framework rozšířit.</span><span class="sxs-lookup"><span data-stu-id="58983-135">`IApplicationModelProvider` is an advanced concept for framework authors to extend.</span></span> <span data-ttu-id="58983-136">Obecně platí aplikace by měly používat konvence a rozhraní by měl používat poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="58983-136">In general, apps should use conventions and frameworks should use providers.</span></span> <span data-ttu-id="58983-137">Klíče rozdíl je, že poskytovatelů vždy spuštěn konvence.</span><span class="sxs-lookup"><span data-stu-id="58983-137">The key distinction is that providers always run before conventions.</span></span>

<span data-ttu-id="58983-138">`DefaultApplicationModelProvider` Zavádí řadu výchozí chování používat ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="58983-138">The `DefaultApplicationModelProvider` establishes many of the default behaviors used by ASP.NET Core MVC.</span></span> <span data-ttu-id="58983-139">Mezi jeho povinnosti patří:</span><span class="sxs-lookup"><span data-stu-id="58983-139">Its responsibilities include:</span></span>

* <span data-ttu-id="58983-140">Přidání globální filtry do kontextu</span><span class="sxs-lookup"><span data-stu-id="58983-140">Adding global filters to the context</span></span>
* <span data-ttu-id="58983-141">Přidání řadiče do kontextu</span><span class="sxs-lookup"><span data-stu-id="58983-141">Adding controllers to the context</span></span>
* <span data-ttu-id="58983-142">Přidání kontroleru veřejné metody jako akce</span><span class="sxs-lookup"><span data-stu-id="58983-142">Adding public controller methods as actions</span></span>
* <span data-ttu-id="58983-143">Přidání parametrů metody akce v kontextu</span><span class="sxs-lookup"><span data-stu-id="58983-143">Adding action method parameters to the context</span></span>
* <span data-ttu-id="58983-144">Použití postupu a dalších atributů</span><span class="sxs-lookup"><span data-stu-id="58983-144">Applying route and other attributes</span></span>

<span data-ttu-id="58983-145">Některé vestavěným chováním jsou implementované `DefaultApplicationModelProvider`.</span><span class="sxs-lookup"><span data-stu-id="58983-145">Some built-in behaviors are implemented by the `DefaultApplicationModelProvider`.</span></span> <span data-ttu-id="58983-146">Tento zprostředkovatel je zodpovědný za vytváření [ `ControllerModel` ](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.controllermodel), které zase odkazuje [ `ActionModel` ](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.actionmodel#Microsoft_AspNetCore_Mvc_ApplicationModels_ActionModel), [ `PropertyModel` ](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.propertymodel), a [ `ParameterModel` ](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.parametermodel#Microsoft_AspNetCore_Mvc_ApplicationModels_ParameterModel) instancí.</span><span class="sxs-lookup"><span data-stu-id="58983-146">This provider is responsible for constructing the [`ControllerModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.controllermodel), which in turn references [`ActionModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.actionmodel#Microsoft_AspNetCore_Mvc_ApplicationModels_ActionModel), [`PropertyModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.propertymodel), and [`ParameterModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.parametermodel#Microsoft_AspNetCore_Mvc_ApplicationModels_ParameterModel) instances.</span></span> <span data-ttu-id="58983-147">`DefaultApplicationModelProvider` Třída je podrobnosti implementace interní rámec, můžete a bude v budoucnu změnit.</span><span class="sxs-lookup"><span data-stu-id="58983-147">The `DefaultApplicationModelProvider` class is an internal framework implementation detail that can and will change in the future.</span></span> 

<span data-ttu-id="58983-148">`AuthorizationApplicationModelProvider` Zodpovídá za použití chování asociovaných s `AuthorizeFilter` a `AllowAnonymousFilter` atributy.</span><span class="sxs-lookup"><span data-stu-id="58983-148">The `AuthorizationApplicationModelProvider` is responsible for applying the behavior associated with the `AuthorizeFilter` and `AllowAnonymousFilter` attributes.</span></span> <span data-ttu-id="58983-149">[Další informace o těchto atributů](xref:security/authorization/simple).</span><span class="sxs-lookup"><span data-stu-id="58983-149">[Learn more about these attributes](xref:security/authorization/simple).</span></span>

<span data-ttu-id="58983-150">`CorsApplicationModelProvider` Implementuje chování asociovaných s `IEnableCorsAttribute` a `IDisableCorsAttribute`a `DisableCorsAuthorizationFilter`.</span><span class="sxs-lookup"><span data-stu-id="58983-150">The `CorsApplicationModelProvider` implements behavior associated with the `IEnableCorsAttribute` and `IDisableCorsAttribute`, and the `DisableCorsAuthorizationFilter`.</span></span> <span data-ttu-id="58983-151">[Další informace o CORS](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="58983-151">[Learn more about CORS](xref:security/cors).</span></span>

## <a name="conventions"></a><span data-ttu-id="58983-152">Konvence</span><span class="sxs-lookup"><span data-stu-id="58983-152">Conventions</span></span>

<span data-ttu-id="58983-153">Aplikační model definuje abstrakce konvence, které poskytují jednodušší způsob, jak přizpůsobit chování modely než přepisování celého modelu nebo zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="58983-153">The application model defines convention abstractions that provide a simpler way to customize the behavior of the models than overriding the entire model or provider.</span></span> <span data-ttu-id="58983-154">Tato abstrakce jsou doporučeným způsobem, jak změnit chování vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="58983-154">These abstractions are the recommended way to modify your app's behavior.</span></span> <span data-ttu-id="58983-155">Konvence poskytují způsob, jak můžete napsat kód, který se bude dynamicky použít vlastní nastavení.</span><span class="sxs-lookup"><span data-stu-id="58983-155">Conventions provide a way for you to write code that will dynamically apply customizations.</span></span> <span data-ttu-id="58983-156">Zatímco [filtry](xref:mvc/controllers/filters) umožňují měnit chování rozhraní framework, přizpůsobení umožňují řídit, jak společně drátové celé aplikace.</span><span class="sxs-lookup"><span data-stu-id="58983-156">While [filters](xref:mvc/controllers/filters) provide a means of modifying the framework's behavior, customizations let you control how the whole app is wired together.</span></span>

<span data-ttu-id="58983-157">K dispozici jsou následující konvence:</span><span class="sxs-lookup"><span data-stu-id="58983-157">The following conventions are available:</span></span>

* [`IApplicationModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelconvention)
* [`IControllerModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.icontrollermodelconvention)
* [`IActionModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iactionmodelconvention)
* [`IParameterModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iparametermodelconvention)

<span data-ttu-id="58983-158">Konvence aplikují jejich přidáním do možnosti MVC nebo implementací `Attribute`s a jejich použití kontrolerů, akce nebo parametry akce (podobně jako [ `Filters` ](xref:mvc/controllers/filters)).</span><span class="sxs-lookup"><span data-stu-id="58983-158">Conventions are applied by adding them to MVC options or by implementing `Attribute`s and applying them to controllers, actions, or action parameters (similar to [`Filters`](xref:mvc/controllers/filters)).</span></span> <span data-ttu-id="58983-159">Na rozdíl od filtry provádějí pouze konvence při spuštění aplikace, nikoli jako součást každého požadavku.</span><span class="sxs-lookup"><span data-stu-id="58983-159">Unlike filters, conventions are only executed when the app is starting, not as part of each request.</span></span>

### <a name="sample-modifying-the-applicationmodel"></a><span data-ttu-id="58983-160">Ukázka: Úpravy ApplicationModel</span><span class="sxs-lookup"><span data-stu-id="58983-160">Sample: Modifying the ApplicationModel</span></span>

<span data-ttu-id="58983-161">Tato konvence slouží k přidání vlastnosti do aplikačního modelu.</span><span class="sxs-lookup"><span data-stu-id="58983-161">The following convention is used to add a property to the application model.</span></span> 

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ApplicationDescription.cs)]

<span data-ttu-id="58983-162">Vytváření modelu aplikací se použijí jako možnosti při přidání MVC v `ConfigureServices` v `Startup`.</span><span class="sxs-lookup"><span data-stu-id="58983-162">Application model conventions are applied as options when MVC is added in `ConfigureServices` in `Startup`.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=5)]

<span data-ttu-id="58983-163">Vlastnosti jsou přístupné `ActionDescriptor` kolekci vlastností v rámci akce kontroleru:</span><span class="sxs-lookup"><span data-stu-id="58983-163">Properties are accessible from the `ActionDescriptor` properties collection within controller actions:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/AppModelController.cs?name=AppModelController)]

### <a name="sample-modifying-the-controllermodel-description"></a><span data-ttu-id="58983-164">Ukázka: Úprava popisu ControllerModel</span><span class="sxs-lookup"><span data-stu-id="58983-164">Sample: Modifying the ControllerModel Description</span></span>

<span data-ttu-id="58983-165">Stejně jako v předchozím příkladu je model řadič také upravit zahrnout vlastní vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="58983-165">As in the previous example, the controller model can also be modified to include custom properties.</span></span> <span data-ttu-id="58983-166">Toto přepíše existující vlastnosti se stejným názvem zadáno v modelu aplikací.</span><span class="sxs-lookup"><span data-stu-id="58983-166">These will override existing properties with the same name specified in the application model.</span></span> <span data-ttu-id="58983-167">Následující atribut konvence přidá popis na úrovni kontroleru:</span><span class="sxs-lookup"><span data-stu-id="58983-167">The following convention attribute adds a description at the controller level:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ControllerDescriptionAttribute.cs)]

<span data-ttu-id="58983-168">Tato konvence je použitý jako atribut na kontroleru.</span><span class="sxs-lookup"><span data-stu-id="58983-168">This convention is applied as an attribute on a controller.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=ControllerDescription&highlight=1)]

<span data-ttu-id="58983-169">Vlastnost "description" pracuje stejným způsobem jako v předchozích příkladech.</span><span class="sxs-lookup"><span data-stu-id="58983-169">The "description" property is accessed in the same manner as in previous examples.</span></span>

### <a name="sample-modifying-the-actionmodel-description"></a><span data-ttu-id="58983-170">Ukázka: Úprava popisu ActionModel</span><span class="sxs-lookup"><span data-stu-id="58983-170">Sample: Modifying the ActionModel Description</span></span>

<span data-ttu-id="58983-171">Konvence samostatný atribut lze použít pro jednotlivé akce přepsání nastavení už používá na úrovni aplikace nebo kontroleru.</span><span class="sxs-lookup"><span data-stu-id="58983-171">A separate attribute convention can be applied to individual actions, overriding behavior already applied at the application or controller level.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ActionDescriptionAttribute.cs)]

<span data-ttu-id="58983-172">Použití na akci v rámci kontroleru předchozí příklad ukazuje, jak přepíše konvence úrovni kontroleru:</span><span class="sxs-lookup"><span data-stu-id="58983-172">Applying this to an action within the previous example's controller demonstrates how it overrides the controller-level convention:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=DescriptionAttributesController&highlight=9)]

### <a name="sample-modifying-the-parametermodel"></a><span data-ttu-id="58983-173">Ukázka: Úpravy ParameterModel</span><span class="sxs-lookup"><span data-stu-id="58983-173">Sample: Modifying the ParameterModel</span></span>

<span data-ttu-id="58983-174">Tato konvence můžete použít pro parametry akce k úpravě svých `BindingInfo`.</span><span class="sxs-lookup"><span data-stu-id="58983-174">The following convention can be applied to action parameters to modify their `BindingInfo`.</span></span> <span data-ttu-id="58983-175">Tato konvence vyžaduje, aby parametr trasa parameter; Další možné zdroje vazby (například hodnoty řetězce dotazu) jsou ignorovány.</span><span class="sxs-lookup"><span data-stu-id="58983-175">The following convention requires that the parameter be a route parameter; other potential binding sources (such as query string values) are ignored.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/MustBeInRouteParameterModelConvention.cs)]

<span data-ttu-id="58983-176">Atribut může být použitý u parametru všechny akce:</span><span class="sxs-lookup"><span data-stu-id="58983-176">The attribute may be applied to any action parameter:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/ParameterModelController.cs?name=ParameterModelController&highlight=5)]

### <a name="sample-modifying-the-actionmodel-name"></a><span data-ttu-id="58983-177">Ukázka: Změna názvu ActionModel</span><span class="sxs-lookup"><span data-stu-id="58983-177">Sample: Modifying the ActionModel Name</span></span>

<span data-ttu-id="58983-178">Upraví následující konvence `ActionModel` aktualizovat *název* akce, na který se použije.</span><span class="sxs-lookup"><span data-stu-id="58983-178">The following convention modifies the `ActionModel` to update the *name* of the action to which it's applied.</span></span> <span data-ttu-id="58983-179">Nový název je zadat jako parametr atributu.</span><span class="sxs-lookup"><span data-stu-id="58983-179">The new name is provided as a parameter to the attribute.</span></span> <span data-ttu-id="58983-180">Tento nový název se používá ve směrování, takže bude mít vliv na trasy použité k dosažení této metodě akce.</span><span class="sxs-lookup"><span data-stu-id="58983-180">This new name is used by routing, so it will affect the route used to reach this action method.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/CustomActionNameAttribute.cs)]

<span data-ttu-id="58983-181">Tento atribut se používá pro metodu akce v `HomeController`:</span><span class="sxs-lookup"><span data-stu-id="58983-181">This attribute is applied to an action method in the `HomeController`:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/HomeController.cs?name=ActionModelConvention&highlight=2)]

<span data-ttu-id="58983-182">I když je název metody `SomeName`, atribut přepíše konvenci MVC pomocí názvu metody a nahradí název akce s `MyCoolAction`.</span><span class="sxs-lookup"><span data-stu-id="58983-182">Even though the method name is `SomeName`, the attribute overrides the MVC convention of using the method name and replaces the action name with `MyCoolAction`.</span></span> <span data-ttu-id="58983-183">Díky tomu se trasy použité k dosažení této akce je `/Home/MyCoolAction`.</span><span class="sxs-lookup"><span data-stu-id="58983-183">Thus, the route used to reach this action is `/Home/MyCoolAction`.</span></span>

> [!NOTE]
> <span data-ttu-id="58983-184">V tomto příkladu je v podstatě totéž jako použití integrovaného [ActionName](/dotnet/api/microsoft.aspnetcore.mvc.actionnameattribute) atribut.</span><span class="sxs-lookup"><span data-stu-id="58983-184">This example is essentially the same as using the built-in [ActionName](/dotnet/api/microsoft.aspnetcore.mvc.actionnameattribute) attribute.</span></span>

### <a name="sample-custom-routing-convention"></a><span data-ttu-id="58983-185">Ukázka: Vlastní konvenci směrování</span><span class="sxs-lookup"><span data-stu-id="58983-185">Sample: Custom Routing Convention</span></span>

<span data-ttu-id="58983-186">Můžete použít `IApplicationModelConvention` přizpůsobit způsob směrování funguje.</span><span class="sxs-lookup"><span data-stu-id="58983-186">You can use an `IApplicationModelConvention` to customize how routing works.</span></span> <span data-ttu-id="58983-187">Například následující konvence bude obsahovat řadiče obory názvů do jejich trasy nahrazení `.` v oboru názvů s `/` v postupu:</span><span class="sxs-lookup"><span data-stu-id="58983-187">For example, the following convention will incorporate Controllers' namespaces into their routes, replacing `.` in the namespace with `/` in the route:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/NamespaceRoutingConvention.cs)]

<span data-ttu-id="58983-188">Tato konvence je přidán jako možnost v při spuštění.</span><span class="sxs-lookup"><span data-stu-id="58983-188">The convention is added as an option in Startup.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=6)]

> [!TIP]
> <span data-ttu-id="58983-189">Můžete přidat konvence mají vaše [middleware](xref:fundamentals/middleware/index) díky přístupu do `MvcOptions` pomocí `services.Configure<MvcOptions>(c => c.Conventions.Add(YOURCONVENTION));`</span><span class="sxs-lookup"><span data-stu-id="58983-189">You can add conventions to your [middleware](xref:fundamentals/middleware/index) by accessing `MvcOptions` using `services.Configure<MvcOptions>(c => c.Conventions.Add(YOURCONVENTION));`</span></span>

<span data-ttu-id="58983-190">Tento příklad se týká tato konvence trasy, které nepoužívají atribut směrování, kdy má "Namespace" v názvu kontroleru.</span><span class="sxs-lookup"><span data-stu-id="58983-190">This sample applies this convention to routes that are not using attribute routing where the controller has  "Namespace" in its name.</span></span> <span data-ttu-id="58983-191">Následující kontroler ukazuje tato konvence:</span><span class="sxs-lookup"><span data-stu-id="58983-191">The following controller demonstrates this convention:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/NamespaceRoutingController.cs?highlight=7-8)]

## <a name="application-model-usage-in-webapicompatshim"></a><span data-ttu-id="58983-192">Používání modelu aplikací v WebApiCompatShim</span><span class="sxs-lookup"><span data-stu-id="58983-192">Application Model Usage in WebApiCompatShim</span></span>

<span data-ttu-id="58983-193">ASP.NET Core MVC používá jinou sadu konvence z ASP.NET Web API 2.</span><span class="sxs-lookup"><span data-stu-id="58983-193">ASP.NET Core MVC uses a different set of conventions from ASP.NET Web API 2.</span></span> <span data-ttu-id="58983-194">Pomocí vlastních konvencí, můžete upravit aplikaci ASP.NET Core MVC chování, aby byla konzistentní se u aplikace webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="58983-194">Using custom conventions, you can modify an ASP.NET Core MVC app's behavior to be consistent with that of a Web API app.</span></span> <span data-ttu-id="58983-195">Microsoft se dodává [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim/) speciálně pro tento účel.</span><span class="sxs-lookup"><span data-stu-id="58983-195">Microsoft ships the [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim/) specifically for this purpose.</span></span>

> [!NOTE]
> <span data-ttu-id="58983-196">Další informace o [migrace z rozhraní ASP.NET Web API](xref:migration/webapi).</span><span class="sxs-lookup"><span data-stu-id="58983-196">Learn more about [migration from ASP.NET Web API](xref:migration/webapi).</span></span>

<span data-ttu-id="58983-197">Chcete-li použít překrytí kompatibility webové rozhraní API, budete muset přidat do projektu balíček a pak přidejte konvencí do MVC voláním `AddWebApiConventions` v `Startup`:</span><span class="sxs-lookup"><span data-stu-id="58983-197">To use the Web API Compatibility Shim, you need to add the package to your project and then add the conventions to MVC by calling `AddWebApiConventions` in `Startup`:</span></span>

```csharp
services.AddMvc().AddWebApiConventions();
```

<span data-ttu-id="58983-198">Konvence poskytované překrytí se použijí jenom k různým částem aplikace, které jste využili určité atributy použité na ně.</span><span class="sxs-lookup"><span data-stu-id="58983-198">The conventions provided by the shim are only applied to parts of the app that have had certain attributes applied to them.</span></span> <span data-ttu-id="58983-199">Následující čtyři atributy se používají pro ovládací prvek, který by měl jejich konvence Autor změny překrytí konvence mají řadiče:</span><span class="sxs-lookup"><span data-stu-id="58983-199">The following four attributes are used to control which controllers should have their conventions modified by the shim's conventions:</span></span>

* [<span data-ttu-id="58983-200">UseWebApiActionConventionsAttribute</span><span class="sxs-lookup"><span data-stu-id="58983-200">UseWebApiActionConventionsAttribute</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiactionconventionsattribute)
* [<span data-ttu-id="58983-201">UseWebApiOverloadingAttribute</span><span class="sxs-lookup"><span data-stu-id="58983-201">UseWebApiOverloadingAttribute</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapioverloadingattribute)
* [<span data-ttu-id="58983-202">UseWebApiParameterConventionsAttribute</span><span class="sxs-lookup"><span data-stu-id="58983-202">UseWebApiParameterConventionsAttribute</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiparameterconventionsattribute)
* [<span data-ttu-id="58983-203">UseWebApiRoutesAttribute</span><span class="sxs-lookup"><span data-stu-id="58983-203">UseWebApiRoutesAttribute</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiroutesattribute)

### <a name="action-conventions"></a><span data-ttu-id="58983-204">Akce konvence</span><span class="sxs-lookup"><span data-stu-id="58983-204">Action Conventions</span></span>

<span data-ttu-id="58983-205">`UseWebApiActionConventionsAttribute` Se používá k mapování metody HTTP akce na základě jejich názvu (například `Get` by mapování na `HttpGet`).</span><span class="sxs-lookup"><span data-stu-id="58983-205">The `UseWebApiActionConventionsAttribute` is used to map the HTTP method to actions based on their name (for instance, `Get` would map to `HttpGet`).</span></span> <span data-ttu-id="58983-206">Platí jenom pro akce, které nepoužívají směrováním atributů.</span><span class="sxs-lookup"><span data-stu-id="58983-206">It only applies to actions that don't use attribute routing.</span></span>

### <a name="overloading"></a><span data-ttu-id="58983-207">Přetížení</span><span class="sxs-lookup"><span data-stu-id="58983-207">Overloading</span></span>

<span data-ttu-id="58983-208">`UseWebApiOverloadingAttribute` Se používá k aplikování `WebApiOverloadingApplicationModelConvention` konvence.</span><span class="sxs-lookup"><span data-stu-id="58983-208">The `UseWebApiOverloadingAttribute` is used to apply the `WebApiOverloadingApplicationModelConvention` convention.</span></span> <span data-ttu-id="58983-209">Tato konvence přidá `OverloadActionConstraint` akce výběru procesu, což omezuje Release candidate akce pro ty, pro které požadavek splňuje všechny povinné parametry.</span><span class="sxs-lookup"><span data-stu-id="58983-209">This convention adds an `OverloadActionConstraint` to the action selection process, which limits candidate actions to those for which the request satisfies all non-optional parameters.</span></span>

### <a name="parameter-conventions"></a><span data-ttu-id="58983-210">Parametr konvence</span><span class="sxs-lookup"><span data-stu-id="58983-210">Parameter Conventions</span></span>

<span data-ttu-id="58983-211">`UseWebApiParameterConventionsAttribute` Se používá k aplikování `WebApiParameterConventionsApplicationModelConvention` konvence akce.</span><span class="sxs-lookup"><span data-stu-id="58983-211">The `UseWebApiParameterConventionsAttribute` is used to apply the `WebApiParameterConventionsApplicationModelConvention` action convention.</span></span> <span data-ttu-id="58983-212">Tato konvence Určuje, že jednoduché typy použité jako parametry akce jsou vázány z identifikátoru URI ve výchozím nastavení, zatímco komplexní typy, které jsou vázány z textu požadavku.</span><span class="sxs-lookup"><span data-stu-id="58983-212">This convention specifies that simple types used as action parameters are bound from the URI by default, while complex types are bound from the request body.</span></span>

### <a name="routes"></a><span data-ttu-id="58983-213">Trasy</span><span class="sxs-lookup"><span data-stu-id="58983-213">Routes</span></span>

<span data-ttu-id="58983-214">`UseWebApiRoutesAttribute` Ovládací prvky, zda `WebApiApplicationModelConvention` použity konvence kontroleru.</span><span class="sxs-lookup"><span data-stu-id="58983-214">The `UseWebApiRoutesAttribute` controls whether the `WebApiApplicationModelConvention` controller convention is applied.</span></span> <span data-ttu-id="58983-215">Pokud povolena, tato konvence slouží k přidání podpory pro [oblasti](xref:mvc/controllers/areas) trasy.</span><span class="sxs-lookup"><span data-stu-id="58983-215">When enabled, this convention is used to add support for [areas](xref:mvc/controllers/areas) to the route.</span></span>

<span data-ttu-id="58983-216">Kromě sadu pravidel, obsahuje balíček kompatibility `System.Web.Http.ApiController` základní třída, která nahradí poskytuje webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="58983-216">In addition to a set of conventions, the compatibility package includes a `System.Web.Http.ApiController` base class that replaces the one provided by Web API.</span></span> <span data-ttu-id="58983-217">To umožňuje kontrolerům napsané pro webové rozhraní API a dědí z jeho `ApiController` fungovat jako byly navrženy, při spuštění na rozhraní ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="58983-217">This allows your controllers written for Web API and inheriting from its `ApiController` to work as they were designed, while running on ASP.NET Core MVC.</span></span> <span data-ttu-id="58983-218">Tato třída base kontroleru je upravený se všemi `UseWebApi*` atributy uvedené výše.</span><span class="sxs-lookup"><span data-stu-id="58983-218">This base controller class is decorated with all of the `UseWebApi*` attributes listed above.</span></span> <span data-ttu-id="58983-219">`ApiController` Zveřejňuje vlastnosti, metody a typy výsledků, které jsou kompatibilní s výstrahám nacházejícím se v rozhraní Web API.</span><span class="sxs-lookup"><span data-stu-id="58983-219">The `ApiController` exposes properties, methods, and result types that are compatible with those found in Web API.</span></span>

## <a name="using-apiexplorer-to-document-your-app"></a><span data-ttu-id="58983-220">Pomocí ApiExplorer do dokumentu aplikace</span><span class="sxs-lookup"><span data-stu-id="58983-220">Using ApiExplorer to Document Your App</span></span>

<span data-ttu-id="58983-221">Zpřístupňuje aplikačního modelu [ `ApiExplorer` ](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.apiexplorermodel) vlastnosti na každé úrovni, který lze použít k procházení struktury aplikace.</span><span class="sxs-lookup"><span data-stu-id="58983-221">The application model exposes an [`ApiExplorer`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.apiexplorermodel) property at each level that can be used to traverse the app's structure.</span></span> <span data-ttu-id="58983-222">To je možné použít k [generování stránek nápovědy pro webová rozhraní API pomocí nástroje jako Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span><span class="sxs-lookup"><span data-stu-id="58983-222">This can be used to [generate help pages for your Web APIs using tools like Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span> <span data-ttu-id="58983-223">`ApiExplorer` Zpřístupňuje vlastnost `IsVisible` vlastnost, která lze použít k určení, které části modelu vaší aplikace by měly být zveřejněny.</span><span class="sxs-lookup"><span data-stu-id="58983-223">The `ApiExplorer` property exposes an `IsVisible` property that can be set to specify which parts of your app's model should be exposed.</span></span> <span data-ttu-id="58983-224">Můžete nakonfigurovat tato nastavení pomocí konvence:</span><span class="sxs-lookup"><span data-stu-id="58983-224">You can configure this setting using a convention:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/EnableApiExplorerApplicationConvention.cs)]

<span data-ttu-id="58983-225">Pomocí tohoto přístupu (a další vytváření názvů v případě potřeby), můžete povolit nebo zakázat viditelnost rozhraní API na libovolné úrovni v rámci vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="58983-225">Using this approach (and additional conventions if required), you can enable or disable API visibility at any level within your app.</span></span> 
