---
title: RazorKnihovny tříd ASP.NET Core komponenty
author: guardrex
description: Zjistěte, jak mohou být součásti součástí Blazor aplikací z externí knihovny součástí.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/27/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/class-libraries
ms.openlocfilehash: d933a677a063d50fbe708264106e3ce19400a270
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628570"
---
# <a name="aspnet-core-no-locrazor-components-class-libraries"></a><span data-ttu-id="98d40-103">RazorKnihovny tříd ASP.NET Core komponenty</span><span class="sxs-lookup"><span data-stu-id="98d40-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="98d40-104">Od [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="98d40-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="98d40-105">Komponenty lze sdílet v [ Razor knihovně tříd (RCL)](xref:razor-pages/ui-class) napříč projekty.</span><span class="sxs-lookup"><span data-stu-id="98d40-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="98d40-106">\* Razor Knihovna tříd komponent\* může obsahovat:</span><span class="sxs-lookup"><span data-stu-id="98d40-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="98d40-107">Jiný projekt v řešení.</span><span class="sxs-lookup"><span data-stu-id="98d40-107">Another project in the solution.</span></span>
* <span data-ttu-id="98d40-108">Balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="98d40-108">A NuGet package.</span></span>
* <span data-ttu-id="98d40-109">Odkazovaná knihovna .NET.</span><span class="sxs-lookup"><span data-stu-id="98d40-109">A referenced .NET library.</span></span>

<span data-ttu-id="98d40-110">Stejně jako komponenty jsou běžné typy .NET, komponenty poskytované RCL jsou normální sestavení .NET.</span><span class="sxs-lookup"><span data-stu-id="98d40-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="98d40-111">Vytvoření RCL</span><span class="sxs-lookup"><span data-stu-id="98d40-111">Create an RCL</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="98d40-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98d40-112">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="98d40-113">Vytvoření nového projektu</span><span class="sxs-lookup"><span data-stu-id="98d40-113">Create a new project.</span></span>
1. <span data-ttu-id="98d40-114">Vyberte možnost \*\* Razor Knihovna tříd\*\*.</span><span class="sxs-lookup"><span data-stu-id="98d40-114">Select **Razor Class Library**.</span></span> <span data-ttu-id="98d40-115">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="98d40-115">Select **Next**.</span></span>
1. <span data-ttu-id="98d40-116">V dialogovém okně **vytvořit novou Razor knihovnu tříd** vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="98d40-116">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="98d40-117">Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="98d40-117">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="98d40-118">V příkladech v tomto tématu se používá název projektu `ComponentLibrary` .</span><span class="sxs-lookup"><span data-stu-id="98d40-118">The examples in this topic use the project name `ComponentLibrary`.</span></span> <span data-ttu-id="98d40-119">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="98d40-119">Select **Create**.</span></span>
1. <span data-ttu-id="98d40-120">Přidat RCL do řešení:</span><span class="sxs-lookup"><span data-stu-id="98d40-120">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="98d40-121">Klikněte pravým tlačítkem na řešení.</span><span class="sxs-lookup"><span data-stu-id="98d40-121">Right-click the solution.</span></span> <span data-ttu-id="98d40-122">Vyberte **Přidat**  >  **existující projekt**.</span><span class="sxs-lookup"><span data-stu-id="98d40-122">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="98d40-123">Přejděte do souboru projektu RCL.</span><span class="sxs-lookup"><span data-stu-id="98d40-123">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="98d40-124">Vyberte soubor projektu RCL ( `.csproj` ).</span><span class="sxs-lookup"><span data-stu-id="98d40-124">Select the RCL's project file (`.csproj`).</span></span>
1. <span data-ttu-id="98d40-125">Přidejte odkaz na RCL z aplikace:</span><span class="sxs-lookup"><span data-stu-id="98d40-125">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="98d40-126">Klikněte pravým tlačítkem na projekt aplikace.</span><span class="sxs-lookup"><span data-stu-id="98d40-126">Right-click the app project.</span></span> <span data-ttu-id="98d40-127">Vyberte **Přidat**  >  **odkaz**.</span><span class="sxs-lookup"><span data-stu-id="98d40-127">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="98d40-128">Vyberte projekt RCL.</span><span class="sxs-lookup"><span data-stu-id="98d40-128">Select the RCL project.</span></span> <span data-ttu-id="98d40-129">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="98d40-129">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="98d40-130">Pokud je zaškrtnuto políčko **stránky podpory a zobrazení** při generování RCL ze šablony, přidejte také `_Imports.razor` soubor do kořenového adresáře vygenerovaného projektu s následujícím obsahem, který umožní Razor vytváření komponent:</span><span class="sxs-lookup"><span data-stu-id="98d40-130">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="98d40-131">Ručně přidejte soubor do kořenového adresáře vygenerovaného projektu.</span><span class="sxs-lookup"><span data-stu-id="98d40-131">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="98d40-132">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="98d40-132">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="98d40-133">Použijte šablonu \*\* Razor knihovny tříd\*\* ( `razorclasslib` ) s [`dotnet new`](/dotnet/core/tools/dotnet-new) příkazem v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="98d40-133">Use the **Razor Class Library** template (`razorclasslib`) with the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="98d40-134">V následujícím příkladu je vytvořen RCL s názvem `ComponentLibrary` .</span><span class="sxs-lookup"><span data-stu-id="98d40-134">In the following example, an RCL is created named `ComponentLibrary`.</span></span> <span data-ttu-id="98d40-135">Složka, která je uložena, `ComponentLibrary` je vytvořena automaticky při spuštění příkazu:</span><span class="sxs-lookup"><span data-stu-id="98d40-135">The folder that holds `ComponentLibrary` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o ComponentLibrary
   ```

   > [!NOTE]
   > <span data-ttu-id="98d40-136">Pokud `-s|--support-pages-and-views` je použit přepínač při generování RCL ze šablony, pak přidejte `_Imports.razor` soubor do kořenového adresáře vygenerovaného projektu s následujícím obsahem, který umožní Razor vytváření komponent:</span><span class="sxs-lookup"><span data-stu-id="98d40-136">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="98d40-137">Ručně přidejte soubor do kořenového adresáře vygenerovaného projektu.</span><span class="sxs-lookup"><span data-stu-id="98d40-137">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="98d40-138">Chcete-li přidat knihovnu do existujícího projektu, použijte [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) příkaz v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="98d40-138">To add the library to an existing project, use the [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="98d40-139">V následujícím příkladu se RCL přidá do aplikace.</span><span class="sxs-lookup"><span data-stu-id="98d40-139">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="98d40-140">Spusťte následující příkaz ze složky projektu aplikace s cestou ke knihovně:</span><span class="sxs-lookup"><span data-stu-id="98d40-140">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="98d40-141">Využití komponenty knihovny</span><span class="sxs-lookup"><span data-stu-id="98d40-141">Consume a library component</span></span>

<span data-ttu-id="98d40-142">Aby bylo možné využívat komponenty definované v knihovně v jiném projektu, použijte některý z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="98d40-142">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="98d40-143">Použijte úplný název typu s oborem názvů.</span><span class="sxs-lookup"><span data-stu-id="98d40-143">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="98d40-144">Razor [`@using`](xref:mvc/views/razor#using) Direktiva use.</span><span class="sxs-lookup"><span data-stu-id="98d40-144">Use Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="98d40-145">Jednotlivé komponenty lze přidat podle názvu.</span><span class="sxs-lookup"><span data-stu-id="98d40-145">Individual components can be added by name.</span></span>

<span data-ttu-id="98d40-146">V následujících příkladech `ComponentLibrary` je knihovna komponent obsahující `Component1` komponentu ( `Component1.razor` ).</span><span class="sxs-lookup"><span data-stu-id="98d40-146">In the following examples, `ComponentLibrary` is a component library containing the `Component1` component (`Component1.razor`).</span></span> <span data-ttu-id="98d40-147">`Component1`Komponenta je ukázková komponenta, která je automaticky přidána šablonou projektu RCL při vytvoření knihovny.</span><span class="sxs-lookup"><span data-stu-id="98d40-147">The `Component1` component is an example component automatically added by the RCL project template when the library is created.</span></span>

<span data-ttu-id="98d40-148">Odkaz na `Component1` komponentu pomocí jejího oboru názvů:</span><span class="sxs-lookup"><span data-stu-id="98d40-148">Reference the `Component1` component using its namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<ComponentLibrary.Component1 />
```

