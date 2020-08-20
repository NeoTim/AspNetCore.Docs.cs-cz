---
title: Práce s modelem aplikace v ASP.NET Core
author: ardalis
description: Naučte se číst model aplikace a manipulovat s ním, abyste mohli změnit způsob, jakým se prvky MVC chovají v ASP.NET Core.
ms.author: riande
ms.date: 12/05/2019
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
uid: mvc/controllers/application-model
ms.openlocfilehash: f2e86a8607ddd00dc7f4bec36079660f0cd1eea3
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630286"
---
# <a name="work-with-the-application-model-in-aspnet-core"></a><span data-ttu-id="a1b30-103">Práce s modelem aplikace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a1b30-103">Work with the application model in ASP.NET Core</span></span>

<span data-ttu-id="a1b30-104">[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="a1b30-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="a1b30-105">ASP.NET Core MVC definuje *model aplikace* , který představuje komponenty aplikace MVC.</span><span class="sxs-lookup"><span data-stu-id="a1b30-105">ASP.NET Core MVC defines an *application model* representing the components of an MVC app.</span></span> <span data-ttu-id="a1b30-106">Tento model můžete číst a manipulovat s úpravami způsobu, jakým se chovají prvky MVC.</span><span class="sxs-lookup"><span data-stu-id="a1b30-106">You can read and manipulate this model to modify how MVC elements behave.</span></span> <span data-ttu-id="a1b30-107">Ve výchozím nastavení MVC používá určité konvence k určení, které třídy jsou považovány za řadiče, které metody na těchto třídách jsou akce a jak se chovají parametry a směrování.</span><span class="sxs-lookup"><span data-stu-id="a1b30-107">By default, MVC follows certain conventions to determine which classes are considered to be controllers, which methods on those classes are actions, and how parameters and routing behave.</span></span> <span data-ttu-id="a1b30-108">Toto chování můžete přizpůsobit tak, aby vyhovovalo potřebám vaší aplikace vytvořením vlastních konvencí a jejich použitím globálně nebo jako atributů.</span><span class="sxs-lookup"><span data-stu-id="a1b30-108">You can customize this behavior to suit your app's needs by creating your own conventions and applying them globally or as attributes.</span></span>

## <a name="models-and-providers"></a><span data-ttu-id="a1b30-109">Modely a zprostředkovatelé</span><span class="sxs-lookup"><span data-stu-id="a1b30-109">Models and Providers</span></span>

<span data-ttu-id="a1b30-110">Model aplikace ASP.NET Core MVC zahrnuje jak abstraktní rozhraní, tak konkrétní implementační třídy, které popisují aplikaci MVC.</span><span class="sxs-lookup"><span data-stu-id="a1b30-110">The ASP.NET Core MVC application model include both abstract interfaces and concrete implementation classes that describe an MVC application.</span></span> <span data-ttu-id="a1b30-111">Tento model je výsledkem, který MVC zjišťuje řadiče aplikace, akce, parametry akcí, trasy a filtry v závislosti na výchozích konvencích.</span><span class="sxs-lookup"><span data-stu-id="a1b30-111">This model is the result of MVC discovering the app's controllers, actions, action parameters, routes, and filters according to default conventions.</span></span> <span data-ttu-id="a1b30-112">Když pracujete s modelem aplikace, můžete upravit svou aplikaci tak, aby následovala různé konvence od výchozího chování MVC.</span><span class="sxs-lookup"><span data-stu-id="a1b30-112">By working with the application model, you can modify your app to follow different conventions from the default MVC behavior.</span></span> <span data-ttu-id="a1b30-113">Parametry, názvy, trasy a filtry se používají jako konfigurační data pro akce a řadiče.</span><span class="sxs-lookup"><span data-stu-id="a1b30-113">The parameters, names, routes, and filters are all used as configuration data for actions and controllers.</span></span>

<span data-ttu-id="a1b30-114">Model aplikace ASP.NET Core MVC má následující strukturu:</span><span class="sxs-lookup"><span data-stu-id="a1b30-114">The ASP.NET Core MVC Application Model has the following structure:</span></span>

* <span data-ttu-id="a1b30-115">ApplicationModel</span><span class="sxs-lookup"><span data-stu-id="a1b30-115">ApplicationModel</span></span>
  * <span data-ttu-id="a1b30-116">Řadiče (ControllerModel)</span><span class="sxs-lookup"><span data-stu-id="a1b30-116">Controllers (ControllerModel)</span></span>
    * <span data-ttu-id="a1b30-117">Akce (ActionModel)</span><span class="sxs-lookup"><span data-stu-id="a1b30-117">Actions (ActionModel)</span></span>
      * <span data-ttu-id="a1b30-118">Parametry (ParameterModel)</span><span class="sxs-lookup"><span data-stu-id="a1b30-118">Parameters (ParameterModel)</span></span>

<span data-ttu-id="a1b30-119">Každá úroveň modelu má přístup ke společné `Properties` kolekci a nižší úrovně mají přístup k hodnotám vlastností nastaveným pomocí vyšších úrovní v hierarchii a můžou na nich být přepsány.</span><span class="sxs-lookup"><span data-stu-id="a1b30-119">Each level of the model has access to a common `Properties` collection, and lower levels can access and overwrite property values set by higher levels in the hierarchy.</span></span> <span data-ttu-id="a1b30-120">Vlastnosti jsou uchovány `ActionDescriptor.Properties` při vytváření akcí.</span><span class="sxs-lookup"><span data-stu-id="a1b30-120">The properties are persisted to the `ActionDescriptor.Properties` when the actions are created.</span></span> <span data-ttu-id="a1b30-121">Při zpracování žádosti se pak ke všem vlastnostem, ke kterým je přidaný nebo upravená konvence, dostanete prostřednictvím `ActionContext.ActionDescriptor.Properties` .</span><span class="sxs-lookup"><span data-stu-id="a1b30-121">Then when a request is being handled, any properties a convention added or modified can be accessed through `ActionContext.ActionDescriptor.Properties`.</span></span> <span data-ttu-id="a1b30-122">Použití vlastností je skvělý způsob konfigurace filtrů, vazeb modelů atd. na základě jednotlivých akcí.</span><span class="sxs-lookup"><span data-stu-id="a1b30-122">Using properties is a great way to configure your filters, model binders, etc. on a per-action basis.</span></span>

> [!NOTE]
> <span data-ttu-id="a1b30-123">`ActionDescriptor.Properties`Kolekce není bezpečná pro přístup z více vláken (pro zápisy) po dokončení spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="a1b30-123">The `ActionDescriptor.Properties` collection isn't thread safe (for writes) once app startup has finished.</span></span> <span data-ttu-id="a1b30-124">Konvence představují nejlepší způsob, jak bezpečně přidat data do této kolekce.</span><span class="sxs-lookup"><span data-stu-id="a1b30-124">Conventions are the best way to safely add data to this collection.</span></span>

### <a name="iapplicationmodelprovider"></a><span data-ttu-id="a1b30-125">IApplicationModelProvider</span><span class="sxs-lookup"><span data-stu-id="a1b30-125">IApplicationModelProvider</span></span>

<span data-ttu-id="a1b30-126">ASP.NET Core MVC načte aplikační model pomocí vzoru poskytovatele definovaného rozhraním [IApplicationModelProvider](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelprovider) .</span><span class="sxs-lookup"><span data-stu-id="a1b30-126">ASP.NET Core MVC loads the application model using a provider pattern, defined by the [IApplicationModelProvider](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelprovider) interface.</span></span> <span data-ttu-id="a1b30-127">Tato část se zabývá některými interními podrobnými informacemi o tom, jak tento poskytovatel funguje.</span><span class="sxs-lookup"><span data-stu-id="a1b30-127">This section covers some of the internal implementation details of how this provider functions.</span></span> <span data-ttu-id="a1b30-128">Toto je pokročilé téma – většina aplikací, které využívají aplikační model, by tak měly fungovat pomocí konvencí.</span><span class="sxs-lookup"><span data-stu-id="a1b30-128">This is an advanced topic - most apps that leverage the application model should do so by working with conventions.</span></span>

<span data-ttu-id="a1b30-129">Implementace `IApplicationModelProvider` rozhraní "Wrap" navzájem a každá implementace volá `OnProvidersExecuting` vzestupné pořadí na základě jeho `Order` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="a1b30-129">Implementations of the `IApplicationModelProvider` interface "wrap" one another, with each implementation calling `OnProvidersExecuting` in ascending order based on its `Order` property.</span></span> <span data-ttu-id="a1b30-130">`OnProvidersExecuted`Metoda je pak volána v opačném pořadí.</span><span class="sxs-lookup"><span data-stu-id="a1b30-130">The `OnProvidersExecuted` method is then called in reverse order.</span></span> <span data-ttu-id="a1b30-131">Rozhraní definuje několik zprostředkovatelů:</span><span class="sxs-lookup"><span data-stu-id="a1b30-131">The framework defines several providers:</span></span>

<span data-ttu-id="a1b30-132">First ( `Order=-1000` ):</span><span class="sxs-lookup"><span data-stu-id="a1b30-132">First (`Order=-1000`):</span></span>

* [`DefaultApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.internal.defaultapplicationmodelprovider)

<span data-ttu-id="a1b30-133">Pak ( `Order=-990` ):</span><span class="sxs-lookup"><span data-stu-id="a1b30-133">Then (`Order=-990`):</span></span>

* [`AuthorizationApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.internal.authorizationapplicationmodelprovider)
* [`CorsApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.cors.internal.corsapplicationmodelprovider)

> [!NOTE]
> <span data-ttu-id="a1b30-134">Pořadí, ve kterém jsou voláni dva zprostředkovatelé se stejnou hodnotou pro `Order` , není definováno, a proto by nemělo být spoléhat na.</span><span class="sxs-lookup"><span data-stu-id="a1b30-134">The order in which two providers with the same value for `Order` are called is undefined, and therefore shouldn't be relied upon.</span></span>

> [!NOTE]
> <span data-ttu-id="a1b30-135">`IApplicationModelProvider` je pokročilý koncept pro rozšíření pro autory architektury.</span><span class="sxs-lookup"><span data-stu-id="a1b30-135">`IApplicationModelProvider` is an advanced concept for framework authors to extend.</span></span> <span data-ttu-id="a1b30-136">Obecně platí, že aplikace by měly používat konvence a rozhraní, které by měly používat poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="a1b30-136">In general, apps should use conventions and frameworks should use providers.</span></span> <span data-ttu-id="a1b30-137">Klíčovým rozdílem je to, že poskytovatelé se vždycky spouštějí před konvencemi.</span><span class="sxs-lookup"><span data-stu-id="a1b30-137">The key distinction is that providers always run before conventions.</span></span>

<span data-ttu-id="a1b30-138">`DefaultApplicationModelProvider`Naváže mnoho výchozích chování, které používá ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="a1b30-138">The `DefaultApplicationModelProvider` establishes many of the default behaviors used by ASP.NET Core MVC.</span></span> <span data-ttu-id="a1b30-139">Mezi tyto povinnosti patří:</span><span class="sxs-lookup"><span data-stu-id="a1b30-139">Its responsibilities include:</span></span>

* <span data-ttu-id="a1b30-140">Přidávání globálních filtrů do kontextu</span><span class="sxs-lookup"><span data-stu-id="a1b30-140">Adding global filters to the context</span></span>
* <span data-ttu-id="a1b30-141">Přidání řadičů do kontextu</span><span class="sxs-lookup"><span data-stu-id="a1b30-141">Adding controllers to the context</span></span>
* <span data-ttu-id="a1b30-142">Přidání metod veřejného kontroleru jako akcí</span><span class="sxs-lookup"><span data-stu-id="a1b30-142">Adding public controller methods as actions</span></span>
* <span data-ttu-id="a1b30-143">Přidání parametrů metody akce do kontextu</span><span class="sxs-lookup"><span data-stu-id="a1b30-143">Adding action method parameters to the context</span></span>
* <span data-ttu-id="a1b30-144">Použití směrování a dalších atributů</span><span class="sxs-lookup"><span data-stu-id="a1b30-144">Applying route and other attributes</span></span>

<span data-ttu-id="a1b30-145">Některá Vestavěná chování jsou implementovaná pomocí `DefaultApplicationModelProvider` .</span><span class="sxs-lookup"><span data-stu-id="a1b30-145">Some built-in behaviors are implemented by the `DefaultApplicationModelProvider`.</span></span> <span data-ttu-id="a1b30-146">Tento poskytovatel zodpovídá za vytváření [`ControllerModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.controllermodel) , což zase odkazuje na [`ActionModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.actionmodel) [`PropertyModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.propertymodel) [`ParameterModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.parametermodel) instance a.</span><span class="sxs-lookup"><span data-stu-id="a1b30-146">This provider is responsible for constructing the [`ControllerModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.controllermodel), which in turn references [`ActionModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.actionmodel), [`PropertyModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.propertymodel), and [`ParameterModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.parametermodel) instances.</span></span> <span data-ttu-id="a1b30-147">`DefaultApplicationModelProvider`Třída je podrobnosti implementace interního rozhraní, které se mohou v budoucnu změnit.</span><span class="sxs-lookup"><span data-stu-id="a1b30-147">The `DefaultApplicationModelProvider` class is an internal framework implementation detail that can and will change in the future.</span></span> 

<span data-ttu-id="a1b30-148">`AuthorizationApplicationModelProvider`Zodpovídá za použití chování přidruženého k `AuthorizeFilter` `AllowAnonymousFilter` atributům a.</span><span class="sxs-lookup"><span data-stu-id="a1b30-148">The `AuthorizationApplicationModelProvider` is responsible for applying the behavior associated with the `AuthorizeFilter` and `AllowAnonymousFilter` attributes.</span></span> <span data-ttu-id="a1b30-149">[Přečtěte si další informace o těchto atributech](xref:security/authorization/simple).</span><span class="sxs-lookup"><span data-stu-id="a1b30-149">[Learn more about these attributes](xref:security/authorization/simple).</span></span>

<span data-ttu-id="a1b30-150">`CorsApplicationModelProvider`Implementuje chování spojené s `IEnableCorsAttribute` a `IDisableCorsAttribute` `DisableCorsAuthorizationFilter` .</span><span class="sxs-lookup"><span data-stu-id="a1b30-150">The `CorsApplicationModelProvider` implements behavior associated with the `IEnableCorsAttribute` and `IDisableCorsAttribute`, and the `DisableCorsAuthorizationFilter`.</span></span> <span data-ttu-id="a1b30-151">[Přečtěte si další informace o CORS](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="a1b30-151">[Learn more about CORS](xref:security/cors).</span></span>

## <a name="conventions"></a><span data-ttu-id="a1b30-152">Konvence</span><span class="sxs-lookup"><span data-stu-id="a1b30-152">Conventions</span></span>

<span data-ttu-id="a1b30-153">Model aplikace definuje abstrakce konvence, které poskytují jednodušší způsob přizpůsobení chování modelů, než je přepsání celého modelu nebo poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="a1b30-153">The application model defines convention abstractions that provide a simpler way to customize the behavior of the models than overriding the entire model or provider.</span></span> <span data-ttu-id="a1b30-154">Tyto abstrakce jsou doporučeným způsobem, jak upravit chování vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="a1b30-154">These abstractions are the recommended way to modify your app's behavior.</span></span> <span data-ttu-id="a1b30-155">Konvence poskytují způsob, jak napsat kód, který bude dynamicky používat vlastní nastavení.</span><span class="sxs-lookup"><span data-stu-id="a1b30-155">Conventions provide a way for you to write code that will dynamically apply customizations.</span></span> <span data-ttu-id="a1b30-156">[Filtry](xref:mvc/controllers/filters) sice poskytují prostředky pro úpravu chování rozhraní. vlastní nastavení vám umožní řídit, jak celá aplikace funguje dohromady.</span><span class="sxs-lookup"><span data-stu-id="a1b30-156">While [filters](xref:mvc/controllers/filters) provide a means of modifying the framework's behavior, customizations let you control how the whole app works together.</span></span>

<span data-ttu-id="a1b30-157">K dispozici jsou následující konvence:</span><span class="sxs-lookup"><span data-stu-id="a1b30-157">The following conventions are available:</span></span>

* [`IApplicationModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelconvention)
* [`IControllerModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.icontrollermodelconvention)
* [`IActionModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iactionmodelconvention)
* [`IParameterModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iparametermodelconvention)

<span data-ttu-id="a1b30-158">Konvence se použijí tak, že je přidáte do možností MVC nebo implementujete `Attribute` s a použijete je na řadiče, akce nebo parametry akce (podobně jako [`Filters`](xref:mvc/controllers/filters) ).</span><span class="sxs-lookup"><span data-stu-id="a1b30-158">Conventions are applied by adding them to MVC options or by implementing `Attribute`s and applying them to controllers, actions, or action parameters (similar to [`Filters`](xref:mvc/controllers/filters)).</span></span> <span data-ttu-id="a1b30-159">Na rozdíl od filtrů se konvence spouští jenom při spuštění aplikace, ne jako součást každé žádosti.</span><span class="sxs-lookup"><span data-stu-id="a1b30-159">Unlike filters, conventions are only executed when the app is starting, not as part of each request.</span></span>

### <a name="sample-modifying-the-applicationmodel"></a><span data-ttu-id="a1b30-160">Ukázka: Změna ApplicationModel</span><span class="sxs-lookup"><span data-stu-id="a1b30-160">Sample: Modifying the ApplicationModel</span></span>

<span data-ttu-id="a1b30-161">Následující konvence se používá k přidání vlastnosti do aplikačního modelu.</span><span class="sxs-lookup"><span data-stu-id="a1b30-161">The following convention is used to add a property to the application model.</span></span> 

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ApplicationDescription.cs)]

<span data-ttu-id="a1b30-162">Konvence aplikačního modelu se používají jako možnosti při přidání MVC do `ConfigureServices` v `Startup` .</span><span class="sxs-lookup"><span data-stu-id="a1b30-162">Application model conventions are applied as options when MVC is added in `ConfigureServices` in `Startup`.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=5)]

<span data-ttu-id="a1b30-163">Vlastnosti jsou přístupné z `ActionDescriptor` kolekce Properties v rámci akcí kontroleru:</span><span class="sxs-lookup"><span data-stu-id="a1b30-163">Properties are accessible from the `ActionDescriptor` properties collection within controller actions:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/AppModelController.cs?name=AppModelController)]

### <a name="sample-modifying-the-controllermodel-description"></a><span data-ttu-id="a1b30-164">Ukázka: Změna popisu ControllerModel</span><span class="sxs-lookup"><span data-stu-id="a1b30-164">Sample: Modifying the ControllerModel Description</span></span>

<span data-ttu-id="a1b30-165">Jak je uvedeno v předchozím příkladu, model kontroleru se taky dá upravit tak, aby zahrnoval vlastní vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="a1b30-165">As in the previous example, the controller model can also be modified to include custom properties.</span></span> <span data-ttu-id="a1b30-166">Přepíší se stávající vlastnosti se stejným názvem zadaným v aplikačním modelu.</span><span class="sxs-lookup"><span data-stu-id="a1b30-166">These will override existing properties with the same name specified in the application model.</span></span> <span data-ttu-id="a1b30-167">Následující atribut konvence přidá popis na úrovni kontroleru:</span><span class="sxs-lookup"><span data-stu-id="a1b30-167">The following convention attribute adds a description at the controller level:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ControllerDescriptionAttribute.cs)]

<span data-ttu-id="a1b30-168">Tato konvence se používá jako atribut na řadiči.</span><span class="sxs-lookup"><span data-stu-id="a1b30-168">This convention is applied as an attribute on a controller.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=ControllerDescription&highlight=1)]

<span data-ttu-id="a1b30-169">K vlastnosti Description je přistup stejným způsobem jako v předchozích příkladech.</span><span class="sxs-lookup"><span data-stu-id="a1b30-169">The "description" property is accessed in the same manner as in previous examples.</span></span>

### <a name="sample-modifying-the-actionmodel-description"></a><span data-ttu-id="a1b30-170">Ukázka: Změna popisu ActionModel</span><span class="sxs-lookup"><span data-stu-id="a1b30-170">Sample: Modifying the ActionModel Description</span></span>

<span data-ttu-id="a1b30-171">Samostatnou konvenci atributu lze použít na jednotlivé akce, přepisující chování, které již bylo použito na úrovni aplikace nebo kontroleru.</span><span class="sxs-lookup"><span data-stu-id="a1b30-171">A separate attribute convention can be applied to individual actions, overriding behavior already applied at the application or controller level.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ActionDescriptionAttribute.cs)]

<span data-ttu-id="a1b30-172">Použití této akce u kontroleru v předchozím příkladu ukazuje, jak Přepisuje konvenci na úrovni řadiče:</span><span class="sxs-lookup"><span data-stu-id="a1b30-172">Applying this to an action within the previous example's controller demonstrates how it overrides the controller-level convention:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=DescriptionAttributesController&highlight=9)]

### <a name="sample-modifying-the-parametermodel"></a><span data-ttu-id="a1b30-173">Ukázka: Změna ParameterModel</span><span class="sxs-lookup"><span data-stu-id="a1b30-173">Sample: Modifying the ParameterModel</span></span>

<span data-ttu-id="a1b30-174">Následující konvenci je možné použít na parametry akce a upravit jejich `BindingInfo` .</span><span class="sxs-lookup"><span data-stu-id="a1b30-174">The following convention can be applied to action parameters to modify their `BindingInfo`.</span></span> <span data-ttu-id="a1b30-175">Následující konvence vyžaduje, aby parametr byl parametrem trasy; Další možné zdroje vazeb (například hodnoty řetězce dotazu) se ignorují.</span><span class="sxs-lookup"><span data-stu-id="a1b30-175">The following convention requires that the parameter be a route parameter; other potential binding sources (such as query string values) are ignored.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/MustBeInRouteParameterModelConvention.cs)]

<span data-ttu-id="a1b30-176">Atribut lze použít pro libovolný parametr akce:</span><span class="sxs-lookup"><span data-stu-id="a1b30-176">The attribute may be applied to any action parameter:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/ParameterModelController.cs?name=ParameterModelController&highlight=5)]

### <a name="sample-modifying-the-actionmodel-name"></a><span data-ttu-id="a1b30-177">Ukázka: Změna názvu ActionModel</span><span class="sxs-lookup"><span data-stu-id="a1b30-177">Sample: Modifying the ActionModel Name</span></span>

<span data-ttu-id="a1b30-178">Následující konvence upravuje `ActionModel` a aktualizuje *název* akce, na kterou se aplikuje.</span><span class="sxs-lookup"><span data-stu-id="a1b30-178">The following convention modifies the `ActionModel` to update the *name* of the action to which it's applied.</span></span> <span data-ttu-id="a1b30-179">Nový název je zadán jako parametr atributu.</span><span class="sxs-lookup"><span data-stu-id="a1b30-179">The new name is provided as a parameter to the attribute.</span></span> <span data-ttu-id="a1b30-180">Tento nový název je používán směrováním, takže bude mít vliv na trasu použitou k dosažení této metody akce.</span><span class="sxs-lookup"><span data-stu-id="a1b30-180">This new name is used by routing, so it will affect the route used to reach this action method.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/CustomActionNameAttribute.cs)]

<span data-ttu-id="a1b30-181">Tento atribut se používá pro metodu akce v `HomeController` :</span><span class="sxs-lookup"><span data-stu-id="a1b30-181">This attribute is applied to an action method in the `HomeController`:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/HomeController.cs?name=ActionModelConvention&highlight=2)]

<span data-ttu-id="a1b30-182">I když je název metody `SomeName` , atribut přepíše konvenci MVC pomocí názvu metody a nahradí název akce `MyCoolAction` .</span><span class="sxs-lookup"><span data-stu-id="a1b30-182">Even though the method name is `SomeName`, the attribute overrides the MVC convention of using the method name and replaces the action name with `MyCoolAction`.</span></span> <span data-ttu-id="a1b30-183">Proto trasa použitá k dosažení této akce je `/Home/MyCoolAction` .</span><span class="sxs-lookup"><span data-stu-id="a1b30-183">Thus, the route used to reach this action is `/Home/MyCoolAction`.</span></span>

> [!NOTE]
> <span data-ttu-id="a1b30-184">Tento příklad je v podstatě stejný jako při použití předdefinovaného atributu [Action](/dotnet/api/microsoft.aspnetcore.mvc.actionnameattribute) .</span><span class="sxs-lookup"><span data-stu-id="a1b30-184">This example is essentially the same as using the built-in [ActionName](/dotnet/api/microsoft.aspnetcore.mvc.actionnameattribute) attribute.</span></span>

### <a name="sample-custom-routing-convention"></a><span data-ttu-id="a1b30-185">Ukázka: vlastní konvence směrování</span><span class="sxs-lookup"><span data-stu-id="a1b30-185">Sample: Custom Routing Convention</span></span>

<span data-ttu-id="a1b30-186">`IApplicationModelConvention`K přizpůsobení fungování směrování můžete použít.</span><span class="sxs-lookup"><span data-stu-id="a1b30-186">You can use an `IApplicationModelConvention` to customize how routing works.</span></span> <span data-ttu-id="a1b30-187">Například následující konvence zahrne do svých tras obory názvů řadiče a nahradí je `.` v oboru názvů `/` v cestě:</span><span class="sxs-lookup"><span data-stu-id="a1b30-187">For example, the following convention will incorporate Controllers' namespaces into their routes, replacing `.` in the namespace with `/` in the route:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/NamespaceRoutingConvention.cs)]

<span data-ttu-id="a1b30-188">Konvence je přidána jako možnost při spuštění.</span><span class="sxs-lookup"><span data-stu-id="a1b30-188">The convention is added as an option in Startup.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=6)]

> [!TIP]
> <span data-ttu-id="a1b30-189">K vašemu [middlewaru](xref:fundamentals/middleware/index) můžete přidat konvence tím, že budete přistupovat `MvcOptions` pomocí `services.Configure<MvcOptions>(c => c.Conventions.Add(YOURCONVENTION));`</span><span class="sxs-lookup"><span data-stu-id="a1b30-189">You can add conventions to your [middleware](xref:fundamentals/middleware/index) by accessing `MvcOptions` using `services.Configure<MvcOptions>(c => c.Conventions.Add(YOURCONVENTION));`</span></span>

<span data-ttu-id="a1b30-190">Tato ukázka používá tuto konvenci na trasy, které nepoužívají směrování atributů, kde Controller má v názvu "Namespace".</span><span class="sxs-lookup"><span data-stu-id="a1b30-190">This sample applies this convention to routes that are not using attribute routing where the controller has  "Namespace" in its name.</span></span> <span data-ttu-id="a1b30-191">Následující kontroler znázorňuje tuto konvenci:</span><span class="sxs-lookup"><span data-stu-id="a1b30-191">The following controller demonstrates this convention:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/NamespaceRoutingController.cs?highlight=7-8)]

## <a name="application-model-usage-in-webapicompatshim"></a><span data-ttu-id="a1b30-192">Využití aplikačního modelu v WebApiCompatShim</span><span class="sxs-lookup"><span data-stu-id="a1b30-192">Application Model Usage in WebApiCompatShim</span></span>

<span data-ttu-id="a1b30-193">ASP.NET Core MVC používá jinou sadu konvencí z webového rozhraní API 2 pro ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="a1b30-193">ASP.NET Core MVC uses a different set of conventions from ASP.NET Web API 2.</span></span> <span data-ttu-id="a1b30-194">Pomocí vlastních konvencí můžete upravit chování aplikace ASP.NET Core MVC tak, aby byla konzistentní s tím, jak je aplikace webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="a1b30-194">Using custom conventions, you can modify an ASP.NET Core MVC app's behavior to be consistent with that of a Web API app.</span></span> <span data-ttu-id="a1b30-195">Společnost Microsoft dodává [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim/) speciálně pro tento účel.</span><span class="sxs-lookup"><span data-stu-id="a1b30-195">Microsoft ships the [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim/) specifically for this purpose.</span></span>

> [!NOTE]
> <span data-ttu-id="a1b30-196">Přečtěte si další informace o [migraci z webového rozhraní API ASP.NET](xref:migration/webapi).</span><span class="sxs-lookup"><span data-stu-id="a1b30-196">Learn more about [migration from ASP.NET Web API](xref:migration/webapi).</span></span>

<span data-ttu-id="a1b30-197">Chcete-li použít překrytí kompatibility webového rozhraní API, je nutné přidat balíček do projektu a pak přidat konvence do MVC voláním `AddWebApiConventions` `Startup` :</span><span class="sxs-lookup"><span data-stu-id="a1b30-197">To use the Web API Compatibility Shim, you need to add the package to your project and then add the conventions to MVC by calling `AddWebApiConventions` in `Startup`:</span></span>

```csharp
services.AddMvc().AddWebApiConventions();
```

<span data-ttu-id="a1b30-198">Konvence překrytí se aplikují jenom na části aplikace, u kterých se používají určité atributy.</span><span class="sxs-lookup"><span data-stu-id="a1b30-198">The conventions provided by the shim are only applied to parts of the app that have had certain attributes applied to them.</span></span> <span data-ttu-id="a1b30-199">Následující čtyři atributy slouží k řízení, které řadiče by měly mít své konvence upravené konvencemi překrytí:</span><span class="sxs-lookup"><span data-stu-id="a1b30-199">The following four attributes are used to control which controllers should have their conventions modified by the shim's conventions:</span></span>

* [<span data-ttu-id="a1b30-200">UseWebApiActionConventionsAttribute</span><span class="sxs-lookup"><span data-stu-id="a1b30-200">UseWebApiActionConventionsAttribute</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiactionconventionsattribute)
* [<span data-ttu-id="a1b30-201">UseWebApiOverloadingAttribute</span><span class="sxs-lookup"><span data-stu-id="a1b30-201">UseWebApiOverloadingAttribute</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapioverloadingattribute)
* [<span data-ttu-id="a1b30-202">UseWebApiParameterConventionsAttribute</span><span class="sxs-lookup"><span data-stu-id="a1b30-202">UseWebApiParameterConventionsAttribute</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiparameterconventionsattribute)
* [<span data-ttu-id="a1b30-203">UseWebApiRoutesAttribute</span><span class="sxs-lookup"><span data-stu-id="a1b30-203">UseWebApiRoutesAttribute</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiroutesattribute)

### <a name="action-conventions"></a><span data-ttu-id="a1b30-204">Konvence akcí</span><span class="sxs-lookup"><span data-stu-id="a1b30-204">Action Conventions</span></span>

<span data-ttu-id="a1b30-205">`UseWebApiActionConventionsAttribute`Slouží k mapování metody HTTP na akce založené na jejich názvu (například `Get` by namapovaly na `HttpGet` ).</span><span class="sxs-lookup"><span data-stu-id="a1b30-205">The `UseWebApiActionConventionsAttribute` is used to map the HTTP method to actions based on their name (for instance, `Get` would map to `HttpGet`).</span></span> <span data-ttu-id="a1b30-206">Vztahuje se pouze na akce, které nepoužívají směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="a1b30-206">It only applies to actions that don't use attribute routing.</span></span>

### <a name="overloading"></a><span data-ttu-id="a1b30-207">Přetížení</span><span class="sxs-lookup"><span data-stu-id="a1b30-207">Overloading</span></span>

<span data-ttu-id="a1b30-208">`UseWebApiOverloadingAttribute`Slouží k použití `WebApiOverloadingApplicationModelConvention` úmluvy.</span><span class="sxs-lookup"><span data-stu-id="a1b30-208">The `UseWebApiOverloadingAttribute` is used to apply the `WebApiOverloadingApplicationModelConvention` convention.</span></span> <span data-ttu-id="a1b30-209">Tato konvence přičítá `OverloadActionConstraint` k procesu výběru akce, který omezuje akce kandidáta na ty, pro které požadavek splňuje všechny nepovinné parametry.</span><span class="sxs-lookup"><span data-stu-id="a1b30-209">This convention adds an `OverloadActionConstraint` to the action selection process, which limits candidate actions to those for which the request satisfies all non-optional parameters.</span></span>

### <a name="parameter-conventions"></a><span data-ttu-id="a1b30-210">Konvence parametrů</span><span class="sxs-lookup"><span data-stu-id="a1b30-210">Parameter Conventions</span></span>

<span data-ttu-id="a1b30-211">`UseWebApiParameterConventionsAttribute`Slouží k použití `WebApiParameterConventionsApplicationModelConvention` úmluvy akce.</span><span class="sxs-lookup"><span data-stu-id="a1b30-211">The `UseWebApiParameterConventionsAttribute` is used to apply the `WebApiParameterConventionsApplicationModelConvention` action convention.</span></span> <span data-ttu-id="a1b30-212">Tato konvence určuje, že jednoduché typy používané jako parametry akce jsou ve výchozím nastavení vázány z identifikátoru URI, zatímco komplexní typy jsou vázány z textu žádosti.</span><span class="sxs-lookup"><span data-stu-id="a1b30-212">This convention specifies that simple types used as action parameters are bound from the URI by default, while complex types are bound from the request body.</span></span>

### <a name="routes"></a><span data-ttu-id="a1b30-213">Trasy</span><span class="sxs-lookup"><span data-stu-id="a1b30-213">Routes</span></span>

<span data-ttu-id="a1b30-214">`UseWebApiRoutesAttribute`Určuje, zda `WebApiApplicationModelConvention` je použita konvence kontroleru.</span><span class="sxs-lookup"><span data-stu-id="a1b30-214">The `UseWebApiRoutesAttribute` controls whether the `WebApiApplicationModelConvention` controller convention is applied.</span></span> <span data-ttu-id="a1b30-215">V případě povolení se tato konvence používá k přidání podpory pro [oblasti](xref:mvc/controllers/areas) do trasy.</span><span class="sxs-lookup"><span data-stu-id="a1b30-215">When enabled, this convention is used to add support for [areas](xref:mvc/controllers/areas) to the route.</span></span>

<span data-ttu-id="a1b30-216">Kromě sady konvencí balíček kompatibility zahrnuje `System.Web.Http.ApiController` základní třídu, která nahrazuje rozhraní API, které poskytuje webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="a1b30-216">In addition to a set of conventions, the compatibility package includes a `System.Web.Http.ApiController` base class that replaces the one provided by Web API.</span></span> <span data-ttu-id="a1b30-217">To umožňuje řadičům napsaným pro webové rozhraní API a dědění z jeho `ApiController` fungování, aby fungovalo tak, jak byly navrženy a běžely na ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="a1b30-217">This allows your controllers written for Web API and inheriting from its `ApiController` to work as they were designed, while running on ASP.NET Core MVC.</span></span> <span data-ttu-id="a1b30-218">Všechny výše `UseWebApi*` uvedené atributy jsou aplikovány na základní třídu kontroleru.</span><span class="sxs-lookup"><span data-stu-id="a1b30-218">All of the `UseWebApi*` attributes listed earlier are applied to the base controller class.</span></span> <span data-ttu-id="a1b30-219">`ApiController`Zpřístupňuje vlastnosti, metody a typy výsledků, které jsou kompatibilní s metodami nalezenými ve webovém rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="a1b30-219">The `ApiController` exposes properties, methods, and result types that are compatible with those found in Web API.</span></span>

## <a name="using-apiexplorer-to-document-your-app"></a><span data-ttu-id="a1b30-220">Použití ApiExplorer k dokumentování aplikace</span><span class="sxs-lookup"><span data-stu-id="a1b30-220">Using ApiExplorer to Document Your App</span></span>

<span data-ttu-id="a1b30-221">Aplikační model zpřístupňuje [`ApiExplorer`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.apiexplorermodel) vlastnost na každé úrovni, kterou lze použít k procházení struktury aplikace.</span><span class="sxs-lookup"><span data-stu-id="a1b30-221">The application model exposes an [`ApiExplorer`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.apiexplorermodel) property at each level that can be used to traverse the app's structure.</span></span> <span data-ttu-id="a1b30-222">To lze použít ke [generování stránek s nápovědu pro vaše webová rozhraní API pomocí nástrojů jako Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span><span class="sxs-lookup"><span data-stu-id="a1b30-222">This can be used to [generate help pages for your Web APIs using tools like Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span> <span data-ttu-id="a1b30-223">`ApiExplorer`Vlastnost zpřístupňuje `IsVisible` vlastnost, která může být nastavena tak, aby určovala, které části modelu vaší aplikace by měly být vystaveny.</span><span class="sxs-lookup"><span data-stu-id="a1b30-223">The `ApiExplorer` property exposes an `IsVisible` property that can be set to specify which parts of your app's model should be exposed.</span></span> <span data-ttu-id="a1b30-224">Toto nastavení můžete nakonfigurovat pomocí konvence:</span><span class="sxs-lookup"><span data-stu-id="a1b30-224">You can configure this setting using a convention:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/EnableApiExplorerApplicationConvention.cs)]

<span data-ttu-id="a1b30-225">Pomocí tohoto přístupu (a v případě potřeby další konvence) můžete viditelnost rozhraní API povolit nebo zakázat v libovolné úrovni aplikace.</span><span class="sxs-lookup"><span data-stu-id="a1b30-225">Using this approach (and additional conventions if required), you can enable or disable API visibility at any level within your app.</span></span> 
