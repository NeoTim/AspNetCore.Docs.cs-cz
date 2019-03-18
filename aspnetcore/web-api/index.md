---
title: Vytvoření webového rozhraní API pomocí ASP.NET Core
author: scottaddie
description: 'Další informace o funkcích, které jsou k dispozici pro vytváření webových rozhraní API v ASP.NET Core a kdy je vhodné používat jednotlivé funkce.'
ms.author: scaddie
ms.custom: mvc
ms.date: 01/11/2019
uid: web-api/index
---
# <a name="build-web-apis-with-aspnet-core"></a><span data-ttu-id="8861e-103">Vytvoření webového rozhraní API pomocí ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8861e-103">Build web APIs with ASP.NET Core</span></span>

<span data-ttu-id="8861e-104">Podle [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="8861e-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="8861e-105">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/define-controller/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8861e-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/define-controller/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="8861e-106">Tento dokument popisuje, jak vytvořit webové rozhraní API v ASP.NET Core a je nejvhodnější používat jednotlivé funkce.</span><span class="sxs-lookup"><span data-stu-id="8861e-106">This document explains how to build a web API in ASP.NET Core and when it's most appropriate to use each feature.</span></span>

## <a name="derive-class-from-controllerbase"></a><span data-ttu-id="8861e-107">Odvodit třídu z ControllerBase</span><span class="sxs-lookup"><span data-stu-id="8861e-107">Derive class from ControllerBase</span></span>

<span data-ttu-id="8861e-108">Dědí <xref:Microsoft.AspNetCore.Mvc.ControllerBase> třídy v kontroleru, který má sloužit jako webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="8861e-108">Inherit from the <xref:Microsoft.AspNetCore.Mvc.ControllerBase> class in a controller that's intended to serve as a web API.</span></span> <span data-ttu-id="8861e-109">Příklad:</span><span class="sxs-lookup"><span data-stu-id="8861e-109">For example:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Controllers/PetsController.cs?name=snippet_PetsController&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](define-controller/samples/WebApiSample.Api.Pre21/Controllers/PetsController.cs?name=snippet_PetsController&highlight=3)]

::: moniker-end

<span data-ttu-id="8861e-110">`ControllerBase` Třídě poskytuje přístup k několika vlastností a metod.</span><span class="sxs-lookup"><span data-stu-id="8861e-110">The `ControllerBase` class provides access to several properties and methods.</span></span> <span data-ttu-id="8861e-111">V předchozím kódu mezi příklady patří <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest(Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)> a <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction(System.String,System.Object,System.Object)>.</span><span class="sxs-lookup"><span data-stu-id="8861e-111">In the preceding code, examples include <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest(Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction(System.String,System.Object,System.Object)>.</span></span> <span data-ttu-id="8861e-112">Tyto metody jsou volány v rámci metod akce vrátit kód HTTP 400 a 201 stavové kódy, v uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="8861e-112">These methods are called within action methods to return HTTP 400 and 201 status codes, respectively.</span></span> <span data-ttu-id="8861e-113"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState> Vlastnost také poskytuje `ControllerBase`, přistupuje ke zpracování žádosti o ověření modelu.</span><span class="sxs-lookup"><span data-stu-id="8861e-113">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState> property, also provided by `ControllerBase`, is accessed to handle request model validation.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="annotation-with-apicontroller-attribute"></a><span data-ttu-id="8861e-114">Poznámka atributem objektu ApiController</span><span class="sxs-lookup"><span data-stu-id="8861e-114">Annotation with ApiController attribute</span></span>

<span data-ttu-id="8861e-115">ASP.NET Core 2.1 přináší [[objektu ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atribut k označení webového rozhraní API třídy kontroleru.</span><span class="sxs-lookup"><span data-stu-id="8861e-115">ASP.NET Core 2.1 introduces the [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute to denote a web API controller class.</span></span> <span data-ttu-id="8861e-116">Příklad:</span><span class="sxs-lookup"><span data-stu-id="8861e-116">For example:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_ControllerSignature&highlight=2)]

<span data-ttu-id="8861e-117">Kompatibilita verze 2.1 nebo novější, nastavené přes <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*>, je potřeba použít tento atribut na úrovni kontroleru.</span><span class="sxs-lookup"><span data-stu-id="8861e-117">A compatibility version of 2.1 or later, set via <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*>, is required to use this attribute at the controller level.</span></span> <span data-ttu-id="8861e-118">Například zvýrazněný kód do `Startup.ConfigureServices` nastaví příznak 2.1 kompatibility:</span><span class="sxs-lookup"><span data-stu-id="8861e-118">For example, the highlighted code in `Startup.ConfigureServices` sets the 2.1 compatibility flag:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Startup.cs?name=snippet_SetCompatibilityVersion&highlight=2)]

<span data-ttu-id="8861e-119">Další informace naleznete v tématu <xref:mvc/compatibility-version>.</span><span class="sxs-lookup"><span data-stu-id="8861e-119">For more information, see <xref:mvc/compatibility-version>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="8861e-120">V ASP.NET Core 2.2 nebo vyšší `[ApiController]` atribut lze použít k sestavení.</span><span class="sxs-lookup"><span data-stu-id="8861e-120">In ASP.NET Core 2.2 or later, the `[ApiController]` attribute can be applied to an assembly.</span></span> <span data-ttu-id="8861e-121">Poznámka tímto způsobem použít chování webového rozhraní API do všech řadičů v sestavení.</span><span class="sxs-lookup"><span data-stu-id="8861e-121">Annotation in this manner applies web API behavior to all controllers in the assembly.</span></span> <span data-ttu-id="8861e-122">Mějte na paměti, že neexistuje žádný způsob, jak se odhlásit pro jednotlivé řadiče.</span><span class="sxs-lookup"><span data-stu-id="8861e-122">Beware that there's no way to opt out for individual controllers.</span></span> <span data-ttu-id="8861e-123">Jako doporučení, je třeba použít atributy úrovně sestavení `Startup` třídy:</span><span class="sxs-lookup"><span data-stu-id="8861e-123">As a recommendation, assembly-level attributes should be applied to the `Startup` class:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.22/Startup.cs?name=snippet_ApiControllerAttributeOnAssembly&highlight=1)]

<span data-ttu-id="8861e-124">Kompatibilita verze 2.2 nebo vyšší, nastavené přes <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*>, je potřeba použít tento atribut na úrovni sestavení.</span><span class="sxs-lookup"><span data-stu-id="8861e-124">A compatibility version of 2.2 or later, set via <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*>, is required to use this attribute at the assembly level.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="8861e-125">`[ApiController]` Atribut běžně doplňuje `ControllerBase` povolit chování specifické pro REST pro řadiče.</span><span class="sxs-lookup"><span data-stu-id="8861e-125">The `[ApiController]` attribute is commonly coupled with `ControllerBase` to enable REST-specific behavior for controllers.</span></span> <span data-ttu-id="8861e-126">`ControllerBase` poskytuje přístup k metodám například <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> a <xref:Microsoft.AspNetCore.Mvc.ControllerBase.File*>.</span><span class="sxs-lookup"><span data-stu-id="8861e-126">`ControllerBase` provides access to methods such as <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.File*>.</span></span>

<span data-ttu-id="8861e-127">Další možností je vytvořit třídu vlastní základní kontroler opatřen poznámkou `[ApiController]` atribut:</span><span class="sxs-lookup"><span data-stu-id="8861e-127">Another approach is to create a custom base controller class annotated with the `[ApiController]` attribute:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Controllers/MyBaseController.cs?name=snippet_ControllerSignature)]