<span data-ttu-id="98d40-149">Případně přeneste knihovnu do oboru pomocí [`@using`](xref:mvc/views/razor#using) direktivy a použijte komponentu bez jejího oboru názvů:</span><span class="sxs-lookup"><span data-stu-id="98d40-149">Alternatively, bring the library into scope with an [`@using`](xref:mvc/views/razor#using) directive and use the component without its namespace:</span></span>

```razor
@using ComponentLibrary

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

<span data-ttu-id="98d40-150">Volitelně můžete zahrnout `@using ComponentLibrary` direktivu do souboru nejvyšší úrovně `_Import.razor` , aby byly komponenty knihovny k dispozici pro celý projekt.</span><span class="sxs-lookup"><span data-stu-id="98d40-150">Optionally, include the `@using ComponentLibrary` directive in the top-level `_Import.razor` file to make the library's components available to an entire project.</span></span> <span data-ttu-id="98d40-151">Přidejte direktivu do `_Import.razor` souboru na libovolné úrovni pro použití oboru názvů pro jednu komponentu nebo sadu součástí v rámci složky.</span><span class="sxs-lookup"><span data-stu-id="98d40-151">Add the directive to an `_Import.razor` file at any level to apply the namespace to a single component or set of components within a folder.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="98d40-152">Chcete- `Component1` li `my-component` do komponenty poskytnout CSS třídu, odkazujte na šablonu stylů knihovny pomocí [ `Link` komponenty](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) rozhraní v nástroji `Component1.razor` :</span><span class="sxs-lookup"><span data-stu-id="98d40-152">To provide `Component1`'s `my-component` CSS class to the component, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) in `Component1.razor`:</span></span>

```razor
<div class="my-component">
    <Link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />

    <p>
        This Blazor component is defined in the <strong>ComponentLibrary</strong> package.
    </p>
</div>
```

<span data-ttu-id="98d40-153">Pokud chcete šablonu stylů zadat v rámci aplikace, můžete alternativně vytvořit odkaz na šablonu stylů knihovny v souboru aplikace `wwwroot/index.html` ( Blazor WebAssembly ) nebo `Pages/_Host.cshtml` souboru ( Blazor Server ):</span><span class="sxs-lookup"><span data-stu-id="98d40-153">To provide the stylesheet across the app, you can alternatively link to the library's stylesheet in the app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

<span data-ttu-id="98d40-154">Při `Link` použití komponenty v podřízené součásti je propojená Asset také k dispozici libovolné jiné podřízené součásti nadřazené komponenty, pokud `Link` je vykreslena podřízená komponenta.</span><span class="sxs-lookup"><span data-stu-id="98d40-154">When the `Link` component is used in a child component, the linked asset is also available to any other child component of the parent component as long as the child with the `Link` component is rendered.</span></span> <span data-ttu-id="98d40-155">Rozdíl mezi použitím `Link` komponenty v podřízené komponentě a umístěním `<link>` značky HTML v `wwwroot/index.html` nebo `Pages/_Host.cshtml` je, že vykreslená značka HTML komponenty rozhraní:</span><span class="sxs-lookup"><span data-stu-id="98d40-155">The distinction between using the `Link` component in a child component and placing a `<link>` HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:</span></span>

* <span data-ttu-id="98d40-156">Může být upraveno stavem aplikace.</span><span class="sxs-lookup"><span data-stu-id="98d40-156">Can be modified by application state.</span></span> <span data-ttu-id="98d40-157">Pevně kódovaná `<link>` značka HTML nemůže být upravena stavem aplikace.</span><span class="sxs-lookup"><span data-stu-id="98d40-157">A hard-coded `<link>` HTML tag can't be modified by application state.</span></span>
* <span data-ttu-id="98d40-158">Je odebrán z kódu HTML, `<head>` Pokud již není vykreslena nadřazená komponenta.</span><span class="sxs-lookup"><span data-stu-id="98d40-158">Is removed from the HTML `<head>` when the parent component is no longer rendered.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="98d40-159">Chcete-li poskytnout `Component1` `my-component` CSS třídu, odkaz na šablonu stylů knihovny v `wwwroot/index.html` souboru aplikace ( Blazor WebAssembly ) nebo `Pages/_Host.cshtml` souboru ( Blazor Server ):</span><span class="sxs-lookup"><span data-stu-id="98d40-159">To provide `Component1`'s `my-component` CSS class, link to the library's stylesheet in the app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

## <a name="create-a-no-locrazor-components-class-library-with-static-assets"></a><span data-ttu-id="98d40-160">Vytvoření Razor knihovny tříd komponent se statickými prostředky</span><span class="sxs-lookup"><span data-stu-id="98d40-160">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="98d40-161">RCL může zahrnovat statické prostředky.</span><span class="sxs-lookup"><span data-stu-id="98d40-161">An RCL can include static assets.</span></span> <span data-ttu-id="98d40-162">Statické prostředky jsou k dispozici pro všechny aplikace, které knihovnu využívají.</span><span class="sxs-lookup"><span data-stu-id="98d40-162">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="98d40-163">Další informace naleznete v tématu <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="98d40-163">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="supply-components-and-static-assets-to-multiple-hosted-no-locblazor-apps"></a><span data-ttu-id="98d40-164">Dodávání komponent a statických prostředků do více hostovaných Blazor aplikací</span><span class="sxs-lookup"><span data-stu-id="98d40-164">Supply components and static assets to multiple hosted Blazor apps</span></span>

<span data-ttu-id="98d40-165">Další informace naleznete v tématu <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="98d40-165">For more information, see <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="98d40-166">Sestavení, balení a odeslání do NuGet</span><span class="sxs-lookup"><span data-stu-id="98d40-166">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="98d40-167">Knihovny součástí jsou standardní knihovny .NET, balení a jejich odeslání do NuGet se neliší od balení a předává jakékoli knihovny do NuGet.</span><span class="sxs-lookup"><span data-stu-id="98d40-167">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="98d40-168">Balení se provádí pomocí [`dotnet pack`](/dotnet/core/tools/dotnet-pack) příkazu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="98d40-168">Packaging is performed using the [`dotnet pack`](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="98d40-169">Nahrajte balíček do NuGet pomocí [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) příkazu v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="98d40-169">Upload the package to NuGet using the [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="98d40-170">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="98d40-170">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="98d40-171">Přidání konfiguračního souboru linkeru XML do knihovny</span><span class="sxs-lookup"><span data-stu-id="98d40-171">Add an XML linker configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
