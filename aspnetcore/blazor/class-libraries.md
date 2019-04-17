---
title: Knihovny tříd Razor komponenty
author: guardrex
description: Zjistěte, jak komponenty mohou být součástí Blazor aplikací z knihovny externí komponenta.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: blazor/class-libraries
ms.openlocfilehash: 6c0b741de1e3b9ad2b226cc376f06ad8365542e8
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614808"
---
# <a name="razor-components-class-libraries"></a><span data-ttu-id="578ec-103">Knihovny tříd Razor komponenty</span><span class="sxs-lookup"><span data-stu-id="578ec-103">Razor Components Class Libraries</span></span>

<span data-ttu-id="578ec-104">Podle [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="578ec-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="578ec-105">Součásti můžete sdílet v knihovnách tříd Razor ve všech projektech.</span><span class="sxs-lookup"><span data-stu-id="578ec-105">Components can be shared in Razor class libraries across projects.</span></span> <span data-ttu-id="578ec-106">Je možné zahrnout z komponenty:</span><span class="sxs-lookup"><span data-stu-id="578ec-106">Components can be included from:</span></span>

* <span data-ttu-id="578ec-107">Jiný projekt v řešení.</span><span class="sxs-lookup"><span data-stu-id="578ec-107">Another project in the solution.</span></span>
* <span data-ttu-id="578ec-108">Balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="578ec-108">A NuGet package.</span></span>
* <span data-ttu-id="578ec-109">Odkazované knihovny .NET.</span><span class="sxs-lookup"><span data-stu-id="578ec-109">A referenced .NET library.</span></span>

<span data-ttu-id="578ec-110">Stejně jako regulární typy .NET jsou komponenty, jsou součástí knihovny tříd Razor normální sestavení .NET.</span><span class="sxs-lookup"><span data-stu-id="578ec-110">Just as components are regular .NET types, components provided by Razor class libraries are normal .NET assemblies.</span></span>

<span data-ttu-id="578ec-111">Použití `razorclasslib` šablony (knihovny tříd Razor) se [dotnet nové](/dotnet/core/tools/dotnet-new) příkaz:</span><span class="sxs-lookup"><span data-stu-id="578ec-111">Use the `razorclasslib` (Razor class library) template with the [dotnet new](/dotnet/core/tools/dotnet-new) command:</span></span>

```console
dotnet new razorclasslib -o MyComponentLib1
```

<span data-ttu-id="578ec-112">Přidat soubory Razor komponent (*.razor*) do knihovny tříd Razor.</span><span class="sxs-lookup"><span data-stu-id="578ec-112">Add Razor Component files (*.razor*) to the Razor class library.</span></span>

<span data-ttu-id="578ec-113">Chcete-li přidat knihovnu do existujícího projektu, použijte [dotnet sln](/dotnet/core/tools/dotnet-sln) příkaz:</span><span class="sxs-lookup"><span data-stu-id="578ec-113">To add the library to an existing project, use the [dotnet sln](/dotnet/core/tools/dotnet-sln) command:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="578ec-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="578ec-114">Visual Studio</span></span>](#tab/visual-studio)

```console
dotnet sln add .\MyComponentLib1
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="578ec-115">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="578ec-115">.NET Core CLI</span></span>](#tab/netcore-cli)

```console
dotnet add WebApplication1 reference MyComponentLib1
```

---

> [!NOTE]
> <span data-ttu-id="578ec-116">Knihovny tříd Razor nejsou kompatibilní s Blazor aplikace v ASP.NET Core ve verzi Preview 3.</span><span class="sxs-lookup"><span data-stu-id="578ec-116">Razor class libraries aren't compatible with Blazor apps in ASP.NET Core Preview 3.</span></span>
>
> <span data-ttu-id="578ec-117">Vytváření komponent v knihovně, která je možné sdílet s Blazor na straně klienta a Razor součásti aplikace na straně serveru, použijte knihovnu tříd Blazor vytvořené `blazorlib` šablony.</span><span class="sxs-lookup"><span data-stu-id="578ec-117">To create components in a library that can be shared with Blazor client-side and Razor Components server-side apps, use a Blazor class library created by the `blazorlib` template.</span></span>
>
> <span data-ttu-id="578ec-118">Knihovny tříd Razor nepodporují statické prostředky v ASP.NET Core ve verzi Preview 3.</span><span class="sxs-lookup"><span data-stu-id="578ec-118">Razor class libraries don't support static assets in ASP.NET Core Preview 3.</span></span> <span data-ttu-id="578ec-119">Pomocí knihovny součástí `blazorlib` šablona může obsahovat statické soubory, jako jsou obrázky, JavaScript a šablony stylů.</span><span class="sxs-lookup"><span data-stu-id="578ec-119">Component libraries using the `blazorlib` template can include static files, such as images, JavaScript, and stylesheets.</span></span> <span data-ttu-id="578ec-120">V okamžiku sestavení, statické soubory jsou vloženy do souborů sestavení (*.dll*), která umožňuje využití komponent bez nutnosti starat o tom, jak zahrnout svoje prostředky.</span><span class="sxs-lookup"><span data-stu-id="578ec-120">At build time, static files are embedded into the built assembly file (*.dll*), which allows consumption of the components without having to worry about how to include their resources.</span></span> <span data-ttu-id="578ec-121">Všechny soubory zahrnuté v `content` adresáře jsou označeny jako vložený prostředek.</span><span class="sxs-lookup"><span data-stu-id="578ec-121">Any files included in the `content` directory are marked as an embedded resource.</span></span>

## <a name="consume-a-library-component"></a><span data-ttu-id="578ec-122">Využívat komponentu knihovny</span><span class="sxs-lookup"><span data-stu-id="578ec-122">Consume a library component</span></span>

<span data-ttu-id="578ec-123">Aby bylo možné využívat součásti definované v knihovně v jiném projektu [ @addTagHelper ](xref:mvc/views/tag-helpers/intro#add-helper-label) musí se použít direktiva.</span><span class="sxs-lookup"><span data-stu-id="578ec-123">In order to consume components defined in a library in another project, the [@addTagHelper](xref:mvc/views/tag-helpers/intro#add-helper-label) directive must be used.</span></span> <span data-ttu-id="578ec-124">Jednotlivé komponenty se můžou přidávat podle názvu.</span><span class="sxs-lookup"><span data-stu-id="578ec-124">Individual components may be added by name.</span></span>

<span data-ttu-id="578ec-125">Obecný formát direktivy je:</span><span class="sxs-lookup"><span data-stu-id="578ec-125">The general format of the directive is:</span></span>

```cshtml
@addTagHelper MyComponentLib1.Component1, MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

<span data-ttu-id="578ec-126">Například následující direktivy přidává `Component1` z `MyComponentLib1`:</span><span class="sxs-lookup"><span data-stu-id="578ec-126">For example, the following directive adds `Component1` of `MyComponentLib1`:</span></span>

```cshtml
@addTagHelper MyComponentLib1.Component1, MyComponentLib1
```

<span data-ttu-id="578ec-127">Je však společné pro všechny součásti ze sestavení pomocí zástupného znaku zahrnují (`*`):</span><span class="sxs-lookup"><span data-stu-id="578ec-127">However, it's common to include all of the components from an assembly using a wildcard (`*`):</span></span>

```cshtml
@addTagHelper *, MyComponentLib1
```

<span data-ttu-id="578ec-128">`@addTagHelper` Směrnice můžou být součástí *_ViewImport.cshtml* k vytvoření součásti k dispozici pro celý projekt nebo použité na jednu stránku nebo sadu stránek ve složce.</span><span class="sxs-lookup"><span data-stu-id="578ec-128">The `@addTagHelper` directive can be included in *_ViewImport.cshtml* to make the components available for an entire project or applied to a single page or set of pages within a folder.</span></span> <span data-ttu-id="578ec-129">S `@addTagHelper` direktiv v místě, součástí knihovny součástí mohou být spotřebovány jako kdyby byly ve stejném sestavení jako aplikace.</span><span class="sxs-lookup"><span data-stu-id="578ec-129">With the `@addTagHelper` directive in place, the components of the component library can be consumed as if they were in the same assembly as the app.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="578ec-130">Sestavení, aktualizací Service pack a příjemce pro NuGet</span><span class="sxs-lookup"><span data-stu-id="578ec-130">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="578ec-131">Protože jsou součástí knihovny knihovny .NET standard, balení a je na cestě k NuGet se nijak neliší od balení a přesouvání všechny knihovny nuget.</span><span class="sxs-lookup"><span data-stu-id="578ec-131">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="578ec-132">Balení se provádí pomocí [balíčku dotnet](/dotnet/core/tools/dotnet-pack) příkaz:</span><span class="sxs-lookup"><span data-stu-id="578ec-132">Packaging is performed using the [dotnet pack](/dotnet/core/tools/dotnet-pack) command:</span></span>

```console
dotnet pack
```

<span data-ttu-id="578ec-133">Nahrání balíčku NuGet pomocí [dotnet nuget publikovat](/dotnet/core/tools/dotnet-nuget-push) příkaz:</span><span class="sxs-lookup"><span data-stu-id="578ec-133">Upload the package to NuGet using the [dotnet nuget publish](/dotnet/core/tools/dotnet-nuget-push) command:</span></span>

```console
dotnet nuget publish
```

<span data-ttu-id="578ec-134">Při použití `blazorlib` šablonu, statických prostředků jsou obsažené v balíčku NuGet.</span><span class="sxs-lookup"><span data-stu-id="578ec-134">When using the `blazorlib` template, static resources are included in the NuGet package.</span></span> <span data-ttu-id="578ec-135">Příjemci knihovny automaticky obdrží skripty a šablony stylů, takže příjemci nejsou potřeba ručně instalovat prostředky.</span><span class="sxs-lookup"><span data-stu-id="578ec-135">Library consumers automatically receive scripts and stylesheets, so consumers aren't required to manually install the resources.</span></span>
