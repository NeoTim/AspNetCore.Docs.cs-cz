---
title: Akvizice knihovny na straně klienta v ASP.NET Core s LibManem
author: scottaddie
description: Zjistěte, jak nainstalovat datové zdroje knihovny na straně klienta v projektu ASP.NET Core pomocí Správce knihovny (LibMan).
ms.author: scaddie
ms.custom: mvc
ms.date: 08/14/2018
uid: client-side/libman/index
ms.openlocfilehash: 87987446b7f2c625da90951510e697e06569ba36
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664666"
---
# <a name="client-side-library-acquisition-in-aspnet-core-with-libman"></a><span data-ttu-id="ea72f-103">Akvizice knihovny na straně klienta v ASP.NET Core s LibManem</span><span class="sxs-lookup"><span data-stu-id="ea72f-103">Client-side library acquisition in ASP.NET Core with LibMan</span></span>

<span data-ttu-id="ea72f-104">Podle [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="ea72f-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="ea72f-105">Správce knihovny (LibMan) je lehký nástroj pro získávání knihovny na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="ea72f-105">Library Manager (LibMan) is a lightweight, client-side library acquisition tool.</span></span> <span data-ttu-id="ea72f-106">LibMan stahuje oblíbené knihovny a architektury ze souborového systému nebo ze [sítě pro doručování obsahu (CDN).](https://wikipedia.org/wiki/Content_delivery_network)</span><span class="sxs-lookup"><span data-stu-id="ea72f-106">LibMan downloads popular libraries and frameworks from the file system or from a [content delivery network (CDN)](https://wikipedia.org/wiki/Content_delivery_network).</span></span> <span data-ttu-id="ea72f-107">Mezi podporované sítě CDN patří [CDNJS](https://cdnjs.com/), [jsDelivr](https://www.jsdelivr.com/)a [unpkg](https://unpkg.com/#/).</span><span class="sxs-lookup"><span data-stu-id="ea72f-107">The supported CDNs include [CDNJS](https://cdnjs.com/), [jsDelivr](https://www.jsdelivr.com/), and [unpkg](https://unpkg.com/#/).</span></span> <span data-ttu-id="ea72f-108">Vybrané soubory knihovny jsou načteny a umístěny na příslušném místě v rámci projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ea72f-108">The selected library files are fetched and placed in the appropriate location within the ASP.NET Core project.</span></span>

## <a name="libman-use-cases"></a><span data-ttu-id="ea72f-109">Případy použití LibMan</span><span class="sxs-lookup"><span data-stu-id="ea72f-109">LibMan use cases</span></span>

<span data-ttu-id="ea72f-110">LibMan nabízí následující výhody:</span><span class="sxs-lookup"><span data-stu-id="ea72f-110">LibMan offers the following benefits:</span></span>

* <span data-ttu-id="ea72f-111">Stahují se pouze soubory knihovny, které potřebujete.</span><span class="sxs-lookup"><span data-stu-id="ea72f-111">Only the library files you need are downloaded.</span></span>
* <span data-ttu-id="ea72f-112">Další nástroje, například [Node.js](https://nodejs.org), [npm](https://www.npmjs.com)a [WebPack](https://webpack.js.org), nejsou nutné k získání podmnožiny souborů v knihovně.</span><span class="sxs-lookup"><span data-stu-id="ea72f-112">Additional tooling, such as [Node.js](https://nodejs.org), [npm](https://www.npmjs.com), and [WebPack](https://webpack.js.org), isn't necessary to acquire a subset of files in a library.</span></span>
* <span data-ttu-id="ea72f-113">Soubory lze umístit do určitého umístění, aniž by se uchylovaly k vytváření úkolů nebo ručního kopírování souborů.</span><span class="sxs-lookup"><span data-stu-id="ea72f-113">Files can be placed in a specific location without resorting to build tasks or manual file copying.</span></span>

<span data-ttu-id="ea72f-114">Další informace o výhodách společnosti LibMan nasledujte [moderní front-end ový vývoj webových aplikací ve Visual Studiu 2017: Segment LibMan](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s).</span><span class="sxs-lookup"><span data-stu-id="ea72f-114">For more information about LibMan's benefits, watch [Modern front-end web development in Visual Studio 2017: LibMan segment](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s).</span></span>

<span data-ttu-id="ea72f-115">LibMan není systém správy balíčků.</span><span class="sxs-lookup"><span data-stu-id="ea72f-115">LibMan isn't a package management system.</span></span> <span data-ttu-id="ea72f-116">Pokud již používáte správce balíčků, například npm nebo [přízi](https://yarnpkg.com), pokračujte v tom.</span><span class="sxs-lookup"><span data-stu-id="ea72f-116">If you're already using a package manager, such as npm or [yarn](https://yarnpkg.com), continue doing so.</span></span> <span data-ttu-id="ea72f-117">LibMan nebyl vyvinut, aby tyto nástroje nahradil.</span><span class="sxs-lookup"><span data-stu-id="ea72f-117">LibMan wasn't developed to replace those tools.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ea72f-118">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="ea72f-118">Additional resources</span></span>

* <xref:client-side/libman/libman-vs>
* <xref:client-side/libman/libman-cli>
* [<span data-ttu-id="ea72f-119">Úložiště LibMan GitHub</span><span class="sxs-lookup"><span data-stu-id="ea72f-119">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
