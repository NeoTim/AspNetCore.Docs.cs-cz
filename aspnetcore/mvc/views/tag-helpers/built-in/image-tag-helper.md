---
title: Pomocná rutina značky obrázku v ASP.NET Core
author: pkellner
description: Ukazuje, jak pracovat s pomocná rutina značky obrázku.
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
uid: mvc/views/tag-helpers/builtin-th/image-tag-helper
ms.openlocfilehash: 964072ad276f7e3e411ee41cb03a2efb9d05c585
ms.sourcegitcommit: 7a40c56bf6a6aaa63a7ee83a2cac9b3a1d77555e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67856118"
---
# <a name="image-tag-helper-in-aspnet-core"></a><span data-ttu-id="159fe-103">Pomocná rutina značky obrázku v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="159fe-103">Image Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="159fe-104">Podle [Peter Kellner](https://peterkellner.net)</span><span class="sxs-lookup"><span data-stu-id="159fe-104">By [Peter Kellner](https://peterkellner.net)</span></span>

<span data-ttu-id="159fe-105">Pomocná rutina značky obrázku rozšiřuje `<img>` značky, které poskytují chování mezipaměti nejnovějších souborů statické bitové kopie.</span><span class="sxs-lookup"><span data-stu-id="159fe-105">The Image Tag Helper enhances the `<img>` tag to provide cache-busting behavior for static image files.</span></span>

<span data-ttu-id="159fe-106">Řetězec nejnovějších mezipaměti je jedinečná hodnota, který představuje hodnotu hash souboru statický obrázek připojí k adrese URL prostředku.</span><span class="sxs-lookup"><span data-stu-id="159fe-106">A cache-busting string is a unique value representing the hash of the static image file appended to the asset's URL.</span></span> <span data-ttu-id="159fe-107">Jedinečný řetězec vyzve klienty (a některé proxy servery), aby znovu načíst obrázek z hostitele webového serveru a ne z mezipaměti klienta.</span><span class="sxs-lookup"><span data-stu-id="159fe-107">The unique string prompts clients (and some proxies) to reload the image from the host web server and not from the client's cache.</span></span>

<span data-ttu-id="159fe-108">Pokud zdroj obrázku (`src`) je statický soubor na webovém serveru hostitele:</span><span class="sxs-lookup"><span data-stu-id="159fe-108">If the image source (`src`) is a static file on the host web server:</span></span>

* <span data-ttu-id="159fe-109">Jedinečné nejnovějších mezipaměti řetězec je připojen jako parametr dotazu na zdroj bitové kopie.</span><span class="sxs-lookup"><span data-stu-id="159fe-109">A unique cache-busting string is appended as a query parameter to the image source.</span></span>
* <span data-ttu-id="159fe-110">Pokud se změní soubor na webovém serveru hostitele, je generována jedinečný požadavek URL, která zahrnuje parametr aktualizované požadavku.</span><span class="sxs-lookup"><span data-stu-id="159fe-110">If the file on the host web server changes, a unique request URL is generated that includes the updated request parameter.</span></span>

<span data-ttu-id="159fe-111">Přehled pomocných rutin značek, naleznete v tématu <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="159fe-111">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

## <a name="image-tag-helper-attributes"></a><span data-ttu-id="159fe-112">Atributy pomocné rutiny značky obrázku</span><span class="sxs-lookup"><span data-stu-id="159fe-112">Image Tag Helper Attributes</span></span>

### <a name="src"></a><span data-ttu-id="159fe-113">src</span><span class="sxs-lookup"><span data-stu-id="159fe-113">src</span></span>

<span data-ttu-id="159fe-114">K aktivaci pomocná rutina značky obrázku `src` na je vyžadován atribut `<img>` elementu.</span><span class="sxs-lookup"><span data-stu-id="159fe-114">To activate the Image Tag Helper, the `src` attribute is required on the `<img>` element.</span></span>

