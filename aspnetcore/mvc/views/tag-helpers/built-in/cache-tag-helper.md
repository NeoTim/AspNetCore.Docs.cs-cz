---
title: Pomocná rutina značek v ASP.NET Core MVC do mezipaměti
author: pkellner
description: Další informace o použití pomocné rutiny značky mezipaměti.
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
uid: mvc/views/tag-helpers/builtin-th/cache-tag-helper
ms.openlocfilehash: 0273a9805dd5db5450f57dcf3fd4d952308df074
ms.sourcegitcommit: 7a40c56bf6a6aaa63a7ee83a2cac9b3a1d77555e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67856215"
---
# <a name="cache-tag-helper-in-aspnet-core-mvc"></a><span data-ttu-id="b9167-103">Pomocná rutina značek v ASP.NET Core MVC do mezipaměti</span><span class="sxs-lookup"><span data-stu-id="b9167-103">Cache Tag Helper in ASP.NET Core MVC</span></span>

<span data-ttu-id="b9167-104">Podle [Peter Kellner](https://peterkellner.net) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b9167-104">By [Peter Kellner](https://peterkellner.net) and [Luke Latham](https://github.com/guardrex)</span></span> 

<span data-ttu-id="b9167-105">Pomocná rutina značek mezipaměti umožňuje zlepšit výkon vaší aplikace ASP.NET Core pomocí ukládání do mezipaměti obsah k poskytovateli vnitřní mezipaměti ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b9167-105">The Cache Tag Helper provides the ability to improve the performance of your ASP.NET Core app by caching its content to the internal ASP.NET Core cache provider.</span></span>

<span data-ttu-id="b9167-106">Přehled pomocných rutin značek, naleznete v tématu <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="b9167-106">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="b9167-107">Následující kód Razor ukládá do mezipaměti na aktuální datum:</span><span class="sxs-lookup"><span data-stu-id="b9167-107">The following Razor markup caches the current date:</span></span>

```cshtml
<cache>@DateTime.Now</cache>
```

<span data-ttu-id="b9167-108">První požadavek na stránku, který obsahuje pomocné rutiny značky zobrazuje aktuální datum.</span><span class="sxs-lookup"><span data-stu-id="b9167-108">The first request to the page that contains the Tag Helper displays the current date.</span></span> <span data-ttu-id="b9167-109">Další požadavky zobrazit hodnotu uloženou v mezipaměti, dokud mezipaměti vyprší (výchozí nastavení 20 minut) nebo data uložená v mezipaměti je odstraněn z mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="b9167-109">Additional requests show the cached value until the cache expires (default 20 minutes) or until the cached date is evicted from the cache.</span></span>

## <a name="cache-tag-helper-attributes"></a><span data-ttu-id="b9167-110">Atributy pomocné rutiny značky do mezipaměti</span><span class="sxs-lookup"><span data-stu-id="b9167-110">Cache Tag Helper Attributes</span></span>

### <a name="enabled"></a><span data-ttu-id="b9167-111">enabled</span><span class="sxs-lookup"><span data-stu-id="b9167-111">enabled</span></span>

| <span data-ttu-id="b9167-112">Typ atributu</span><span class="sxs-lookup"><span data-stu-id="b9167-112">Attribute Type</span></span>  | <span data-ttu-id="b9167-113">Příklady</span><span class="sxs-lookup"><span data-stu-id="b9167-113">Examples</span></span>        | <span data-ttu-id="b9167-114">Výchozí</span><span class="sxs-lookup"><span data-stu-id="b9167-114">Default</span></span> |
| --------------- | --------------- | ------- |
| <span data-ttu-id="b9167-115">Boolean</span><span class="sxs-lookup"><span data-stu-id="b9167-115">Boolean</span></span>         | <span data-ttu-id="b9167-116">`true`, `false`</span><span class="sxs-lookup"><span data-stu-id="b9167-116">`true`, `false`</span></span> | `true`  |

<span data-ttu-id="b9167-117">`enabled` Určuje, pokud obsah uzavřená v pomocné rutiny značky mezipaměti se ukládá do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="b9167-117">`enabled` determines if the content enclosed by the Cache Tag Helper is cached.</span></span> <span data-ttu-id="b9167-118">Výchozí hodnota je `true`.</span><span class="sxs-lookup"><span data-stu-id="b9167-118">The default is `true`.</span></span> <span data-ttu-id="b9167-119">Pokud hodnotu `false`, vykresleného výstupu je **není** uložené v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="b9167-119">If set to `false`, the rendered output is **not** cached.</span></span>

<span data-ttu-id="b9167-120">Příklad:</span><span class="sxs-lookup"><span data-stu-id="b9167-120">Example:</span></span>

```cshtml
<cache enabled="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-on"></a><span data-ttu-id="b9167-121">expires-on</span><span class="sxs-lookup"><span data-stu-id="b9167-121">expires-on</span></span>

| <span data-ttu-id="b9167-122">Typ atributu</span><span class="sxs-lookup"><span data-stu-id="b9167-122">Attribute Type</span></span>   | <span data-ttu-id="b9167-123">Příklad</span><span class="sxs-lookup"><span data-stu-id="b9167-123">Example</span></span>                            |
| ---------------- | ---------------------------------- |
| `DateTimeOffset` | `@new DateTime(2025,1,29,17,02,0)` |

<span data-ttu-id="b9167-124">`expires-on` Nastaví data absolutní vypršení platnosti položky v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="b9167-124">`expires-on` sets an absolute expiration date for the cached item.</span></span>

<span data-ttu-id="b9167-125">Následující příklad ukládá do mezipaměti obsah pomocné rutiny značky mezipaměti až do 17:02:00 na 29. ledna 2025:</span><span class="sxs-lookup"><span data-stu-id="b9167-125">The following example caches the contents of the Cache Tag Helper until 5:02 PM on January 29, 2025:</span></span>

```cshtml
<cache expires-on="@new DateTime(2025,1,29,17,02,0)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-after"></a><span data-ttu-id="b9167-126">expires-after</span><span class="sxs-lookup"><span data-stu-id="b9167-126">expires-after</span></span>

| <span data-ttu-id="b9167-127">Typ atributu</span><span class="sxs-lookup"><span data-stu-id="b9167-127">Attribute Type</span></span> | <span data-ttu-id="b9167-128">Příklad</span><span class="sxs-lookup"><span data-stu-id="b9167-128">Example</span></span>                      | <span data-ttu-id="b9167-129">Výchozí</span><span class="sxs-lookup"><span data-stu-id="b9167-129">Default</span></span>    |
| -------------- | ---------------------------- | ---------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(120)` | <span data-ttu-id="b9167-130">20 minut</span><span class="sxs-lookup"><span data-stu-id="b9167-130">20 minutes</span></span> |

<span data-ttu-id="b9167-131">`expires-after` Nastaví dobu od prvního požadavku pro ukládání do mezipaměti obsah.</span><span class="sxs-lookup"><span data-stu-id="b9167-131">`expires-after` sets the length of time from the first request time to cache the contents.</span></span>

<span data-ttu-id="b9167-132">Příklad:</span><span class="sxs-lookup"><span data-stu-id="b9167-132">Example:</span></span>

```cshtml
<cache expires-after="@TimeSpan.FromSeconds(120)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="b9167-133">Nastaví výchozí zobrazovací modul Razor `expires-after` hodnota 20 minut.</span><span class="sxs-lookup"><span data-stu-id="b9167-133">The Razor View Engine sets the default `expires-after` value to twenty minutes.</span></span>

### <a name="expires-sliding"></a><span data-ttu-id="b9167-134">expires-sliding</span><span class="sxs-lookup"><span data-stu-id="b9167-134">expires-sliding</span></span>

| <span data-ttu-id="b9167-135">Typ atributu</span><span class="sxs-lookup"><span data-stu-id="b9167-135">Attribute Type</span></span> | <span data-ttu-id="b9167-136">Příklad</span><span class="sxs-lookup"><span data-stu-id="b9167-136">Example</span></span>                     |
| -------------- | --------------------------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(60)` |

<span data-ttu-id="b9167-137">Nastaví čas, který se položka mezipaměti by měla být vyřazena, pokud její hodnota nepřistupovalo.</span><span class="sxs-lookup"><span data-stu-id="b9167-137">Sets the time that a cache entry should be evicted if its value hasn't been accessed.</span></span>

<span data-ttu-id="b9167-138">Příklad:</span><span class="sxs-lookup"><span data-stu-id="b9167-138">Example:</span></span>

```cshtml
<cache expires-sliding="@TimeSpan.FromSeconds(60)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-header"></a><span data-ttu-id="b9167-139">měnit podle hlavičky</span><span class="sxs-lookup"><span data-stu-id="b9167-139">vary-by-header</span></span>

| <span data-ttu-id="b9167-140">Typ atributu</span><span class="sxs-lookup"><span data-stu-id="b9167-140">Attribute Type</span></span> | <span data-ttu-id="b9167-141">Příklady</span><span class="sxs-lookup"><span data-stu-id="b9167-141">Examples</span></span>                                    |
| -------------- | ------------------------------------------- |
| <span data-ttu-id="b9167-142">String</span><span class="sxs-lookup"><span data-stu-id="b9167-142">String</span></span>         | <span data-ttu-id="b9167-143">`User-Agent`, `User-Agent,content-encoding`</span><span class="sxs-lookup"><span data-stu-id="b9167-143">`User-Agent`, `User-Agent,content-encoding`</span></span> |

<span data-ttu-id="b9167-144">`vary-by-header` přijímá čárkami oddělený seznam hodnot hlavičky, které aktivují aktualizace mezipaměti při změnách.</span><span class="sxs-lookup"><span data-stu-id="b9167-144">`vary-by-header` accepts a comma-delimited list of header values that trigger a cache refresh when they change.</span></span>

<span data-ttu-id="b9167-145">Následující příklad monitoruje hodnota hlavičky `User-Agent`.</span><span class="sxs-lookup"><span data-stu-id="b9167-145">The following example monitors the header value `User-Agent`.</span></span> <span data-ttu-id="b9167-146">V příkladu ukládá do mezipaměti obsah pro každé jiné `User-Agent` uvedené na webový server:</span><span class="sxs-lookup"><span data-stu-id="b9167-146">The example caches the content for every different `User-Agent` presented to the web server:</span></span>

```cshtml
<cache vary-by-header="User-Agent">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-query"></a><span data-ttu-id="b9167-147">se liší podle dotazu</span><span class="sxs-lookup"><span data-stu-id="b9167-147">vary-by-query</span></span>

| <span data-ttu-id="b9167-148">Typ atributu</span><span class="sxs-lookup"><span data-stu-id="b9167-148">Attribute Type</span></span> | <span data-ttu-id="b9167-149">Příklady</span><span class="sxs-lookup"><span data-stu-id="b9167-149">Examples</span></span>             |
| -------------- | -------------------- |
| <span data-ttu-id="b9167-150">String</span><span class="sxs-lookup"><span data-stu-id="b9167-150">String</span></span>         | <span data-ttu-id="b9167-151">`Make`, `Make,Model`</span><span class="sxs-lookup"><span data-stu-id="b9167-151">`Make`, `Make,Model`</span></span> |

<span data-ttu-id="b9167-152">`vary-by-query` přijímá čárkami oddělený seznam <xref:Microsoft.AspNetCore.Http.IQueryCollection.Keys*> v řetězci dotazu (<xref:Microsoft.AspNetCore.Http.HttpRequest.Query*>), který aktivuje aktualizace mezipaměti, když zadejte hodnotu kterékoli uvedené změny klíče.</span><span class="sxs-lookup"><span data-stu-id="b9167-152">`vary-by-query` accepts a comma-delimited list of <xref:Microsoft.AspNetCore.Http.IQueryCollection.Keys*> in a query string (<xref:Microsoft.AspNetCore.Http.HttpRequest.Query*>) that trigger a cache refresh when the value of any listed key changes.</span></span>

<span data-ttu-id="b9167-153">Následující příklad monitoruje hodnoty `Make` a `Model`.</span><span class="sxs-lookup"><span data-stu-id="b9167-153">The following example monitors the values of `Make` and `Model`.</span></span> <span data-ttu-id="b9167-154">V příkladu ukládá do mezipaměti obsah pro každé jiné `Make` a `Model` uvedené na webový server:</span><span class="sxs-lookup"><span data-stu-id="b9167-154">The example caches the content for every different `Make` and `Model` presented to the web server:</span></span>

```cshtml
<cache vary-by-query="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-route"></a><span data-ttu-id="b9167-155">se liší podle postupu</span><span class="sxs-lookup"><span data-stu-id="b9167-155">vary-by-route</span></span>

| <span data-ttu-id="b9167-156">Typ atributu</span><span class="sxs-lookup"><span data-stu-id="b9167-156">Attribute Type</span></span> | <span data-ttu-id="b9167-157">Příklady</span><span class="sxs-lookup"><span data-stu-id="b9167-157">Examples</span></span>             |
| -------------- | -------------------- |
| <span data-ttu-id="b9167-158">String</span><span class="sxs-lookup"><span data-stu-id="b9167-158">String</span></span>         | <span data-ttu-id="b9167-159">`Make`, `Make,Model`</span><span class="sxs-lookup"><span data-stu-id="b9167-159">`Make`, `Make,Model`</span></span> |

<span data-ttu-id="b9167-160">`vary-by-route` přijímá čárkami oddělený seznam názvů parametr trasy, které aktivovat aktualizaci mezipaměti při změně hodnoty parametru dat trasy.</span><span class="sxs-lookup"><span data-stu-id="b9167-160">`vary-by-route` accepts a comma-delimited list of route parameter names that trigger a cache refresh when the route data parameter value changes.</span></span>

<span data-ttu-id="b9167-161">Příklad:</span><span class="sxs-lookup"><span data-stu-id="b9167-161">Example:</span></span>

<span data-ttu-id="b9167-162">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="b9167-162">*Startup.cs*:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{Make?}/{Model?}");
```

<span data-ttu-id="b9167-163">*Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="b9167-163">*Index.cshtml*:</span></span>

```cshtml
<cache vary-by-route="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-cookie"></a><span data-ttu-id="b9167-164">se liší podle cookie</span><span class="sxs-lookup"><span data-stu-id="b9167-164">vary-by-cookie</span></span>

| <span data-ttu-id="b9167-165">Typ atributu</span><span class="sxs-lookup"><span data-stu-id="b9167-165">Attribute Type</span></span> | <span data-ttu-id="b9167-166">Příklady</span><span class="sxs-lookup"><span data-stu-id="b9167-166">Examples</span></span>                                                                         |
| -------------- | -------------------------------------------------------------------------------- |
| <span data-ttu-id="b9167-167">String</span><span class="sxs-lookup"><span data-stu-id="b9167-167">String</span></span>         | <span data-ttu-id="b9167-168">`.AspNetCore.Identity.Application`, `.AspNetCore.Identity.Application,HairColor`</span><span class="sxs-lookup"><span data-stu-id="b9167-168">`.AspNetCore.Identity.Application`, `.AspNetCore.Identity.Application,HairColor`</span></span> |

<span data-ttu-id="b9167-169">`vary-by-cookie` přijímá čárkami oddělený seznam názvů souborů cookie, které aktivují aktualizace mezipaměti, pokud se změní hodnoty souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="b9167-169">`vary-by-cookie` accepts a comma-delimited list of cookie names that trigger a cache refresh when the cookie values change.</span></span>

<span data-ttu-id="b9167-170">Následující příklad monitoruje souboru cookie přidruženého k ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="b9167-170">The following example monitors the cookie associated with ASP.NET Core Identity.</span></span> <span data-ttu-id="b9167-171">Když je uživatel ověřen, změny v souboru cookie Identity aktivuje aktualizace mezipaměti:</span><span class="sxs-lookup"><span data-stu-id="b9167-171">When a user is authenticated, a change in the Identity cookie triggers a cache refresh:</span></span>

```cshtml
<cache vary-by-cookie=".AspNetCore.Identity.Application">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-user"></a><span data-ttu-id="b9167-172">se liší podle uživatele</span><span class="sxs-lookup"><span data-stu-id="b9167-172">vary-by-user</span></span>

| <span data-ttu-id="b9167-173">Typ atributu</span><span class="sxs-lookup"><span data-stu-id="b9167-173">Attribute Type</span></span>  | <span data-ttu-id="b9167-174">Příklady</span><span class="sxs-lookup"><span data-stu-id="b9167-174">Examples</span></span>        | <span data-ttu-id="b9167-175">Výchozí</span><span class="sxs-lookup"><span data-stu-id="b9167-175">Default</span></span> |
| --------------- | --------------- | ------- |
| <span data-ttu-id="b9167-176">Boolean</span><span class="sxs-lookup"><span data-stu-id="b9167-176">Boolean</span></span>         | <span data-ttu-id="b9167-177">`true`, `false`</span><span class="sxs-lookup"><span data-stu-id="b9167-177">`true`, `false`</span></span> | `true`  |

<span data-ttu-id="b9167-178">`vary-by-user` Určuje, zda mezipaměti resetuje při změně přihlášeného uživatele (nebo objekt kontextu zabezpečení).</span><span class="sxs-lookup"><span data-stu-id="b9167-178">`vary-by-user` specifies whether or not the cache resets when the signed-in user (or Context Principal) changes.</span></span> <span data-ttu-id="b9167-179">Aktuální uživatel se také označuje jako instanční objekt kontextu požadavku a lze je zobrazit v zobrazení Razor odkazem `@User.Identity.Name`.</span><span class="sxs-lookup"><span data-stu-id="b9167-179">The current user is also known as the Request Context Principal and can be viewed in a Razor view by referencing `@User.Identity.Name`.</span></span>

<span data-ttu-id="b9167-180">Následující příklad monitoruje aktuálně přihlášeného uživatele k aktivaci aktualizace mezipaměti:</span><span class="sxs-lookup"><span data-stu-id="b9167-180">The following example monitors the current logged in user to trigger a cache refresh:</span></span>

```cshtml
<cache vary-by-user="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="b9167-181">Pomocí tohoto atributu udržuje cyklu obsah v mezipaměti, prostřednictvím u přihlášení a odhlášení.</span><span class="sxs-lookup"><span data-stu-id="b9167-181">Using this attribute maintains the contents in cache through a sign-in and sign-out cycle.</span></span> <span data-ttu-id="b9167-182">Pokud je hodnota nastavená na `true`, ověřování cyklu zruší platnost mezipaměti pro ověřeného uživatele.</span><span class="sxs-lookup"><span data-stu-id="b9167-182">When the value is set to `true`, an authentication cycle invalidates the cache for the authenticated user.</span></span> <span data-ttu-id="b9167-183">Mezipaměť je neplatná, protože nová hodnota jedinečný soubor cookie se vygeneruje, když je ověření uživatele.</span><span class="sxs-lookup"><span data-stu-id="b9167-183">The cache is invalidated because a new unique cookie value is generated when a user is authenticated.</span></span> <span data-ttu-id="b9167-184">Mezipaměť je zachován z důvodu anonymní stavu Pokud je k dispozici žádný soubor cookie nebo vypršela platnost souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="b9167-184">Cache is maintained for the anonymous state when no cookie is present or the cookie has expired.</span></span> <span data-ttu-id="b9167-185">Pokud je uživatel **není** ověření do mezipaměti se zachová.</span><span class="sxs-lookup"><span data-stu-id="b9167-185">If the user is **not** authenticated, the cache is maintained.</span></span>

### <a name="vary-by"></a><span data-ttu-id="b9167-186">se liší podle</span><span class="sxs-lookup"><span data-stu-id="b9167-186">vary-by</span></span>

| <span data-ttu-id="b9167-187">Typ atributu</span><span class="sxs-lookup"><span data-stu-id="b9167-187">Attribute Type</span></span> | <span data-ttu-id="b9167-188">Příklad</span><span class="sxs-lookup"><span data-stu-id="b9167-188">Example</span></span>  |
| -------------- | -------- |
| <span data-ttu-id="b9167-189">String</span><span class="sxs-lookup"><span data-stu-id="b9167-189">String</span></span>         | `@Model` |

<span data-ttu-id="b9167-190">`vary-by` umožňuje přizpůsobení jaká data se uloží do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="b9167-190">`vary-by` allows for customization of what data is cached.</span></span> <span data-ttu-id="b9167-191">Při aktualizaci objektu, odkazuje atribut řetězec hodnotu změny, obsah pomocné rutiny značky mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="b9167-191">When the object referenced by the attribute's string value changes, the content of the Cache Tag Helper is updated.</span></span> <span data-ttu-id="b9167-192">Často zřetězení hodnoty modelu jsou přiřazeny k tomuto atributu.</span><span class="sxs-lookup"><span data-stu-id="b9167-192">Often, a string-concatenation of model values are assigned to this attribute.</span></span> <span data-ttu-id="b9167-193">Efektivně výsledkem scénář, ve kterém aktualizaci zřetězených hodnot zruší platnost mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="b9167-193">Effectively, this results in a scenario where an update to any of the concatenated values invalidates the cache.</span></span>

<span data-ttu-id="b9167-194">V následujícím příkladu se předpokládá metoda kontroleru vykreslení zobrazení součtů celočíselnou hodnotu dva parametry trasy `myParam1` a `myParam2`a vrátí součet jako vlastnost jednoho modelu.</span><span class="sxs-lookup"><span data-stu-id="b9167-194">The following example assumes the controller method rendering the view sums the integer value of the two route parameters, `myParam1` and `myParam2`, and returns the sum as the single model property.</span></span> <span data-ttu-id="b9167-195">Při změně tohoto součtu obsah pomocné rutiny značky mezipaměti je vykreslen a uložili do mezipaměti znovu.</span><span class="sxs-lookup"><span data-stu-id="b9167-195">When this sum changes, the content of the Cache Tag Helper is rendered and cached again.</span></span>  

<span data-ttu-id="b9167-196">Akce:</span><span class="sxs-lookup"><span data-stu-id="b9167-196">Action:</span></span>

```csharp
public IActionResult Index(string myParam1, string myParam2, string myParam3)
{
    int num1;
    int num2;
    int.TryParse(myParam1, out num1);
    int.TryParse(myParam2, out num2);
    return View(viewName, num1 + num2);
}
```

<span data-ttu-id="b9167-197">*Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="b9167-197">*Index.cshtml*:</span></span>

```cshtml
<cache vary-by="@Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="priority"></a><span data-ttu-id="b9167-198">priorita</span><span class="sxs-lookup"><span data-stu-id="b9167-198">priority</span></span>

| <span data-ttu-id="b9167-199">Typ atributu</span><span class="sxs-lookup"><span data-stu-id="b9167-199">Attribute Type</span></span>      | <span data-ttu-id="b9167-200">Příklady</span><span class="sxs-lookup"><span data-stu-id="b9167-200">Examples</span></span>                               | <span data-ttu-id="b9167-201">Výchozí</span><span class="sxs-lookup"><span data-stu-id="b9167-201">Default</span></span>  |
| ------------------- | -------------------------------------- | -------- |
| `CacheItemPriority` | <span data-ttu-id="b9167-202">`High`, `Low`, `NeverRemove`, `Normal`</span><span class="sxs-lookup"><span data-stu-id="b9167-202">`High`, `Low`, `NeverRemove`, `Normal`</span></span> | `Normal` |

<span data-ttu-id="b9167-203">`priority` Obsahuje pokyny k vyřazení mezipaměti integrovanou mezipaměť na poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="b9167-203">`priority` provides cache eviction guidance to the built-in cache provider.</span></span> <span data-ttu-id="b9167-204">Webový server vyloučí `Low` nejprve mezipaměti položky, když je přetížena paměť.</span><span class="sxs-lookup"><span data-stu-id="b9167-204">The web server evicts `Low` cache entries first when it's under memory pressure.</span></span>

<span data-ttu-id="b9167-205">Příklad:</span><span class="sxs-lookup"><span data-stu-id="b9167-205">Example:</span></span>

```cshtml
<cache priority="High">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="b9167-206">`priority` Atribut nezaručuje konkrétní úroveň mezipaměti dobu uchování.</span><span class="sxs-lookup"><span data-stu-id="b9167-206">The `priority` attribute doesn't guarantee a specific level of cache retention.</span></span> <span data-ttu-id="b9167-207">`CacheItemPriority` je jenom návrh.</span><span class="sxs-lookup"><span data-stu-id="b9167-207">`CacheItemPriority` is only a suggestion.</span></span> <span data-ttu-id="b9167-208">Nastavení tohoto atributu na `NeverRemove` nezaručuje, že se vždy zachovají položek v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="b9167-208">Setting this attribute to `NeverRemove` doesn't guarantee that cached items are always retained.</span></span> <span data-ttu-id="b9167-209">Najdete v tématech [další prostředky](#additional-resources) části Další informace.</span><span class="sxs-lookup"><span data-stu-id="b9167-209">See the topics in the [Additional Resources](#additional-resources) section for more information.</span></span>

<span data-ttu-id="b9167-210">Pomocná rutina značek mezipaměti je závislá na [služby mezipaměti paměti](xref:performance/caching/memory).</span><span class="sxs-lookup"><span data-stu-id="b9167-210">The Cache Tag Helper is dependent on the [memory cache service](xref:performance/caching/memory).</span></span> <span data-ttu-id="b9167-211">Pomocná rutina značek mezipaměti přidá službu, pokud nebyla přidána.</span><span class="sxs-lookup"><span data-stu-id="b9167-211">The Cache Tag Helper adds the service if it hasn't been added.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b9167-212">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="b9167-212">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