<span data-ttu-id="8861e-128">Následující části popisují užitečných funkcí, které jsou přidány pomocí atributu.</span><span class="sxs-lookup"><span data-stu-id="8861e-128">The following sections describe convenience features added by the attribute.</span></span>

### <a name="automatic-http-400-responses"></a><span data-ttu-id="8861e-129">Automatické odpovědi HTTP 400</span><span class="sxs-lookup"><span data-stu-id="8861e-129">Automatic HTTP 400 responses</span></span>

<span data-ttu-id="8861e-130">Chyby ověření modelu se automaticky aktivuje odpověď HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="8861e-130">Model validation errors automatically trigger an HTTP 400 response.</span></span> <span data-ttu-id="8861e-131">V důsledku toho bude nutná u akcí následující kód:</span><span class="sxs-lookup"><span data-stu-id="8861e-131">Consequently, the following code becomes unnecessary in your actions:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.Pre21/Controllers/PetsController.cs?name=snippet_ModelStateIsValidCheck)]

<span data-ttu-id="8861e-132">Použití <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> přizpůsobení výstup výsledné odpovědi.</span><span class="sxs-lookup"><span data-stu-id="8861e-132">Use <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> to customize the output of the resulting response.</span></span>

<span data-ttu-id="8861e-133">Zakazuje výchozí chování je užitečné, když vaše akce můžete obnovit z chyby ověření modelu.</span><span class="sxs-lookup"><span data-stu-id="8861e-133">Disabling the default behavior is useful when your action can recover from a model validation error.</span></span> <span data-ttu-id="8861e-134">Výchozí chování je zakázaná. Pokud <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> je nastavena na `true`.</span><span class="sxs-lookup"><span data-stu-id="8861e-134">The default behavior is disabled when the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> property is set to `true`.</span></span> <span data-ttu-id="8861e-135">Přidejte následující kód do `Startup.ConfigureServices` po `services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_<version_number>);`:</span><span class="sxs-lookup"><span data-stu-id="8861e-135">Add the following code in `Startup.ConfigureServices` after `services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_<version_number>);`:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](define-controller/samples/WebApiSample.Api.22/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=7)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=5)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="8861e-136">Pomocí příznaku kompatibility 2.2 nebo novější, je výchozí typ odpovědi pro odpovědi HTTP 400 <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span><span class="sxs-lookup"><span data-stu-id="8861e-136">With a compatibility flag of 2.2 or later, the default response type for HTTP 400 responses is <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="8861e-137">`ValidationProblemDetails` Typ v souladu s [specifikaci RFC 7807](https://tools.ietf.org/html/rfc7807).</span><span class="sxs-lookup"><span data-stu-id="8861e-137">The `ValidationProblemDetails` type complies with the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807).</span></span> <span data-ttu-id="8861e-138">Nastavte `SuppressUseValidationProblemDetailsForInvalidModelStateResponses` vlastnost `true` na místo toho vrátí Chyba formátu ASP.NET Core 2.1 <xref:Microsoft.AspNetCore.Mvc.SerializableError>.</span><span class="sxs-lookup"><span data-stu-id="8861e-138">Set the `SuppressUseValidationProblemDetailsForInvalidModelStateResponses` property to `true` to instead return the ASP.NET Core 2.1 error format of <xref:Microsoft.AspNetCore.Mvc.SerializableError>.</span></span> <span data-ttu-id="8861e-139">Přidejte následující kód do `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="8861e-139">Add the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2)
    .ConfigureApiBehaviorOptions(options =>
    {
        options
          .SuppressUseValidationProblemDetailsForInvalidModelStateResponses = true;
    });
```

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

### <a name="binding-source-parameter-inference"></a><span data-ttu-id="8861e-140">Odvození parametr zdroje vazby</span><span class="sxs-lookup"><span data-stu-id="8861e-140">Binding source parameter inference</span></span>

<span data-ttu-id="8861e-141">Zdrojový atribut vazby definuje umístění, ve kterém není nalezena hodnota parametru akce.</span><span class="sxs-lookup"><span data-stu-id="8861e-141">A binding source attribute defines the location at which an action parameter's value is found.</span></span> <span data-ttu-id="8861e-142">Existují následující atributy zdroje vazby:</span><span class="sxs-lookup"><span data-stu-id="8861e-142">The following binding source attributes exist:</span></span>

|<span data-ttu-id="8861e-143">Atribut</span><span class="sxs-lookup"><span data-stu-id="8861e-143">Attribute</span></span>|<span data-ttu-id="8861e-144">Zdroje připojení</span><span class="sxs-lookup"><span data-stu-id="8861e-144">Binding source</span></span> |
|---------|---------|
|<span data-ttu-id="8861e-145">**[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)**</span><span class="sxs-lookup"><span data-stu-id="8861e-145">**[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)**</span></span>     | <span data-ttu-id="8861e-146">Text požadavku</span><span class="sxs-lookup"><span data-stu-id="8861e-146">Request body</span></span> |
|<span data-ttu-id="8861e-147">**[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)**</span><span class="sxs-lookup"><span data-stu-id="8861e-147">**[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)**</span></span>     | <span data-ttu-id="8861e-148">Data formuláře v textu požadavku</span><span class="sxs-lookup"><span data-stu-id="8861e-148">Form data in the request body</span></span> |
|<span data-ttu-id="8861e-149">**[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)**</span><span class="sxs-lookup"><span data-stu-id="8861e-149">**[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)**</span></span> | <span data-ttu-id="8861e-150">Hlavička požadavku</span><span class="sxs-lookup"><span data-stu-id="8861e-150">Request header</span></span> |
|<span data-ttu-id="8861e-151">**[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)**</span><span class="sxs-lookup"><span data-stu-id="8861e-151">**[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)**</span></span>   | <span data-ttu-id="8861e-152">Požádat o parametru řetězce dotazu</span><span class="sxs-lookup"><span data-stu-id="8861e-152">Request query string parameter</span></span> |
|<span data-ttu-id="8861e-153">**[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)**</span><span class="sxs-lookup"><span data-stu-id="8861e-153">**[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)**</span></span>   | <span data-ttu-id="8861e-154">Data trasy z aktuální žádosti</span><span class="sxs-lookup"><span data-stu-id="8861e-154">Route data from the current request</span></span> |
|<span data-ttu-id="8861e-155">**[[FromServices]](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices)**</span><span class="sxs-lookup"><span data-stu-id="8861e-155">**[[FromServices]](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices)**</span></span> | <span data-ttu-id="8861e-156">Žádost o služby, vložený jako parametru akce</span><span class="sxs-lookup"><span data-stu-id="8861e-156">The request service injected as an action parameter</span></span> |

> [!WARNING]
> <span data-ttu-id="8861e-157">Nepoužívejte `[FromRoute]` při může obsahovat hodnoty `%2f` (to znamená `/`).</span><span class="sxs-lookup"><span data-stu-id="8861e-157">Don't use `[FromRoute]` when values might contain `%2f` (that is `/`).</span></span> <span data-ttu-id="8861e-158">`%2f` nebude znaků bez řídících k `/`.</span><span class="sxs-lookup"><span data-stu-id="8861e-158">`%2f` won't be unescaped to `/`.</span></span> <span data-ttu-id="8861e-159">Použití `[FromQuery]` Pokud hodnota může obsahovat `%2f`.</span><span class="sxs-lookup"><span data-stu-id="8861e-159">Use `[FromQuery]` if the value might contain `%2f`.</span></span>

<span data-ttu-id="8861e-160">Bez `[ApiController]` atribut, vytvoření vazby zdroje nejsou explicitně definovány atributy.</span><span class="sxs-lookup"><span data-stu-id="8861e-160">Without the `[ApiController]` attribute, binding source attributes are explicitly defined.</span></span> <span data-ttu-id="8861e-161">Bez `[ApiController]` nebo jiné atributy zdroje vazby, jako je `[FromQuery]`, modul runtime ASP.NET Core se pokusí použít komplexní objekt vazače modelu.</span><span class="sxs-lookup"><span data-stu-id="8861e-161">Without `[ApiController]` or other binding source attributes like `[FromQuery]`, the ASP.NET Core runtime attempts to use the complex object model binder.</span></span> <span data-ttu-id="8861e-162">Vazač modelu komplexní objekt si vyžádá data z zprostředkovatele hodnot (které mají definovanou pořadí).</span><span class="sxs-lookup"><span data-stu-id="8861e-162">The complex object model binder pulls data from value providers (which have a defined order).</span></span> <span data-ttu-id="8861e-163">Například 'body vazač modelu' je vždy vyjádřit výslovný souhlas.</span><span class="sxs-lookup"><span data-stu-id="8861e-163">For instance, the 'body model binder' is always opt in.</span></span>

<span data-ttu-id="8861e-164">V následujícím příkladu `[FromQuery]` atribut označuje, že `discontinuedOnly` je zadána hodnota parametru v řetězci dotazu v adrese URL požadavku:</span><span class="sxs-lookup"><span data-stu-id="8861e-164">In the following example, the `[FromQuery]` attribute indicates that the `discontinuedOnly` parameter value is provided in the request URL's query string:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

<span data-ttu-id="8861e-165">Odvozená pravidla se použijí pro zdroje dat výchozí parametry akce.</span><span class="sxs-lookup"><span data-stu-id="8861e-165">Inference rules are applied for the default data sources of action parameters.</span></span> <span data-ttu-id="8861e-166">Tato pravidla konfigurace zdroje připojení, v opačném případě budete pravděpodobně pro ruční použití na parametry akce.</span><span class="sxs-lookup"><span data-stu-id="8861e-166">These rules configure the binding sources you're otherwise likely to manually apply to the action parameters.</span></span> <span data-ttu-id="8861e-167">Atributy zdroje vazby chovají následovně:</span><span class="sxs-lookup"><span data-stu-id="8861e-167">The binding source attributes behave as follows:</span></span>

* <span data-ttu-id="8861e-168">**[FromBody]**  odvodit pro komplexní typ parametrů.</span><span class="sxs-lookup"><span data-stu-id="8861e-168">**[FromBody]** is inferred for complex type parameters.</span></span> <span data-ttu-id="8861e-169">Výjimkou z tohoto pravidla je libovolný integrované, komplexní typ zvláštní význam, jako například <xref:Microsoft.AspNetCore.Http.IFormCollection> a <xref:System.Threading.CancellationToken>.</span><span class="sxs-lookup"><span data-stu-id="8861e-169">An exception to this rule is any complex, built-in type with a special meaning, such as <xref:Microsoft.AspNetCore.Http.IFormCollection> and <xref:System.Threading.CancellationToken>.</span></span> <span data-ttu-id="8861e-170">Zdrojový kód odvození vazby ignoruje tyto speciální typy.</span><span class="sxs-lookup"><span data-stu-id="8861e-170">The binding source inference code ignores those special types.</span></span> <span data-ttu-id="8861e-171">`[FromBody]` není odvodit pro jednoduché typy, jako například `string` nebo `int`.</span><span class="sxs-lookup"><span data-stu-id="8861e-171">`[FromBody]` isn't inferred for simple types such as `string` or `int`.</span></span> <span data-ttu-id="8861e-172">Proto `[FromBody]` atribut by měl použít pro jednoduché typy, které tuto funkci je potřeba.</span><span class="sxs-lookup"><span data-stu-id="8861e-172">Therefore, the `[FromBody]` attribute should be used for simple types when that functionality is needed.</span></span> <span data-ttu-id="8861e-173">Pokud má akci explicitně zadán více než jeden parametr (prostřednictvím `[FromBody]`) nebo odvozený jako vázaný z textu požadavku, je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="8861e-173">When an action has more than one parameter explicitly specified (via `[FromBody]`) or inferred as bound from the request body, an exception is thrown.</span></span> <span data-ttu-id="8861e-174">Například následující akce podpisy způsobit výjimku:</span><span class="sxs-lookup"><span data-stu-id="8861e-174">For example, the following action signatures cause an exception:</span></span>

    [!code-csharp[](define-controller/samples/WebApiSample.Api.21/Controllers/TestController.cs?name=snippet_ActionsCausingExceptions)]

    > [!NOTE]
    > <span data-ttu-id="8861e-175">V ASP.NET Core 2.1 parametry typu kolekce, jako je například seznamy a pole jsou odvozeny nesprávně jako [[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute).</span><span class="sxs-lookup"><span data-stu-id="8861e-175">In ASP.NET Core 2.1, collection type parameters such as lists and arrays are incorrectly inferred as [[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute).</span></span> <span data-ttu-id="8861e-176">[[FromBody] ](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) byste měli použít pro tyto parametry, pokud mají být vázány z textu požadavku.</span><span class="sxs-lookup"><span data-stu-id="8861e-176">[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) should be used for these parameters if they are to be bound from the request body.</span></span> <span data-ttu-id="8861e-177">Toto chování je pevně v ASP.NET Core 2.2 nebo vyšší, ve kterém jsou parametry typu kolekce odvodit vázat z textu ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="8861e-177">This behavior is fixed in ASP.NET Core 2.2 or later, where collection type parameters are inferred to be bound from the body by default.</span></span>

* <span data-ttu-id="8861e-178">**[FromForm]**  odvodit pro parametry akce typu <xref:Microsoft.AspNetCore.Http.IFormFile> a <xref:Microsoft.AspNetCore.Http.IFormFileCollection>.</span><span class="sxs-lookup"><span data-stu-id="8861e-178">**[FromForm]** is inferred for action parameters of type <xref:Microsoft.AspNetCore.Http.IFormFile> and <xref:Microsoft.AspNetCore.Http.IFormFileCollection>.</span></span> <span data-ttu-id="8861e-179">Není odvodit pro jednoduché nebo uživatelem definované typy.</span><span class="sxs-lookup"><span data-stu-id="8861e-179">It's not inferred for any simple or user-defined types.</span></span>
* <span data-ttu-id="8861e-180">**[FromRoute]**  odvodit pro název parametru žádné akce odpovídající parametr v šabloně trasy.</span><span class="sxs-lookup"><span data-stu-id="8861e-180">**[FromRoute]** is inferred for any action parameter name matching a parameter in the route template.</span></span> <span data-ttu-id="8861e-181">Když víc tras odpovídá parametru akce, je považován za libovolnou hodnotu trasy `[FromRoute]`.</span><span class="sxs-lookup"><span data-stu-id="8861e-181">When more than one route matches an action parameter, any route value is considered `[FromRoute]`.</span></span>
* <span data-ttu-id="8861e-182">**[FromQuery]**  odvodit pro všechny ostatní parametry akce.</span><span class="sxs-lookup"><span data-stu-id="8861e-182">**[FromQuery]** is inferred for any other action parameters.</span></span>

<span data-ttu-id="8861e-183">Výchozí pravidla pro odvození jsou zakázané. Pokud <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> je nastavena na `true`.</span><span class="sxs-lookup"><span data-stu-id="8861e-183">The default inference rules are disabled when the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> property is set to `true`.</span></span> <span data-ttu-id="8861e-184">Přidejte následující kód do `Startup.ConfigureServices` po `services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_<version_number>);`:</span><span class="sxs-lookup"><span data-stu-id="8861e-184">Add the following code in `Startup.ConfigureServices` after `services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_<version_number>);`:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](define-controller/samples/WebApiSample.Api.22/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=6)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=4)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

### <a name="multipartform-data-request-inference"></a><span data-ttu-id="8861e-185">Odvození multipart/formulář data požadavku</span><span class="sxs-lookup"><span data-stu-id="8861e-185">Multipart/form-data request inference</span></span>

<span data-ttu-id="8861e-186">Když parametr akce je opatřen poznámkou [[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) atribut, `multipart/form-data` žádosti je odvozený typ obsahu.</span><span class="sxs-lookup"><span data-stu-id="8861e-186">When an action parameter is annotated with the [[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) attribute, the `multipart/form-data` request content type is inferred.</span></span>

<span data-ttu-id="8861e-187">Výchozí chování je zakázaná. Pokud <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> je nastavena na `true`.</span><span class="sxs-lookup"><span data-stu-id="8861e-187">The default behavior is disabled when the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> property is set to `true`.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="8861e-188">Přidejte následující kód do `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="8861e-188">Add the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.22/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=5)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="8861e-189">Přidejte následující kód do `Startup.ConfigureServices` po `services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);`:</span><span class="sxs-lookup"><span data-stu-id="8861e-189">Add the following code in `Startup.ConfigureServices` after `services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);`:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

