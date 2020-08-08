---
title: Získání knihovny na straně klienta v ASP.NET Core s LibMan
author: scottaddie
description: Naučte se instalovat prostředky knihovny na straně klienta v ASP.NET Core projektu pomocí Správce knihovny (LibMan).
ms.author: scaddie
ms.custom: mvc
ms.date: 08/14/2018
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
uid: client-side/libman/index
ms.openlocfilehash: 341822b07414dc872113b12562b06a170e497b4c
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013330"
---
# <a name="client-side-library-acquisition-in-aspnet-core-with-libman"></a><span data-ttu-id="f16bb-103">Získání knihovny na straně klienta v ASP.NET Core s LibMan</span><span class="sxs-lookup"><span data-stu-id="f16bb-103">Client-side library acquisition in ASP.NET Core with LibMan</span></span>

<span data-ttu-id="f16bb-104">[Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="f16bb-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="f16bb-105">Správce knihovny (LibMan) je jednoduchý nástroj pro pořízení knihovny na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="f16bb-105">Library Manager (LibMan) is a lightweight, client-side library acquisition tool.</span></span> <span data-ttu-id="f16bb-106">LibMan stáhne oblíbené knihovny a architektury ze systému souborů nebo ze služby [Content Delivery Network (CDN)](https://wikipedia.org/wiki/Content_delivery_network).</span><span class="sxs-lookup"><span data-stu-id="f16bb-106">LibMan downloads popular libraries and frameworks from the file system or from a [content delivery network (CDN)](https://wikipedia.org/wiki/Content_delivery_network).</span></span> <span data-ttu-id="f16bb-107">Mezi podporované sítě CDN patří [CDNJS](https://cdnjs.com/), [jsDelivr](https://www.jsdelivr.com/)a [unpkg](https://unpkg.com/#/).</span><span class="sxs-lookup"><span data-stu-id="f16bb-107">The supported CDNs include [CDNJS](https://cdnjs.com/), [jsDelivr](https://www.jsdelivr.com/), and [unpkg](https://unpkg.com/#/).</span></span> <span data-ttu-id="f16bb-108">Vybrané soubory knihovny jsou načteny a umístěny do příslušného umístění v rámci ASP.NET Core projektu.</span><span class="sxs-lookup"><span data-stu-id="f16bb-108">The selected library files are fetched and placed in the appropriate location within the ASP.NET Core project.</span></span>

## <a name="libman-use-cases"></a><span data-ttu-id="f16bb-109">Případy použití LibMan</span><span class="sxs-lookup"><span data-stu-id="f16bb-109">LibMan use cases</span></span>

<span data-ttu-id="f16bb-110">LibMan nabízí následující výhody:</span><span class="sxs-lookup"><span data-stu-id="f16bb-110">LibMan offers the following benefits:</span></span>

* <span data-ttu-id="f16bb-111">Stáhnou se jenom soubory knihovny, které potřebujete.</span><span class="sxs-lookup"><span data-stu-id="f16bb-111">Only the library files you need are downloaded.</span></span>
* <span data-ttu-id="f16bb-112">Další nástroje, například [Node.js](https://nodejs.org), [npm](https://www.npmjs.com)a [Webpack](https://webpack.js.org), není nutné k získání podmnožiny souborů v knihovně.</span><span class="sxs-lookup"><span data-stu-id="f16bb-112">Additional tooling, such as [Node.js](https://nodejs.org), [npm](https://www.npmjs.com), and [WebPack](https://webpack.js.org), isn't necessary to acquire a subset of files in a library.</span></span>
* <span data-ttu-id="f16bb-113">Soubory lze umístit do konkrétního umístění bez nutnosti vytvářet úlohy nebo provádět ruční kopírování souborů.</span><span class="sxs-lookup"><span data-stu-id="f16bb-113">Files can be placed in a specific location without resorting to build tasks or manual file copying.</span></span>

<span data-ttu-id="f16bb-114">Další informace o výhodách LibMan naleznete [v moderním vývojovém webu v aplikaci Visual Studio 2017: segment LibMan](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s).</span><span class="sxs-lookup"><span data-stu-id="f16bb-114">For more information about LibMan's benefits, watch [Modern front-end web development in Visual Studio 2017: LibMan segment](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s).</span></span>

<span data-ttu-id="f16bb-115">LibMan není systém správy balíčků.</span><span class="sxs-lookup"><span data-stu-id="f16bb-115">LibMan isn't a package management system.</span></span> <span data-ttu-id="f16bb-116">Pokud již používáte Správce balíčků, například npm nebo [příze](https://yarnpkg.com), pokračujte v tomto postupu.</span><span class="sxs-lookup"><span data-stu-id="f16bb-116">If you're already using a package manager, such as npm or [yarn](https://yarnpkg.com), continue doing so.</span></span> <span data-ttu-id="f16bb-117">LibMan se nevyvinuly, aby tyto nástroje nahradily.</span><span class="sxs-lookup"><span data-stu-id="f16bb-117">LibMan wasn't developed to replace those tools.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f16bb-118">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f16bb-118">Additional resources</span></span>

* <xref:client-side/libman/libman-vs>
* <xref:client-side/libman/libman-cli>
* [<span data-ttu-id="f16bb-119">Úložiště GitHub LibMan</span><span class="sxs-lookup"><span data-stu-id="f16bb-119">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
