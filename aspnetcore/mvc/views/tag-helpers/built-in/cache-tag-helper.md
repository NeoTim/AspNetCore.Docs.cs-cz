---
title: Pomocná značka značky v ASP.NET Core MVC
author: pkellner
description: Naučte se používat pomocníka značky mezipaměti.
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/cache-tag-helper
ms.openlocfilehash: 65d8bbcdaed76a308b924ba024219e8f520bb585
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399280"
---
# <a name="cache-tag-helper-in-aspnet-core-mvc"></a><span data-ttu-id="97964-103">Pomocná značka značky v ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="97964-103">Cache Tag Helper in ASP.NET Core MVC</span></span>

<span data-ttu-id="97964-104">Od [Petra Kellner](https://peterkellner.net)</span><span class="sxs-lookup"><span data-stu-id="97964-104">By [Peter Kellner](https://peterkellner.net)</span></span>

<span data-ttu-id="97964-105">Pomocník značek cache poskytuje možnost zlepšit výkon aplikace ASP.NET Core ukládáním obsahu do mezipaměti interního poskytovatele mezipaměti ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="97964-105">The Cache Tag Helper provides the ability to improve the performance of your ASP.NET Core app by caching its content to the internal ASP.NET Core cache provider.</span></span>

<span data-ttu-id="97964-106">Přehled pomocníků značek naleznete v tématu <xref:mvc/views/tag-helpers/intro> .</span><span class="sxs-lookup"><span data-stu-id="97964-106">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="97964-107">Následující Razor kód ukládá do mezipaměti aktuální datum:</span><span class="sxs-lookup"><span data-stu-id="97964-107">The following Razor markup caches the current date:</span></span>

```cshtml
<cache>@DateTime.Now</cache>
```

<span data-ttu-id="97964-108">První požadavek na stránku, která obsahuje pomocníka značek, zobrazí aktuální datum.</span><span class="sxs-lookup"><span data-stu-id="97964-108">The first request to the page that contains the Tag Helper displays the current date.</span></span> <span data-ttu-id="97964-109">Další požadavky zobrazí hodnotu uloženou v mezipaměti, dokud nevyprší platnost mezipaměti (výchozí 20 minut) nebo dokud nebude datum v mezipaměti vyřazeno z mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="97964-109">Additional requests show the cached value until the cache expires (default 20 minutes) or until the cached date is evicted from the cache.</span></span>

## <a name="cache-tag-helper-attributes"></a><span data-ttu-id="97964-110">Pomocné atributy značek mezipaměti</span><span class="sxs-lookup"><span data-stu-id="97964-110">Cache Tag Helper Attributes</span></span>

### <a name="enabled"></a><span data-ttu-id="97964-111">enabled</span><span class="sxs-lookup"><span data-stu-id="97964-111">enabled</span></span>

| <span data-ttu-id="97964-112">Typ atributu</span><span class="sxs-lookup"><span data-stu-id="97964-112">Attribute Type</span></span>  | <span data-ttu-id="97964-113">Příklady</span><span class="sxs-lookup"><span data-stu-id="97964-113">Examples</span></span>        | <span data-ttu-id="97964-114">Výchozí</span><span class="sxs-lookup"><span data-stu-id="97964-114">Default</span></span> |
| --------------- | --------------- | ------- |
| <span data-ttu-id="97964-115">Logická hodnota</span><span class="sxs-lookup"><span data-stu-id="97964-115">Boolean</span></span>         | <span data-ttu-id="97964-116">`true`, `false`</span><span class="sxs-lookup"><span data-stu-id="97964-116">`true`, `false`</span></span> | `true`  |

<span data-ttu-id="97964-117">`enabled`Určuje, zda je obsah uzavřený pomocníkem značky mezipaměti uložen do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="97964-117">`enabled` determines if the content enclosed by the Cache Tag Helper is cached.</span></span> <span data-ttu-id="97964-118">Výchozí formát je `true`.</span><span class="sxs-lookup"><span data-stu-id="97964-118">The default is `true`.</span></span> <span data-ttu-id="97964-119">Pokud je nastaveno na `false` , Vykreslený výstup **není** uložen v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="97964-119">If set to `false`, the rendered output is **not** cached.</span></span>

<span data-ttu-id="97964-120">Příklad:</span><span class="sxs-lookup"><span data-stu-id="97964-120">Example:</span></span>

```cshtml
<cache enabled="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-on"></a><span data-ttu-id="97964-121">vyprší – dne</span><span class="sxs-lookup"><span data-stu-id="97964-121">expires-on</span></span>

| <span data-ttu-id="97964-122">Typ atributu</span><span class="sxs-lookup"><span data-stu-id="97964-122">Attribute Type</span></span>   | <span data-ttu-id="97964-123">Příklad</span><span class="sxs-lookup"><span data-stu-id="97964-123">Example</span></span>                            |
| ---------------- | ---------------------------------- |
| `DateTimeOffset` | `@new DateTime(2025,1,29,17,02,0)` |

<span data-ttu-id="97964-124">`expires-on`Nastaví absolutní datum vypršení platnosti pro položku uloženou v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="97964-124">`expires-on` sets an absolute expiration date for the cached item.</span></span>

<span data-ttu-id="97964-125">Následující příklad ukládá do mezipaměti obsah pomocné rutiny značky cache až 5:02 PM, který je 29. ledna 2025:</span><span class="sxs-lookup"><span data-stu-id="97964-125">The following example caches the contents of the Cache Tag Helper until 5:02 PM on January 29, 2025:</span></span>

```cshtml
<cache expires-on="@new DateTime(2025,1,29,17,02,0)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-after"></a><span data-ttu-id="97964-126">platnost vyprší – po</span><span class="sxs-lookup"><span data-stu-id="97964-126">expires-after</span></span>

| <span data-ttu-id="97964-127">Typ atributu</span><span class="sxs-lookup"><span data-stu-id="97964-127">Attribute Type</span></span> | <span data-ttu-id="97964-128">Příklad</span><span class="sxs-lookup"><span data-stu-id="97964-128">Example</span></span>                      | <span data-ttu-id="97964-129">Výchozí</span><span class="sxs-lookup"><span data-stu-id="97964-129">Default</span></span>    |
| -------------- | ---------------------------- | ---------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(120)` | <span data-ttu-id="97964-130">20 minut</span><span class="sxs-lookup"><span data-stu-id="97964-130">20 minutes</span></span> |

<span data-ttu-id="97964-131">`expires-after`nastaví dobu od první žádosti o dobu ukládání obsahu do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="97964-131">`expires-after` sets the length of time from the first request time to cache the contents.</span></span>

<span data-ttu-id="97964-132">Příklad:</span><span class="sxs-lookup"><span data-stu-id="97964-132">Example:</span></span>

```cshtml
<cache expires-after="@TimeSpan.FromSeconds(120)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="97964-133">RazorModul zobrazení nastaví výchozí `expires-after` hodnotu na 20 minut.</span><span class="sxs-lookup"><span data-stu-id="97964-133">The Razor View Engine sets the default `expires-after` value to twenty minutes.</span></span>

### <a name="expires-sliding"></a><span data-ttu-id="97964-134">konec platnosti – klouzavé</span><span class="sxs-lookup"><span data-stu-id="97964-134">expires-sliding</span></span>

| <span data-ttu-id="97964-135">Typ atributu</span><span class="sxs-lookup"><span data-stu-id="97964-135">Attribute Type</span></span> | <span data-ttu-id="97964-136">Příklad</span><span class="sxs-lookup"><span data-stu-id="97964-136">Example</span></span>                     |
| -------------- | --------------------------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(60)` |

<span data-ttu-id="97964-137">Nastaví čas, kdy by měla být položka mezipaměti vyřazena, pokud k její hodnotě nedojde.</span><span class="sxs-lookup"><span data-stu-id="97964-137">Sets the time that a cache entry should be evicted if its value hasn't been accessed.</span></span>

<span data-ttu-id="97964-138">Příklad:</span><span class="sxs-lookup"><span data-stu-id="97964-138">Example:</span></span>

```cshtml
<cache expires-sliding="@TimeSpan.FromSeconds(60)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-header"></a><span data-ttu-id="97964-139">různé – podle záhlaví</span><span class="sxs-lookup"><span data-stu-id="97964-139">vary-by-header</span></span>

| <span data-ttu-id="97964-140">Typ atributu</span><span class="sxs-lookup"><span data-stu-id="97964-140">Attribute Type</span></span> | <span data-ttu-id="97964-141">Příklady</span><span class="sxs-lookup"><span data-stu-id="97964-141">Examples</span></span>                                    |
| -------------- | ------------------------------------------- |
| <span data-ttu-id="97964-142">Řetězec</span><span class="sxs-lookup"><span data-stu-id="97964-142">String</span></span>         | <span data-ttu-id="97964-143">`User-Agent`, `User-Agent,content-encoding`</span><span class="sxs-lookup"><span data-stu-id="97964-143">`User-Agent`, `User-Agent,content-encoding`</span></span> |

<span data-ttu-id="97964-144">`vary-by-header`přijímá seznam hodnot hlaviček oddělených čárkami, které aktivují aktualizaci mezipaměti, když se změní.</span><span class="sxs-lookup"><span data-stu-id="97964-144">`vary-by-header` accepts a comma-delimited list of header values that trigger a cache refresh when they change.</span></span>

<span data-ttu-id="97964-145">V následujícím příkladu je sledována hodnota hlavičky `User-Agent` .</span><span class="sxs-lookup"><span data-stu-id="97964-145">The following example monitors the header value `User-Agent`.</span></span> <span data-ttu-id="97964-146">Příklad ukládá obsah do mezipaměti pro všechny různé `User-Agent` prezentované webovému serveru:</span><span class="sxs-lookup"><span data-stu-id="97964-146">The example caches the content for every different `User-Agent` presented to the web server:</span></span>

```cshtml
<cache vary-by-header="User-Agent">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-query"></a><span data-ttu-id="97964-147">v závislosti na dotazu</span><span class="sxs-lookup"><span data-stu-id="97964-147">vary-by-query</span></span>

| <span data-ttu-id="97964-148">Typ atributu</span><span class="sxs-lookup"><span data-stu-id="97964-148">Attribute Type</span></span> | <span data-ttu-id="97964-149">Příklady</span><span class="sxs-lookup"><span data-stu-id="97964-149">Examples</span></span>             |
| -------------- | -------------------- |
| <span data-ttu-id="97964-150">Řetězec</span><span class="sxs-lookup"><span data-stu-id="97964-150">String</span></span>         | <span data-ttu-id="97964-151">`Make`, `Make,Model`</span><span class="sxs-lookup"><span data-stu-id="97964-151">`Make`, `Make,Model`</span></span> |

<span data-ttu-id="97964-152">`vary-by-query`přijme <xref:Microsoft.AspNetCore.Http.IQueryCollection.Keys*> v řetězci dotazu () seznam oddělený čárkami ( <xref:Microsoft.AspNetCore.Http.HttpRequest.Query*> ), který aktivuje aktualizaci mezipaměti, když hodnota jakékoli v uvedených klíčových změnách.</span><span class="sxs-lookup"><span data-stu-id="97964-152">`vary-by-query` accepts a comma-delimited list of <xref:Microsoft.AspNetCore.Http.IQueryCollection.Keys*> in a query string (<xref:Microsoft.AspNetCore.Http.HttpRequest.Query*>) that trigger a cache refresh when the value of any listed key changes.</span></span>

<span data-ttu-id="97964-153">Následující příklad sleduje hodnoty `Make` a `Model` .</span><span class="sxs-lookup"><span data-stu-id="97964-153">The following example monitors the values of `Make` and `Model`.</span></span> <span data-ttu-id="97964-154">Tento příklad ukládá obsah do mezipaměti pro všechny různé `Make` a `Model` prezentované webovému serveru:</span><span class="sxs-lookup"><span data-stu-id="97964-154">The example caches the content for every different `Make` and `Model` presented to the web server:</span></span>

```cshtml
<cache vary-by-query="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-route"></a><span data-ttu-id="97964-155">různé – podle směrování</span><span class="sxs-lookup"><span data-stu-id="97964-155">vary-by-route</span></span>

| <span data-ttu-id="97964-156">Typ atributu</span><span class="sxs-lookup"><span data-stu-id="97964-156">Attribute Type</span></span> | <span data-ttu-id="97964-157">Příklady</span><span class="sxs-lookup"><span data-stu-id="97964-157">Examples</span></span>             |
| -------------- | -------------------- |
| <span data-ttu-id="97964-158">Řetězec</span><span class="sxs-lookup"><span data-stu-id="97964-158">String</span></span>         | <span data-ttu-id="97964-159">`Make`, `Make,Model`</span><span class="sxs-lookup"><span data-stu-id="97964-159">`Make`, `Make,Model`</span></span> |

<span data-ttu-id="97964-160">`vary-by-route`přijme seznam názvů parametrů trasy oddělených čárkami, které aktivují aktualizaci mezipaměti při změně hodnoty parametru dat trasy.</span><span class="sxs-lookup"><span data-stu-id="97964-160">`vary-by-route` accepts a comma-delimited list of route parameter names that trigger a cache refresh when the route data parameter value changes.</span></span>

<span data-ttu-id="97964-161">Příklad:</span><span class="sxs-lookup"><span data-stu-id="97964-161">Example:</span></span>

<span data-ttu-id="97964-162">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="97964-162">*Startup.cs*:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{Make?}/{Model?}");
```

<span data-ttu-id="97964-163">*Index. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="97964-163">*Index.cshtml*:</span></span>

```cshtml
<cache vary-by-route="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-cookie"></a><span data-ttu-id="97964-164">různé – podle souborů cookie</span><span class="sxs-lookup"><span data-stu-id="97964-164">vary-by-cookie</span></span>

| <span data-ttu-id="97964-165">Typ atributu</span><span class="sxs-lookup"><span data-stu-id="97964-165">Attribute Type</span></span> | <span data-ttu-id="97964-166">Příklady</span><span class="sxs-lookup"><span data-stu-id="97964-166">Examples</span></span>                                                                         |
| -------------- | -------------------------------------------------------------------------------- |
| <span data-ttu-id="97964-167">Řetězec</span><span class="sxs-lookup"><span data-stu-id="97964-167">String</span></span>         | <span data-ttu-id="97964-168">`.AspNetCore.Identity.Application`, `.AspNetCore.Identity.Application,HairColor`</span><span class="sxs-lookup"><span data-stu-id="97964-168">`.AspNetCore.Identity.Application`, `.AspNetCore.Identity.Application,HairColor`</span></span> |

<span data-ttu-id="97964-169">`vary-by-cookie`přijímá seznam názvů souborů cookie oddělených čárkami, které aktivují aktualizaci mezipaměti, když se změní hodnoty souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="97964-169">`vary-by-cookie` accepts a comma-delimited list of cookie names that trigger a cache refresh when the cookie values change.</span></span>

<span data-ttu-id="97964-170">Následující příklad monitoruje soubor cookie přidružený k ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="97964-170">The following example monitors the cookie associated with ASP.NET Core Identity.</span></span> <span data-ttu-id="97964-171">Při ověření uživatele spustí změna v Identity souboru cookie aktualizaci mezipaměti:</span><span class="sxs-lookup"><span data-stu-id="97964-171">When a user is authenticated, a change in the Identity cookie triggers a cache refresh:</span></span>

```cshtml
<cache vary-by-cookie=".AspNetCore.Identity.Application">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-user"></a><span data-ttu-id="97964-172">různé – podle uživatele</span><span class="sxs-lookup"><span data-stu-id="97964-172">vary-by-user</span></span>

| <span data-ttu-id="97964-173">Typ atributu</span><span class="sxs-lookup"><span data-stu-id="97964-173">Attribute Type</span></span>  | <span data-ttu-id="97964-174">Příklady</span><span class="sxs-lookup"><span data-stu-id="97964-174">Examples</span></span>        | <span data-ttu-id="97964-175">Výchozí</span><span class="sxs-lookup"><span data-stu-id="97964-175">Default</span></span> |
| --------------- | --------------- | ------- |
| <span data-ttu-id="97964-176">Logická hodnota</span><span class="sxs-lookup"><span data-stu-id="97964-176">Boolean</span></span>         | <span data-ttu-id="97964-177">`true`, `false`</span><span class="sxs-lookup"><span data-stu-id="97964-177">`true`, `false`</span></span> | `true`  |

<span data-ttu-id="97964-178">`vary-by-user`Určuje, zda je mezipaměť obnovena při změně přihlášeného uživatele (nebo objektu zabezpečení kontextu).</span><span class="sxs-lookup"><span data-stu-id="97964-178">`vary-by-user` specifies whether or not the cache resets when the signed-in user (or Context Principal) changes.</span></span> <span data-ttu-id="97964-179">Aktuální uživatel je také známý jako objekt zabezpečení kontextu požadavku a lze jej zobrazit v Razor zobrazení odkazem `@User.Identity.Name` .</span><span class="sxs-lookup"><span data-stu-id="97964-179">The current user is also known as the Request Context Principal and can be viewed in a Razor view by referencing `@User.Identity.Name`.</span></span>

<span data-ttu-id="97964-180">Následující příklad sleduje aktuálně přihlášeného uživatele, aby aktivoval aktualizaci mezipaměti:</span><span class="sxs-lookup"><span data-stu-id="97964-180">The following example monitors the current logged in user to trigger a cache refresh:</span></span>

```cshtml
<cache vary-by-user="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="97964-181">Použití tohoto atributu udržuje obsah v mezipaměti prostřednictvím cyklu přihlášení a odhlášení.</span><span class="sxs-lookup"><span data-stu-id="97964-181">Using this attribute maintains the contents in cache through a sign-in and sign-out cycle.</span></span> <span data-ttu-id="97964-182">Pokud je hodnota nastavena na `true` , cyklus ověřování neověřuje mezipaměť pro ověřeného uživatele.</span><span class="sxs-lookup"><span data-stu-id="97964-182">When the value is set to `true`, an authentication cycle invalidates the cache for the authenticated user.</span></span> <span data-ttu-id="97964-183">Platnost mezipaměti je zrušena, protože při ověření uživatele je vygenerována nová jedinečná hodnota souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="97964-183">The cache is invalidated because a new unique cookie value is generated when a user is authenticated.</span></span> <span data-ttu-id="97964-184">Mezipaměť je udržována pro anonymní stav, pokud není přítomen žádný soubor cookie nebo vypršela platnost souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="97964-184">Cache is maintained for the anonymous state when no cookie is present or the cookie has expired.</span></span> <span data-ttu-id="97964-185">Pokud uživatel **není ověřený,** zachová se mezipaměť.</span><span class="sxs-lookup"><span data-stu-id="97964-185">If the user is **not** authenticated, the cache is maintained.</span></span>

### <a name="vary-by"></a><span data-ttu-id="97964-186">různé – podle</span><span class="sxs-lookup"><span data-stu-id="97964-186">vary-by</span></span>

| <span data-ttu-id="97964-187">Typ atributu</span><span class="sxs-lookup"><span data-stu-id="97964-187">Attribute Type</span></span> | <span data-ttu-id="97964-188">Příklad</span><span class="sxs-lookup"><span data-stu-id="97964-188">Example</span></span>  |
| -------------- | -------- |
| <span data-ttu-id="97964-189">Řetězec</span><span class="sxs-lookup"><span data-stu-id="97964-189">String</span></span>         | `@Model` |

<span data-ttu-id="97964-190">`vary-by`umožňuje přizpůsobení dat uložených v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="97964-190">`vary-by` allows for customization of what data is cached.</span></span> <span data-ttu-id="97964-191">Pokud se změní objekt odkazovaný hodnotou řetězce atributu, je aktualizován obsah pomocné rutiny značky cache.</span><span class="sxs-lookup"><span data-stu-id="97964-191">When the object referenced by the attribute's string value changes, the content of the Cache Tag Helper is updated.</span></span> <span data-ttu-id="97964-192">K tomuto atributu jsou často přiřazeny řetězcové zřetězení hodnot modelu.</span><span class="sxs-lookup"><span data-stu-id="97964-192">Often, a string-concatenation of model values are assigned to this attribute.</span></span> <span data-ttu-id="97964-193">To má za následek situaci, kdy aktualizace jakékoli zřetězené hodnoty neověřuje mezipaměť.</span><span class="sxs-lookup"><span data-stu-id="97964-193">Effectively, this results in a scenario where an update to any of the concatenated values invalidates the cache.</span></span>

<span data-ttu-id="97964-194">Následující příklad předpokládá, že metoda kontroleru vykreslování zobrazení sečte celočíselnou hodnotu dvou parametrů trasy `myParam1` a `myParam2` vrátí součet jako vlastnost jednoho modelu.</span><span class="sxs-lookup"><span data-stu-id="97964-194">The following example assumes the controller method rendering the view sums the integer value of the two route parameters, `myParam1` and `myParam2`, and returns the sum as the single model property.</span></span> <span data-ttu-id="97964-195">Při změně tohoto součtu se obsah pomocné rutiny značky mezipaměti vykresluje a ukládá do mezipaměti znovu.</span><span class="sxs-lookup"><span data-stu-id="97964-195">When this sum changes, the content of the Cache Tag Helper is rendered and cached again.</span></span>  

<span data-ttu-id="97964-196">Akce:</span><span class="sxs-lookup"><span data-stu-id="97964-196">Action:</span></span>

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

<span data-ttu-id="97964-197">*Index. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="97964-197">*Index.cshtml*:</span></span>

```cshtml
<cache vary-by="@Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="priority"></a><span data-ttu-id="97964-198">upřednostněn</span><span class="sxs-lookup"><span data-stu-id="97964-198">priority</span></span>

| <span data-ttu-id="97964-199">Typ atributu</span><span class="sxs-lookup"><span data-stu-id="97964-199">Attribute Type</span></span>      | <span data-ttu-id="97964-200">Příklady</span><span class="sxs-lookup"><span data-stu-id="97964-200">Examples</span></span>                               | <span data-ttu-id="97964-201">Výchozí</span><span class="sxs-lookup"><span data-stu-id="97964-201">Default</span></span>  |
| ------------------- | -------------------------------------- | -------- |
| `CacheItemPriority` | <span data-ttu-id="97964-202">`High`, `Low`, `NeverRemove`, `Normal`</span><span class="sxs-lookup"><span data-stu-id="97964-202">`High`, `Low`, `NeverRemove`, `Normal`</span></span> | `Normal` |

<span data-ttu-id="97964-203">`priority`poskytuje pokyny k vyřazení mezipaměti do integrovaného poskytovatele mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="97964-203">`priority` provides cache eviction guidance to the built-in cache provider.</span></span> <span data-ttu-id="97964-204">Webový server `Low` nejprve vyřadí položky mezipaměti, když dojde k přetížení paměti.</span><span class="sxs-lookup"><span data-stu-id="97964-204">The web server evicts `Low` cache entries first when it's under memory pressure.</span></span>

<span data-ttu-id="97964-205">Příklad:</span><span class="sxs-lookup"><span data-stu-id="97964-205">Example:</span></span>

```cshtml
<cache priority="High">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="97964-206">`priority`Atribut nezaručuje konkrétní úroveň uchovávání mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="97964-206">The `priority` attribute doesn't guarantee a specific level of cache retention.</span></span> <span data-ttu-id="97964-207">`CacheItemPriority`je pouze návrh.</span><span class="sxs-lookup"><span data-stu-id="97964-207">`CacheItemPriority` is only a suggestion.</span></span> <span data-ttu-id="97964-208">Nastavením tohoto atributu se `NeverRemove` nezaručí, že položky uložené v mezipaměti jsou vždycky zachované.</span><span class="sxs-lookup"><span data-stu-id="97964-208">Setting this attribute to `NeverRemove` doesn't guarantee that cached items are always retained.</span></span> <span data-ttu-id="97964-209">Další informace najdete v tématech v části [Další zdroje](#additional-resources) informací.</span><span class="sxs-lookup"><span data-stu-id="97964-209">See the topics in the [Additional Resources](#additional-resources) section for more information.</span></span>

<span data-ttu-id="97964-210">Pomocník značek cache je závislý na [službě mezipaměť paměti](xref:performance/caching/memory).</span><span class="sxs-lookup"><span data-stu-id="97964-210">The Cache Tag Helper is dependent on the [memory cache service](xref:performance/caching/memory).</span></span> <span data-ttu-id="97964-211">Pomocník značek cache přidá službu, pokud nebyla přidána.</span><span class="sxs-lookup"><span data-stu-id="97964-211">The Cache Tag Helper adds the service if it hasn't been added.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="97964-212">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="97964-212">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
