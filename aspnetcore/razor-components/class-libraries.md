---
title: Knihovny tříd Razor komponenty
author: guardrex
description: Zjistěte, jak komponenty mohou být součástí aplikace Razor komponenty z externí komponenta knihovny.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/09/2019
uid: razor-components/class-libraries
ms.openlocfilehash: 0e644627178bae2b8880760335860b3e0ebef156
ms.sourcegitcommit: af8a6eb5375ef547a52ffae22465e265837aa82b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56159231"
---
# <a name="razor-components-class-libraries"></a><span data-ttu-id="4394f-103">Knihovny tříd Razor komponenty</span><span class="sxs-lookup"><span data-stu-id="4394f-103">Razor Components Class Libraries</span></span>

<span data-ttu-id="4394f-104">Podle [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="4394f-104">By [Simon Timms](https://github.com/stimms)</span></span>

> [!NOTE]
> <span data-ttu-id="4394f-105">Sada .NET Core 3.0 ve verzi Preview 2 SDK neobsahuje šablona projektu pro knihovny tříd Razor součásti, ale chceme přidat šablonu v budoucí verzi preview.</span><span class="sxs-lookup"><span data-stu-id="4394f-105">The .NET Core 3.0 Preview 2 SDK doesn't include a project template for Razor Component Class Libraries, but we expect to add a template in a future preview.</span></span> <span data-ttu-id="4394f-106">Mezitím můžete použít šablony knihovna tříd komponenty Blazor vysvětlované v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="4394f-106">In meantime, you can use the Blazor Component Class Library template explained in this topic.</span></span>

<span data-ttu-id="4394f-107">Součástí je sdílet v knihovnách komponenty ve všech projektech.</span><span class="sxs-lookup"><span data-stu-id="4394f-107">Components can be shared in component libraries across projects.</span></span> <span data-ttu-id="4394f-108">Je možné zahrnout z komponenty:</span><span class="sxs-lookup"><span data-stu-id="4394f-108">Components can be included from:</span></span>

* <span data-ttu-id="4394f-109">Jiný projekt v řešení.</span><span class="sxs-lookup"><span data-stu-id="4394f-109">Another project in the solution.</span></span>
* <span data-ttu-id="4394f-110">Balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="4394f-110">A NuGet package.</span></span>
* <span data-ttu-id="4394f-111">Odkazované knihovny .NET.</span><span class="sxs-lookup"><span data-stu-id="4394f-111">A referenced .NET library.</span></span>

<span data-ttu-id="4394f-112">Stejně, jako jsou komponenty regulární typy .NET, jsou součástí knihovny normální sestavení .NET.</span><span class="sxs-lookup"><span data-stu-id="4394f-112">Just as components are regular .NET types, component libraries are normal .NET assemblies.</span></span>

<span data-ttu-id="4394f-113">K vytvoření nové komponenty knihovny, použijte `blazorlib` šablonu s [dotnet nové](/dotnet/core/tools/dotnet-new) příkazu.</span><span class="sxs-lookup"><span data-stu-id="4394f-113">To create a new component library, use the `blazorlib` template with the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="4394f-114">Šablona je součástí šablony nainstalované při [nastavení komponent Razor](xref:razor-components/get-started).</span><span class="sxs-lookup"><span data-stu-id="4394f-114">The template is part of the templates installed when [setting up Razor Components](xref:razor-components/get-started).</span></span>

```console
dotnet new blazorlib -o MyComponentLib1
```

<span data-ttu-id="4394f-115">Chcete-li přidat knihovnu do existujícího projektu, použijte [dotnet sln](/dotnet/core/tools/dotnet-sln) příkaz:</span><span class="sxs-lookup"><span data-stu-id="4394f-115">To add the library to an existing project, use the [dotnet sln](/dotnet/core/tools/dotnet-sln) command:</span></span>

```console
dotnet sln add .\MyComponentLib1
```

<span data-ttu-id="4394f-116">Komponenta knihovny mohou obsahovat statické soubory, jako jsou obrázky, JavaScript a šablony stylů.</span><span class="sxs-lookup"><span data-stu-id="4394f-116">Component libraries may contain static files, such as images, JavaScript, and stylesheets.</span></span> <span data-ttu-id="4394f-117">V okamžiku sestavení, statické soubory jsou vloženy do souborů sestavení (*.dll*), která umožňuje využití komponent bez nutnosti starat o tom, jak zahrnout svoje prostředky.</span><span class="sxs-lookup"><span data-stu-id="4394f-117">At build time, static files are embedded into the built assembly file (*.dll*), which allows consumption of the components without having to worry about how to include their resources.</span></span> <span data-ttu-id="4394f-118">Všechny soubory zahrnuté v `content` adresáře jsou označeny jako vložený prostředek.</span><span class="sxs-lookup"><span data-stu-id="4394f-118">Any files included in the `content` directory are marked as an embedded resource.</span></span> 

## <a name="consume-a-library-component"></a><span data-ttu-id="4394f-119">Využívat komponentu knihovny</span><span class="sxs-lookup"><span data-stu-id="4394f-119">Consume a library component</span></span>

<span data-ttu-id="4394f-120">Aby bylo možné využívat součásti definované v knihovně v jiném projektu [ @addTagHelper ](/aspnet/core/mvc/views/tag-helpers/intro#add-helper-label) musí se použít direktiva.</span><span class="sxs-lookup"><span data-stu-id="4394f-120">In order to consume components defined in a library in another project, the [@addTagHelper](/aspnet/core/mvc/views/tag-helpers/intro#add-helper-label) directive must be used.</span></span> <span data-ttu-id="4394f-121">Jednotlivé komponenty se můžou přidávat podle názvu.</span><span class="sxs-lookup"><span data-stu-id="4394f-121">Individual components may be added by name.</span></span> <span data-ttu-id="4394f-122">Například následující direktivy přidává `Component1` z `MyComponentLib1`:</span><span class="sxs-lookup"><span data-stu-id="4394f-122">For example, the following directive adds `Component1` of `MyComponentLib1`:</span></span>

```cshtml
@addTagHelper MyComponentLib1.Component1, MyComponentLib1
```

<span data-ttu-id="4394f-123">Obecný formát direktivy je:</span><span class="sxs-lookup"><span data-stu-id="4394f-123">The general format of the directive is:</span></span>

```cshtml
@addTagHelper <namespaced component name>, <assembly name>
```

<span data-ttu-id="4394f-124">Je však společné pro všechny součásti ze sestavení pomocí zástupného znaku patří:</span><span class="sxs-lookup"><span data-stu-id="4394f-124">However, it's common to include all of the components from an assembly using a wildcard:</span></span>

```cshtml
@addTagHelper *, MyComponentLib1
```

<span data-ttu-id="4394f-125">`@addTagHelper` Směrnice můžou být součástí *_ViewImport.cshtml* k vytvoření součásti k dispozici pro celý projekt nebo použité na jednu stránku nebo sadu stránek ve složce.</span><span class="sxs-lookup"><span data-stu-id="4394f-125">The `@addTagHelper` directive can be included in *_ViewImport.cshtml* to make the components available for an entire project or applied to a single page or set of pages within a folder.</span></span> <span data-ttu-id="4394f-126">S `@addTagHelper` direktiv v místě, součástí knihovny součástí mohou být spotřebovány jako kdyby byly ve stejném sestavení jako aplikace.</span><span class="sxs-lookup"><span data-stu-id="4394f-126">With the `@addTagHelper` directive in place, the components of the component library can be consumed as if they were in the same assembly as the app.</span></span> 

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="4394f-127">Sestavení, aktualizací Service pack a příjemce pro NuGet</span><span class="sxs-lookup"><span data-stu-id="4394f-127">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="4394f-128">Protože jsou součástí knihovny knihovny .NET standard, balení a je na cestě k NuGet se nijak neliší od balení a přesouvání všechny knihovny nuget.</span><span class="sxs-lookup"><span data-stu-id="4394f-128">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="4394f-129">Balení se provádí pomocí [balíčku dotnet](/dotnet/core/tools/dotnet-pack) příkaz:</span><span class="sxs-lookup"><span data-stu-id="4394f-129">Packaging is performed using the [dotnet pack](/dotnet/core/tools/dotnet-pack) command:</span></span>

```console
dotnet pack
```

<span data-ttu-id="4394f-130">Nahrání balíčku NuGet pomocí [dotnet nuget publikovat](/dotnet/core/tools/dotnet-nuget-push) příkaz:</span><span class="sxs-lookup"><span data-stu-id="4394f-130">Upload the package to NuGet using the [dotnet nuget publish](/dotnet/core/tools/dotnet-nuget-push) command:</span></span>

```console
dotnet nuget publish
```

<span data-ttu-id="4394f-131">Žádné zahrnuté statické prostředky jsou obsažené v balíčku NuGet.</span><span class="sxs-lookup"><span data-stu-id="4394f-131">Any included static resources are included in the NuGet package.</span></span> <span data-ttu-id="4394f-132">Příjemci knihovny automaticky obdrží skripty a šablony stylů, takže příjemci nejsou potřeba ručně instalovat prostředky.</span><span class="sxs-lookup"><span data-stu-id="4394f-132">Library consumers automatically receive scripts and stylesheets, so consumers aren't required to manually install the resources.</span></span>