### <a name="attribute-routing-requirement"></a><span data-ttu-id="8861e-190">Atribut směrování požadavků</span><span class="sxs-lookup"><span data-stu-id="8861e-190">Attribute routing requirement</span></span>

<span data-ttu-id="8861e-191">Směrování atributů se změní na požadavek.</span><span class="sxs-lookup"><span data-stu-id="8861e-191">Attribute routing becomes a requirement.</span></span> <span data-ttu-id="8861e-192">Příklad:</span><span class="sxs-lookup"><span data-stu-id="8861e-192">For example:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_ControllerSignature&highlight=1)]

<span data-ttu-id="8861e-193">Akce jsou nedostupné přes [trasy konvenční](xref:mvc/controllers/routing#conventional-routing) definované v <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> nebo <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="8861e-193">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined in <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> or by <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> in `Startup.Configure`.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="problem-details-responses-for-error-status-codes"></a><span data-ttu-id="8861e-194">Problém podrobnosti odpovědi pro stavové kódy chyb</span><span class="sxs-lookup"><span data-stu-id="8861e-194">Problem details responses for error status codes</span></span>

<span data-ttu-id="8861e-195">V ASP.NET Core 2.2 nebo vyšší, MVC transformuje chybného výsledku (výsledek s stavový kód 400 nebo vyšší) k výsledku s <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span><span class="sxs-lookup"><span data-stu-id="8861e-195">In ASP.NET Core 2.2 or later, MVC transforms an error result (a result with status code 400 or higher) to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span> <span data-ttu-id="8861e-196">`ProblemDetails` je:</span><span class="sxs-lookup"><span data-stu-id="8861e-196">`ProblemDetails` is:</span></span>

* <span data-ttu-id="8861e-197">Na základě typu [specifikaci RFC 7807](https://tools.ietf.org/html/rfc7807).</span><span class="sxs-lookup"><span data-stu-id="8861e-197">A type based on the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807).</span></span>
* <span data-ttu-id="8861e-198">Standardizovaný formát pro zadávání podrobnosti o chybě Strojově čitelný v odpovědi HTTP.</span><span class="sxs-lookup"><span data-stu-id="8861e-198">A standardized format for specifying machine-readable error details in an HTTP response.</span></span>

<span data-ttu-id="8861e-199">Vezměte v úvahu následující kód do kontroleru akce:</span><span class="sxs-lookup"><span data-stu-id="8861e-199">Consider the following code in a controller action:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.22/Controllers/ProductsController.cs?name=snippet_ProblemDetailsStatusCode)]

