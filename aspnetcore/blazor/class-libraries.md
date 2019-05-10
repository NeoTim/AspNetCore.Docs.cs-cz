---
title: Knihovny tříd Razor komponenty
author: guardrex
description: Zjistěte, jak komponenty mohou být součástí Blazor aplikací z knihovny externí komponenta.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/06/2019
uid: blazor/class-libraries
ms.openlocfilehash: 9ca1d54da584c2957be98708782437e28b619e3b
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65085843"
---
# <a name="razor-components-class-libraries"></a><span data-ttu-id="1287f-103">Knihovny tříd Razor komponenty</span><span class="sxs-lookup"><span data-stu-id="1287f-103">Razor components class libraries</span></span>

<span data-ttu-id="1287f-104">Podle [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="1287f-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="1287f-105">Součásti můžete sdílet v knihovnách tříd Razor ve všech projektech.</span><span class="sxs-lookup"><span data-stu-id="1287f-105">Components can be shared in Razor class libraries across projects.</span></span> <span data-ttu-id="1287f-106">Je možné zahrnout z komponenty:</span><span class="sxs-lookup"><span data-stu-id="1287f-106">Components can be included from:</span></span>

* <span data-ttu-id="1287f-107">Jiný projekt v řešení.</span><span class="sxs-lookup"><span data-stu-id="1287f-107">Another project in the solution.</span></span>
* <span data-ttu-id="1287f-108">Balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="1287f-108">A NuGet package.</span></span>
* <span data-ttu-id="1287f-109">Odkazované knihovny .NET.</span><span class="sxs-lookup"><span data-stu-id="1287f-109">A referenced .NET library.</span></span>

<span data-ttu-id="1287f-110">Stejně jako regulární typy .NET jsou komponenty, jsou součástí knihovny tříd Razor normální sestavení .NET.</span><span class="sxs-lookup"><span data-stu-id="1287f-110">Just as components are regular .NET types, components provided by Razor class libraries are normal .NET assemblies.</span></span>

## <a name="create-a-razor-class-library"></a><span data-ttu-id="1287f-111">Vytvoření knihovny tříd Razor</span><span class="sxs-lookup"><span data-stu-id="1287f-111">Create a Razor class library</span></span>

<span data-ttu-id="1287f-112">Postupujte podle pokynů v <xref:blazor/get-started> článku ke konfiguraci prostředí pro Blazor.</span><span class="sxs-lookup"><span data-stu-id="1287f-112">Follow the guidance in the <xref:blazor/get-started> article to configure your environment for Blazor.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1287f-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1287f-113">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="1287f-114">Vytvořte nový projekt.</span><span class="sxs-lookup"><span data-stu-id="1287f-114">Create a new project.</span></span>
1. <span data-ttu-id="1287f-115">Vyberte **webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="1287f-115">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="1287f-116">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="1287f-116">Select **Next**.</span></span>
1. <span data-ttu-id="1287f-117">Zadejte název projektu **název projektu** pole nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="1287f-117">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="1287f-118">V příkladech v tomto tématu se používá název projektu `MyComponentLib1`.</span><span class="sxs-lookup"><span data-stu-id="1287f-118">The examples in this topic use the project name `MyComponentLib1`.</span></span> <span data-ttu-id="1287f-119">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="1287f-119">Select **Create**.</span></span>
1. <span data-ttu-id="1287f-120">V **vytvořit novou webovou aplikaci ASP.NET Core** dialogového okna, ujistěte se, že **.NET Core** a **ASP.NET Core 3.0** jsou vybrány.</span><span class="sxs-lookup"><span data-stu-id="1287f-120">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span>
1. <span data-ttu-id="1287f-121">Vyberte **knihovny tříd Razor** šablony.</span><span class="sxs-lookup"><span data-stu-id="1287f-121">Select the **Razor Class Library** template.</span></span> <span data-ttu-id="1287f-122">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="1287f-122">Select **Create**.</span></span>
1. <span data-ttu-id="1287f-123">Přidání knihovny tříd Razor k řešení:</span><span class="sxs-lookup"><span data-stu-id="1287f-123">Add the Razor class library to a solution:</span></span>
   1. <span data-ttu-id="1287f-124">Klikněte pravým tlačítkem na řešení.</span><span class="sxs-lookup"><span data-stu-id="1287f-124">Right-click the solution.</span></span> <span data-ttu-id="1287f-125">Vyberte **přidat** > **existující projekt**.</span><span class="sxs-lookup"><span data-stu-id="1287f-125">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="1287f-126">Přejděte k souboru projektu knihovny tříd Razor.</span><span class="sxs-lookup"><span data-stu-id="1287f-126">Navigate to the Razor class library's project file.</span></span>
   1. <span data-ttu-id="1287f-127">Vyberte soubor projektu knihovny tříd Razor (*.csproj*).</span><span class="sxs-lookup"><span data-stu-id="1287f-127">Select the Razor class library's project file (*.csproj*).</span></span>
1. <span data-ttu-id="1287f-128">Přidáte odkaz knihovny tříd Razor z aplikace:</span><span class="sxs-lookup"><span data-stu-id="1287f-128">Add a reference the Razor class library from the app:</span></span>
   1. <span data-ttu-id="1287f-129">Klikněte pravým tlačítkem na projekt aplikace.</span><span class="sxs-lookup"><span data-stu-id="1287f-129">Right-click the app project.</span></span> <span data-ttu-id="1287f-130">Vyberte **přidat** > **odkaz**.</span><span class="sxs-lookup"><span data-stu-id="1287f-130">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="1287f-131">Vyberte projekt knihovny tříd Razor.</span><span class="sxs-lookup"><span data-stu-id="1287f-131">Select the Razor class library project.</span></span> <span data-ttu-id="1287f-132">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="1287f-132">Select **OK**.</span></span>

# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[<span data-ttu-id="1287f-133">Visual Studio Code / .NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="1287f-133">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

1. <span data-ttu-id="1287f-134">Použití knihovny tříd Razor (`razorclasslib`) šablony s [dotnet nové](/dotnet/core/tools/dotnet-new) z příkazové okno.</span><span class="sxs-lookup"><span data-stu-id="1287f-134">Use the Razor class library (`razorclasslib`) template with the [dotnet new](/dotnet/core/tools/dotnet-new) command from a command shell.</span></span> <span data-ttu-id="1287f-135">V následujícím příkladu je vytvořena s názvem knihovny tříd Razor `MyComponentLib1`.</span><span class="sxs-lookup"><span data-stu-id="1287f-135">In the following example, a Razor class library is created named `MyComponentLib1`.</span></span> <span data-ttu-id="1287f-136">Složky obsahující `MyComponentLib1` je vytvořen automaticky při spuštění příkazu.</span><span class="sxs-lookup"><span data-stu-id="1287f-136">The folder that holds `MyComponentLib1` is created automatically when the command is executed.</span></span>

   ```console
   dotnet new razorclasslib -o MyComponentLib1
   ```

1. <span data-ttu-id="1287f-137">Přidání knihovny do existujícího projektu, použijte [se příkaz dotnet add odkaz](/dotnet/core/tools/dotnet-add-reference) z příkazové okno.</span><span class="sxs-lookup"><span data-stu-id="1287f-137">To add the library to an existing project, use the [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) command from a command shell.</span></span> <span data-ttu-id="1287f-138">V následujícím příkladu knihovny tříd Razor se přidá do aplikace.</span><span class="sxs-lookup"><span data-stu-id="1287f-138">In the following example, the Razor class library is added to the app.</span></span> <span data-ttu-id="1287f-139">Spusťte následující příkaz ze složky projektu aplikace s cestou ke knihovně:</span><span class="sxs-lookup"><span data-stu-id="1287f-139">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```console
   dotnet add reference {PATH TO LIBRARY}
   ```

---

<span data-ttu-id="1287f-140">Přidat soubory součástí Razor (*.razor*) do knihovny tříd Razor.</span><span class="sxs-lookup"><span data-stu-id="1287f-140">Add Razor component files (*.razor*) to the Razor class library.</span></span>

## <a name="razor-class-libraries-not-supported-for-client-side-apps"></a><span data-ttu-id="1287f-141">Knihovny tříd Razor není podporována pro aplikace na straně klienta</span><span class="sxs-lookup"><span data-stu-id="1287f-141">Razor class libraries not supported for client-side apps</span></span>

<span data-ttu-id="1287f-142">ASP.NET Core 3.0 ve verzi Preview nejsou kompatibilní s aplikacemi na straně klienta Blazor knihovny tříd Razor.</span><span class="sxs-lookup"><span data-stu-id="1287f-142">In ASP.NET Core 3.0 Preview, Razor class libraries aren't compatible with Blazor client-side apps.</span></span>

<span data-ttu-id="1287f-143">Blazor aplikace na straně klienta, použijte knihovně komponent Blazor vytvořené `blazorlib` šablonu z příkazové prostředí:</span><span class="sxs-lookup"><span data-stu-id="1287f-143">For Blazor client-side apps, use a Blazor component library created by the `blazorlib` template from a command shell:</span></span>

```console
dotnet new blazorlib -o MyComponentLib1
```

<span data-ttu-id="1287f-144">Pomocí knihovny součástí `blazorlib` šablona může obsahovat statické soubory, jako jsou obrázky, JavaScript a šablony stylů.</span><span class="sxs-lookup"><span data-stu-id="1287f-144">Component libraries using the `blazorlib` template can include static files, such as images, JavaScript, and stylesheets.</span></span> <span data-ttu-id="1287f-145">V okamžiku sestavení, statické soubory jsou vloženy do souborů sestavení (*.dll*), která umožňuje využití komponent bez nutnosti starat o tom, jak zahrnout svoje prostředky.</span><span class="sxs-lookup"><span data-stu-id="1287f-145">At build time, static files are embedded into the built assembly file (*.dll*), which allows consumption of the components without having to worry about how to include their resources.</span></span> <span data-ttu-id="1287f-146">Všechny soubory zahrnuté v `content` adresáře jsou označeny jako vložený prostředek.</span><span class="sxs-lookup"><span data-stu-id="1287f-146">Any files included in the `content` directory are marked as an embedded resource.</span></span>

## <a name="static-assets-not-supported-for-server-side-apps"></a><span data-ttu-id="1287f-147">Statické prostředky nejsou podporovány pro aplikace na straně serveru</span><span class="sxs-lookup"><span data-stu-id="1287f-147">Static assets not supported for server-side apps</span></span>

<span data-ttu-id="1287f-148">Ve verzi Preview pro aplikaci ASP.NET Core 3.0 Blazor serverové aplikace moci spotřebovat statické prostředky z obou knihovny tříd Razor (`razorclasslib`) nebo knihovny Blazor (`blazorlib`).</span><span class="sxs-lookup"><span data-stu-id="1287f-148">In ASP.NET Core 3.0 Preview, Blazor server-side apps can't consume static assets from either a Razor class library (`razorclasslib`) or a Blazor library (`blazorlib`).</span></span>

<span data-ttu-id="1287f-149">Jako dočasné řešení, můžete zkusit [BlazorEmbedLibrary](https://www.nuget.org/packages/BlazorEmbedLibrary/).</span><span class="sxs-lookup"><span data-stu-id="1287f-149">As a temporary workaround, you can try [BlazorEmbedLibrary](https://www.nuget.org/packages/BlazorEmbedLibrary/).</span></span>

> [!NOTE]
> <span data-ttu-id="1287f-150">[BlazorEmbedLibrary](https://www.nuget.org/packages/BlazorEmbedLibrary/) není zachována nebo podporovaný společností Microsoft.</span><span class="sxs-lookup"><span data-stu-id="1287f-150">[BlazorEmbedLibrary](https://www.nuget.org/packages/BlazorEmbedLibrary/) isn't maintained or supported by Microsoft.</span></span>

## <a name="consume-a-library-component"></a><span data-ttu-id="1287f-151">Využívat komponentu knihovny</span><span class="sxs-lookup"><span data-stu-id="1287f-151">Consume a library component</span></span>

<span data-ttu-id="1287f-152">Aby bylo možné využívat součásti definované v knihovně v jiném projektu, použijte některou z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="1287f-152">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="1287f-153">Použijte úplný název typu s oborem názvů.</span><span class="sxs-lookup"><span data-stu-id="1287f-153">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="1287f-154">Použití syntaxe Razor pro [ \@pomocí](xref:mvc/views/razor#using) směrnice.</span><span class="sxs-lookup"><span data-stu-id="1287f-154">Use Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="1287f-155">Jednotlivé komponenty mohou být přidány podle názvu.</span><span class="sxs-lookup"><span data-stu-id="1287f-155">Individual components can be added by name.</span></span>

<span data-ttu-id="1287f-156">V následujících příkladech `MyComponentLib1` je součást Knihovna, která obsahuje prodejní sestavu (`SalesReport`) komponenty.</span><span class="sxs-lookup"><span data-stu-id="1287f-156">In the following examples, `MyComponentLib1` is a component library containing a Sales Report (`SalesReport`) component.</span></span>

<span data-ttu-id="1287f-157">Komponenta prodejní sestavu můžete odkazovat pomocí jeho úplný název typu s oborem názvů:</span><span class="sxs-lookup"><span data-stu-id="1287f-157">The Sales Report component can be referenced using its full type name with namespace:</span></span>

```cshtml
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

<span data-ttu-id="1287f-158">Komponentu lze také odkazovat Pokud knihovny je přenesena do rozsahu pomocí `@using` – direktiva:</span><span class="sxs-lookup"><span data-stu-id="1287f-158">The component can also be referenced if the library is brought into scope with an `@using` directive:</span></span>

```cshtml
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

<span data-ttu-id="1287f-159">Zahrnout `@using MyComponentLib1` direktivu na nejvyšší úrovni *_Import.razor* souboru pro zpřístupnění knihovny součásti pro celý projekt.</span><span class="sxs-lookup"><span data-stu-id="1287f-159">Include the `@using MyComponentLib1` directive in the top-level *_Import.razor* file to make the library's components available to an entire project.</span></span> <span data-ttu-id="1287f-160">Přidat direktivu do *_Import.razor* souboru na libovolné úrovni použít obor názvů na jednu stránku nebo sadu stránek ve složce.</span><span class="sxs-lookup"><span data-stu-id="1287f-160">Add the directive to an *_Import.razor* file at any level to apply the namespace to a single page or set of pages within a folder.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="1287f-161">Sestavení, aktualizací Service pack a příjemce pro NuGet</span><span class="sxs-lookup"><span data-stu-id="1287f-161">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="1287f-162">Protože jsou součástí knihovny knihovny .NET standard, balení a je na cestě k NuGet se nijak neliší od balení a přesouvání všechny knihovny nuget.</span><span class="sxs-lookup"><span data-stu-id="1287f-162">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="1287f-163">Balení se provádí pomocí [balíčku dotnet](/dotnet/core/tools/dotnet-pack) z příkazové prostředí:</span><span class="sxs-lookup"><span data-stu-id="1287f-163">Packaging is performed using the [dotnet pack](/dotnet/core/tools/dotnet-pack) command from a command shell:</span></span>

```console
dotnet pack
```

<span data-ttu-id="1287f-164">Nahrání balíčku NuGet pomocí [dotnet nuget publikovat](/dotnet/core/tools/dotnet-nuget-push) z příkazové prostředí:</span><span class="sxs-lookup"><span data-stu-id="1287f-164">Upload the package to NuGet using the [dotnet nuget publish](/dotnet/core/tools/dotnet-nuget-push) command from a command shell:</span></span>

```console
dotnet nuget publish
```

<span data-ttu-id="1287f-165">Při použití `blazorlib` šablonu, statických prostředků jsou obsažené v balíčku NuGet.</span><span class="sxs-lookup"><span data-stu-id="1287f-165">When using the `blazorlib` template, static resources are included in the NuGet package.</span></span> <span data-ttu-id="1287f-166">Příjemci knihovny automaticky obdrží skripty a šablony stylů, takže příjemci nejsou potřeba ručně instalovat prostředky.</span><span class="sxs-lookup"><span data-stu-id="1287f-166">Library consumers automatically receive scripts and stylesheets, so consumers aren't required to manually install the resources.</span></span> <span data-ttu-id="1287f-167">Všimněte si, že [statických prostředků se nepodporují pro aplikace na straně serveru](#static-assets-not-supported-for-server-side-apps), včetně toho, když Blazor knihovny (`blazorlib`) odkazuje na aplikaci na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="1287f-167">Note that [static assets aren't supported for server-side apps](#static-assets-not-supported-for-server-side-apps), including when a Blazor library (`blazorlib`) is referenced by a server-side app.</span></span>
