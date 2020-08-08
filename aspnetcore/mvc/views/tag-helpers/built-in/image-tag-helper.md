---
title: Pomocník značek obrázku v ASP.NET Core
author: pkellner
description: Ukazuje, jak pracovat s pomocníkem značek obrázku.
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/image-tag-helper
ms.openlocfilehash: a877078356b54a3d0bba6a1be24307e6e1c075c8
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018595"
---
# <a name="image-tag-helper-in-aspnet-core"></a><span data-ttu-id="acee4-103">Pomocník značek obrázku v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="acee4-103">Image Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="acee4-104">Od [Petra Kellner](https://peterkellner.net)</span><span class="sxs-lookup"><span data-stu-id="acee4-104">By [Peter Kellner](https://peterkellner.net)</span></span>

<span data-ttu-id="acee4-105">Pomocník značek image rozšiřuje `<img>` značku tak, aby poskytoval busting chování mezipaměti pro soubory statických imagí.</span><span class="sxs-lookup"><span data-stu-id="acee4-105">The Image Tag Helper enhances the `<img>` tag to provide cache-busting behavior for static image files.</span></span>

<span data-ttu-id="acee4-106">Řetězec cache-busting je jedinečná hodnota, která představuje hodnotu hash souboru statické bitové kopie připojené k adrese URL prostředku.</span><span class="sxs-lookup"><span data-stu-id="acee4-106">A cache-busting string is a unique value representing the hash of the static image file appended to the asset's URL.</span></span> <span data-ttu-id="acee4-107">Jedinečný řetězec vyzve klienty (a některé proxy) k opětovnému načtení bitové kopie z hostitelského webového serveru a nikoli z mezipaměti klienta.</span><span class="sxs-lookup"><span data-stu-id="acee4-107">The unique string prompts clients (and some proxies) to reload the image from the host web server and not from the client's cache.</span></span>

<span data-ttu-id="acee4-108">Pokud je zdroj Image ( `src` ) statickým souborem na hostitelském webovém serveru:</span><span class="sxs-lookup"><span data-stu-id="acee4-108">If the image source (`src`) is a static file on the host web server:</span></span>

* <span data-ttu-id="acee4-109">Jedinečný řetězec busting cache je připojen jako parametr dotazu pro zdroj obrázku.</span><span class="sxs-lookup"><span data-stu-id="acee4-109">A unique cache-busting string is appended as a query parameter to the image source.</span></span>
* <span data-ttu-id="acee4-110">Pokud se soubor na hostitelském webovém serveru změní, je vygenerována jedinečná adresa URL požadavku, která obsahuje aktualizovaný parametr žádosti.</span><span class="sxs-lookup"><span data-stu-id="acee4-110">If the file on the host web server changes, a unique request URL is generated that includes the updated request parameter.</span></span>

<span data-ttu-id="acee4-111">Přehled pomocníků značek naleznete v tématu <xref:mvc/views/tag-helpers/intro> .</span><span class="sxs-lookup"><span data-stu-id="acee4-111">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

## <a name="image-tag-helper-attributes"></a><span data-ttu-id="acee4-112">Pomocné atributy značek obrázku</span><span class="sxs-lookup"><span data-stu-id="acee4-112">Image Tag Helper Attributes</span></span>

### <a name="src"></a><span data-ttu-id="acee4-113">src</span><span class="sxs-lookup"><span data-stu-id="acee4-113">src</span></span>

<span data-ttu-id="acee4-114">Chcete-li aktivovat pomocníka značky image, `src` je atribut požadován pro `<img>` element.</span><span class="sxs-lookup"><span data-stu-id="acee4-114">To activate the Image Tag Helper, the `src` attribute is required on the `<img>` element.</span></span>

<span data-ttu-id="acee4-115">Zdroj obrázku ( `src` ) musí odkazovat na fyzický statický soubor na serveru.</span><span class="sxs-lookup"><span data-stu-id="acee4-115">The image source (`src`) must point to a physical static file on the server.</span></span> <span data-ttu-id="acee4-116">Pokud `src` je to vzdálený identifikátor URI, parametr řetězce dotazu cache-busting se nevygeneruje.</span><span class="sxs-lookup"><span data-stu-id="acee4-116">If the `src` is a remote URI, the cache-busting query string parameter isn't generated.</span></span>