<span data-ttu-id="159fe-115">Zdroj obrázku (`src`) musí odkazovat na fyzický statický soubor na serveru.</span><span class="sxs-lookup"><span data-stu-id="159fe-115">The image source (`src`) must point to a physical static file on the server.</span></span> <span data-ttu-id="159fe-116">Pokud `src` je identifikátor URI vzdálené mezipaměti nejnovějších parametru řetězce dotazu se nevygeneroval.</span><span class="sxs-lookup"><span data-stu-id="159fe-116">If the `src` is a remote URI, the cache-busting query string parameter isn't generated.</span></span>

### <a name="asp-append-version"></a><span data-ttu-id="159fe-117">asp-append-version</span><span class="sxs-lookup"><span data-stu-id="159fe-117">asp-append-version</span></span>

<span data-ttu-id="159fe-118">Při `asp-append-version` zadán s parametrem `true` hodnotu spolu s `src` atribut pomocná rutina značky obrázku je vyvolána.</span><span class="sxs-lookup"><span data-stu-id="159fe-118">When `asp-append-version` is specified with a `true` value along with a `src` attribute, the Image Tag Helper is invoked.</span></span>

<span data-ttu-id="159fe-119">Následující příklad používá pomocná rutina značky obrázku:</span><span class="sxs-lookup"><span data-stu-id="159fe-119">The following example uses an Image Tag Helper:</span></span>

```cshtml
<img src="~/images/asplogo.png" asp-append-version="true">
```

<span data-ttu-id="159fe-120">Pokud ke statickému souboru v adresáři existuje */wwwroot/image/* , generovaný kód HTML je podobný následujícímu (hodnota hash se lišit):</span><span class="sxs-lookup"><span data-stu-id="159fe-120">If the static file exists in the directory */wwwroot/images/*, the generated HTML is similar to the following (the hash will be different):</span></span>

```html
<img src="/images/asplogo.png?v=Kl_dqr9NVtnMdsM2MUg4qthUnWZm5T1fCEimBPWDNgM">
```

<span data-ttu-id="159fe-121">Hodnota přiřazená k parametru `v` je hodnota hash *asplogo.png* souboru na disku.</span><span class="sxs-lookup"><span data-stu-id="159fe-121">The value assigned to the parameter `v` is the hash value of the *asplogo.png* file on disk.</span></span> <span data-ttu-id="159fe-122">Pokud webový server nelze získat přístup pro čtení k statických souborů žádné `v` parametru se přidá do `src` atribut v vykreslované značky.</span><span class="sxs-lookup"><span data-stu-id="159fe-122">If the web server is unable to obtain read access to the static file, no `v` parameter is added to the `src` attribute in the rendered markup.</span></span>

## <a name="hash-caching-behavior"></a><span data-ttu-id="159fe-123">Hash – chování ukládání do mezipaměti</span><span class="sxs-lookup"><span data-stu-id="159fe-123">Hash caching behavior</span></span>

<span data-ttu-id="159fe-124">Pomocná rutina značky obrázku využívá poskytovatele mezipaměti v místní webový server k ukládání vypočítané `Sha512` hash daný soubor.</span><span class="sxs-lookup"><span data-stu-id="159fe-124">The Image Tag Helper uses the cache provider on the local web server to store the calculated `Sha512` hash of a given file.</span></span> <span data-ttu-id="159fe-125">Pokud soubor je požadováno více než jednou, není přepočítá hodnoty hash.</span><span class="sxs-lookup"><span data-stu-id="159fe-125">If the file is requested multiple times, the hash isn't recalculated.</span></span> <span data-ttu-id="159fe-126">Mezipaměť je ukončit platnost souboru sledovací proces, který se připojuje k souboru po souboru `Sha512` vypočítat hodnotu hash.</span><span class="sxs-lookup"><span data-stu-id="159fe-126">The cache is invalidated by a file watcher that's attached to the file when the file's `Sha512` hash is calculated.</span></span> <span data-ttu-id="159fe-127">Při změně souboru na disku, nová hodnota hash se počítá a uložili do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="159fe-127">When the file changes on disk, a new hash is calculated and cached.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="159fe-128">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="159fe-128">Additional resources</span></span>

* <xref:performance/caching/memory>