<span data-ttu-id="8861e-200">Odpověď HTTP pro `NotFound` má stavový kód 404 s `ProblemDetails` textu.</span><span class="sxs-lookup"><span data-stu-id="8861e-200">The HTTP response for `NotFound` has a 404 status code with a `ProblemDetails` body.</span></span> <span data-ttu-id="8861e-201">Příklad:</span><span class="sxs-lookup"><span data-stu-id="8861e-201">For example:</span></span>

```json
{
    type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
    title: "Not Found",
    status: 404,
    traceId: "0HLHLV31KRN83:00000001"
}
```

<span data-ttu-id="8861e-202">Podrobnosti o problému vyžaduje příznak kompatibility 2.2 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="8861e-202">The problem details feature requires a compatibility flag of 2.2 or later.</span></span> <span data-ttu-id="8861e-203">Výchozí chování je zakázaná. Pokud `SuppressMapClientErrors` je nastavena na `true`.</span><span class="sxs-lookup"><span data-stu-id="8861e-203">The default behavior is disabled when the `SuppressMapClientErrors` property is set to `true`.</span></span> <span data-ttu-id="8861e-204">Přidejte následující kód do `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="8861e-204">Add the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.22/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8)]

<span data-ttu-id="8861e-205">Použití `ClientErrorMapping` vlastnosti ke konfiguraci obsah `ProblemDetails` odpovědi.</span><span class="sxs-lookup"><span data-stu-id="8861e-205">Use the `ClientErrorMapping` property to configure the contents of the `ProblemDetails` response.</span></span> <span data-ttu-id="8861e-206">Například následující kód aktualizace `type` vlastnost obdržíte kód odpovědi 404:</span><span class="sxs-lookup"><span data-stu-id="8861e-206">For example, the following code updates the `type` property for 404 responses:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.22/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=10-11)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="8861e-207">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="8861e-207">Additional resources</span></span>

* <xref:web-api/action-return-types>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