### <a name="asp-append-version"></a><span data-ttu-id="acee4-117">ASP – připojení – verze</span><span class="sxs-lookup"><span data-stu-id="acee4-117">asp-append-version</span></span>

<span data-ttu-id="acee4-118">Je-li `asp-append-version` parametr zadán s `true` hodnotou společně s `src` atributem, je vyvolána pomocná značka obrázku.</span><span class="sxs-lookup"><span data-stu-id="acee4-118">When `asp-append-version` is specified with a `true` value along with a `src` attribute, the Image Tag Helper is invoked.</span></span>

<span data-ttu-id="acee4-119">V následujícím příkladu je použita pomocná pomůcka značek obrázku:</span><span class="sxs-lookup"><span data-stu-id="acee4-119">The following example uses an Image Tag Helper:</span></span>

```cshtml
<img src="~/images/asplogo.png" asp-append-version="true">
```

<span data-ttu-id="acee4-120">Pokud statický soubor existuje v adresáři */wwwroot/images/*, VYGENEROVANÝ kód HTML je podobný následujícímu (hodnota hash bude odlišná):</span><span class="sxs-lookup"><span data-stu-id="acee4-120">If the static file exists in the directory */wwwroot/images/*, the generated HTML is similar to the following (the hash will be different):</span></span>

```html
<img src="/images/asplogo.png?v=Kl_dqr9NVtnMdsM2MUg4qthUnWZm5T1fCEimBPWDNgM">
```

<span data-ttu-id="acee4-121">Hodnota přiřazená parametru `v` je hodnota hash *asplogo.png* souboru na disku.</span><span class="sxs-lookup"><span data-stu-id="acee4-121">The value assigned to the parameter `v` is the hash value of the *asplogo.png* file on disk.</span></span> <span data-ttu-id="acee4-122">Pokud webový server nemůže získat přístup pro čtení ke statickému souboru, `v` není přidán žádný parametr do `src` atributu ve vykresleném kódu.</span><span class="sxs-lookup"><span data-stu-id="acee4-122">If the web server is unable to obtain read access to the static file, no `v` parameter is added to the `src` attribute in the rendered markup.</span></span>

## <a name="hash-caching-behavior"></a><span data-ttu-id="acee4-123">Chování ukládání do mezipaměti hash</span><span class="sxs-lookup"><span data-stu-id="acee4-123">Hash caching behavior</span></span>

<span data-ttu-id="acee4-124">Pomocník značek image používá poskytovatele mezipaměti na místním webovém serveru k uložení vypočtené `Sha512` hodnoty hash daného souboru.</span><span class="sxs-lookup"><span data-stu-id="acee4-124">The Image Tag Helper uses the cache provider on the local web server to store the calculated `Sha512` hash of a given file.</span></span> <span data-ttu-id="acee4-125">Pokud se soubor vyžádá víckrát, hodnota hash se nepřepočítá.</span><span class="sxs-lookup"><span data-stu-id="acee4-125">If the file is requested multiple times, the hash isn't recalculated.</span></span> <span data-ttu-id="acee4-126">V případě, že `Sha512` je vypočtena hodnota hash souboru, je mezipaměť zrušena platností sledovacího procesu souboru připojeného k souboru.</span><span class="sxs-lookup"><span data-stu-id="acee4-126">The cache is invalidated by a file watcher that's attached to the file when the file's `Sha512` hash is calculated.</span></span> <span data-ttu-id="acee4-127">Při změně souboru na disku se vypočítává nový hash a ukládá do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="acee4-127">When the file changes on disk, a new hash is calculated and cached.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="acee4-128">Další materiály</span><span class="sxs-lookup"><span data-stu-id="acee4-128">Additional resources</span></span>

* <xref:performance/caching/memory>
