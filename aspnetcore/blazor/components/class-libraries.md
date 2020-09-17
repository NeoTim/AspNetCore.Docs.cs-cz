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
ms.openlocfilehash: afd1bfffae11520a5d9abccc1d2ee4cf3a46a4bf
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722459"
---
# <a name="aspnet-core-no-locrazor-components-class-libraries"></a><span data-ttu-id="8ede2-103">RazorKnihovny tříd ASP.NET Core komponenty</span><span class="sxs-lookup"><span data-stu-id="8ede2-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="8ede2-104">Od [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="8ede2-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="8ede2-105">Komponenty lze sdílet v [ Razor knihovně tříd (RCL)](xref:razor-pages/ui-class) napříč projekty.</span><span class="sxs-lookup"><span data-stu-id="8ede2-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="8ede2-106">\* Razor Knihovna tříd komponent\* může obsahovat:</span><span class="sxs-lookup"><span data-stu-id="8ede2-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="8ede2-107">Jiný projekt v řešení.</span><span class="sxs-lookup"><span data-stu-id="8ede2-107">Another project in the solution.</span></span>
* <span data-ttu-id="8ede2-108">Balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="8ede2-108">A NuGet package.</span></span>
* <span data-ttu-id="8ede2-109">Odkazovaná knihovna .NET.</span><span class="sxs-lookup"><span data-stu-id="8ede2-109">A referenced .NET library.</span></span>

<span data-ttu-id="8ede2-110">Stejně jako komponenty jsou běžné typy .NET, komponenty poskytované RCL jsou normální sestavení .NET.</span><span class="sxs-lookup"><span data-stu-id="8ede2-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="8ede2-111">Vytvoření RCL</span><span class="sxs-lookup"><span data-stu-id="8ede2-111">Create an RCL</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8ede2-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8ede2-112">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="8ede2-113">Vytvoření nového projektu</span><span class="sxs-lookup"><span data-stu-id="8ede2-113">Create a new project.</span></span>
1. <span data-ttu-id="8ede2-114">Vyberte možnost \*\* Razor Knihovna tříd\*\*.</span><span class="sxs-lookup"><span data-stu-id="8ede2-114">Select **Razor Class Library**.</span></span> <span data-ttu-id="8ede2-115">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="8ede2-115">Select **Next**.</span></span>
1. <span data-ttu-id="8ede2-116">V dialogovém okně **vytvořit novou Razor knihovnu tříd** vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="8ede2-116">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="8ede2-117">Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="8ede2-117">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="8ede2-118">V příkladech v tomto tématu se používá název projektu `ComponentLibrary` .</span><span class="sxs-lookup"><span data-stu-id="8ede2-118">The examples in this topic use the project name `ComponentLibrary`.</span></span> <span data-ttu-id="8ede2-119">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="8ede2-119">Select **Create**.</span></span>
1. <span data-ttu-id="8ede2-120">Přidat RCL do řešení:</span><span class="sxs-lookup"><span data-stu-id="8ede2-120">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="8ede2-121">Klikněte pravým tlačítkem na řešení.</span><span class="sxs-lookup"><span data-stu-id="8ede2-121">Right-click the solution.</span></span> <span data-ttu-id="8ede2-122">Vyberte **Přidat**  >  **existující projekt**.</span><span class="sxs-lookup"><span data-stu-id="8ede2-122">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="8ede2-123">Přejděte do souboru projektu RCL.</span><span class="sxs-lookup"><span data-stu-id="8ede2-123">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="8ede2-124">Vyberte soubor projektu RCL ( `.csproj` ).</span><span class="sxs-lookup"><span data-stu-id="8ede2-124">Select the RCL's project file (`.csproj`).</span></span>
1. <span data-ttu-id="8ede2-125">Přidejte odkaz na RCL z aplikace:</span><span class="sxs-lookup"><span data-stu-id="8ede2-125">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="8ede2-126">Klikněte pravým tlačítkem na projekt aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ede2-126">Right-click the app project.</span></span> <span data-ttu-id="8ede2-127">Vyberte **Přidat**  >  **odkaz**.</span><span class="sxs-lookup"><span data-stu-id="8ede2-127">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="8ede2-128">Vyberte projekt RCL.</span><span class="sxs-lookup"><span data-stu-id="8ede2-128">Select the RCL project.</span></span> <span data-ttu-id="8ede2-129">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="8ede2-129">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="8ede2-130">Pokud je zaškrtnuto políčko **stránky podpory a zobrazení** při generování RCL ze šablony, přidejte také `_Imports.razor` soubor do kořenového adresáře vygenerovaného projektu s následujícím obsahem, který umožní Razor vytváření komponent:</span><span class="sxs-lookup"><span data-stu-id="8ede2-130">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="8ede2-131">Ručně přidejte soubor do kořenového adresáře vygenerovaného projektu.</span><span class="sxs-lookup"><span data-stu-id="8ede2-131">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="8ede2-132">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="8ede2-132">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="8ede2-133">Použijte šablonu \*\* Razor knihovny tříd\*\* ( `razorclasslib` ) s [`dotnet new`](/dotnet/core/tools/dotnet-new) příkazem v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="8ede2-133">Use the **Razor Class Library** template (`razorclasslib`) with the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="8ede2-134">V následujícím příkladu je vytvořen RCL s názvem `ComponentLibrary` .</span><span class="sxs-lookup"><span data-stu-id="8ede2-134">In the following example, an RCL is created named `ComponentLibrary`.</span></span> <span data-ttu-id="8ede2-135">Složka, která je uložena, `ComponentLibrary` je vytvořena automaticky při spuštění příkazu:</span><span class="sxs-lookup"><span data-stu-id="8ede2-135">The folder that holds `ComponentLibrary` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o ComponentLibrary
   ```

   > [!NOTE]
   > <span data-ttu-id="8ede2-136">Pokud `-s|--support-pages-and-views` je použit přepínač při generování RCL ze šablony, pak přidejte `_Imports.razor` soubor do kořenového adresáře vygenerovaného projektu s následujícím obsahem, který umožní Razor vytváření komponent:</span><span class="sxs-lookup"><span data-stu-id="8ede2-136">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="8ede2-137">Ručně přidejte soubor do kořenového adresáře vygenerovaného projektu.</span><span class="sxs-lookup"><span data-stu-id="8ede2-137">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="8ede2-138">Chcete-li přidat knihovnu do existujícího projektu, použijte [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) příkaz v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="8ede2-138">To add the library to an existing project, use the [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="8ede2-139">V následujícím příkladu se RCL přidá do aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ede2-139">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="8ede2-140">Spusťte následující příkaz ze složky projektu aplikace s cestou ke knihovně:</span><span class="sxs-lookup"><span data-stu-id="8ede2-140">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="8ede2-141">Využití komponenty knihovny</span><span class="sxs-lookup"><span data-stu-id="8ede2-141">Consume a library component</span></span>

<span data-ttu-id="8ede2-142">Aby bylo možné využívat komponenty definované v knihovně v jiném projektu, použijte některý z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="8ede2-142">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="8ede2-143">Použijte úplný název typu s oborem názvů.</span><span class="sxs-lookup"><span data-stu-id="8ede2-143">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="8ede2-144">Razor [`@using`](xref:mvc/views/razor#using) Direktiva use.</span><span class="sxs-lookup"><span data-stu-id="8ede2-144">Use Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="8ede2-145">Jednotlivé komponenty lze přidat podle názvu.</span><span class="sxs-lookup"><span data-stu-id="8ede2-145">Individual components can be added by name.</span></span>

<span data-ttu-id="8ede2-146">V následujících příkladech `ComponentLibrary` je knihovna komponent obsahující `Component1` komponentu ( `Component1.razor` ).</span><span class="sxs-lookup"><span data-stu-id="8ede2-146">In the following examples, `ComponentLibrary` is a component library containing the `Component1` component (`Component1.razor`).</span></span> <span data-ttu-id="8ede2-147">`Component1`Komponenta je ukázková komponenta, která je automaticky přidána šablonou projektu RCL při vytvoření knihovny.</span><span class="sxs-lookup"><span data-stu-id="8ede2-147">The `Component1` component is an example component automatically added by the RCL project template when the library is created.</span></span>

<span data-ttu-id="8ede2-148">Odkaz na `Component1` komponentu pomocí jejího oboru názvů:</span><span class="sxs-lookup"><span data-stu-id="8ede2-148">Reference the `Component1` component using its namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<ComponentLibrary.Component1 />
```

<span data-ttu-id="8ede2-149">Případně přeneste knihovnu do oboru pomocí [`@using`](xref:mvc/views/razor#using) direktivy a použijte komponentu bez jejího oboru názvů:</span><span class="sxs-lookup"><span data-stu-id="8ede2-149">Alternatively, bring the library into scope with an [`@using`](xref:mvc/views/razor#using) directive and use the component without its namespace:</span></span>

```razor
@using ComponentLibrary

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

<span data-ttu-id="8ede2-150">Volitelně můžete zahrnout `@using ComponentLibrary` direktivu do souboru nejvyšší úrovně `_Import.razor` , aby byly komponenty knihovny k dispozici pro celý projekt.</span><span class="sxs-lookup"><span data-stu-id="8ede2-150">Optionally, include the `@using ComponentLibrary` directive in the top-level `_Import.razor` file to make the library's components available to an entire project.</span></span> <span data-ttu-id="8ede2-151">Přidejte direktivu do `_Import.razor` souboru na libovolné úrovni pro použití oboru názvů pro jednu komponentu nebo sadu součástí v rámci složky.</span><span class="sxs-lookup"><span data-stu-id="8ede2-151">Add the directive to an `_Import.razor` file at any level to apply the namespace to a single component or set of components within a folder.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="8ede2-152">Chcete- `Component1` li `my-component` do komponenty poskytnout CSS třídu, odkazujte na šablonu stylů knihovny pomocí [ `Link` komponenty](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) rozhraní v nástroji `Component1.razor` :</span><span class="sxs-lookup"><span data-stu-id="8ede2-152">To provide `Component1`'s `my-component` CSS class to the component, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) in `Component1.razor`:</span></span>

```razor
<div class="my-component">
    <Link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />

    <p>
        This Blazor component is defined in the <strong>ComponentLibrary</strong> package.
    </p>
</div>
```

<span data-ttu-id="8ede2-153">Pokud chcete šablonu stylů zadat v rámci aplikace, můžete alternativně vytvořit odkaz na šablonu stylů knihovny v souboru aplikace `wwwroot/index.html` ( Blazor WebAssembly ) nebo `Pages/_Host.cshtml` souboru ( Blazor Server ):</span><span class="sxs-lookup"><span data-stu-id="8ede2-153">To provide the stylesheet across the app, you can alternatively link to the library's stylesheet in the app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

<span data-ttu-id="8ede2-154">Při `Link` použití komponenty v podřízené součásti je propojená Asset také k dispozici libovolné jiné podřízené součásti nadřazené komponenty, pokud `Link` je vykreslena podřízená komponenta.</span><span class="sxs-lookup"><span data-stu-id="8ede2-154">When the `Link` component is used in a child component, the linked asset is also available to any other child component of the parent component as long as the child with the `Link` component is rendered.</span></span> <span data-ttu-id="8ede2-155">Rozdíl mezi použitím `Link` komponenty v podřízené komponentě a umístěním `<link>` značky HTML v `wwwroot/index.html` nebo `Pages/_Host.cshtml` je, že vykreslená značka HTML komponenty rozhraní:</span><span class="sxs-lookup"><span data-stu-id="8ede2-155">The distinction between using the `Link` component in a child component and placing a `<link>` HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:</span></span>

* <span data-ttu-id="8ede2-156">Může být upraveno stavem aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ede2-156">Can be modified by application state.</span></span> <span data-ttu-id="8ede2-157">Pevně kódovaná `<link>` značka HTML nemůže být upravena stavem aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ede2-157">A hard-coded `<link>` HTML tag can't be modified by application state.</span></span>
* <span data-ttu-id="8ede2-158">Je odebrán z kódu HTML, `<head>` Pokud již není vykreslena nadřazená komponenta.</span><span class="sxs-lookup"><span data-stu-id="8ede2-158">Is removed from the HTML `<head>` when the parent component is no longer rendered.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="8ede2-159">Chcete-li poskytnout `Component1` `my-component` CSS třídu, odkaz na šablonu stylů knihovny v `wwwroot/index.html` souboru aplikace ( Blazor WebAssembly ) nebo `Pages/_Host.cshtml` souboru ( Blazor Server ):</span><span class="sxs-lookup"><span data-stu-id="8ede2-159">To provide `Component1`'s `my-component` CSS class, link to the library's stylesheet in the app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

## <a name="create-a-no-locrazor-components-class-library-with-static-assets"></a><span data-ttu-id="8ede2-160">Vytvoření Razor knihovny tříd komponent se statickými prostředky</span><span class="sxs-lookup"><span data-stu-id="8ede2-160">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="8ede2-161">RCL může zahrnovat statické prostředky.</span><span class="sxs-lookup"><span data-stu-id="8ede2-161">An RCL can include static assets.</span></span> <span data-ttu-id="8ede2-162">Statické prostředky jsou k dispozici pro všechny aplikace, které knihovnu využívají.</span><span class="sxs-lookup"><span data-stu-id="8ede2-162">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="8ede2-163">Další informace naleznete v tématu <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="8ede2-163">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="supply-components-and-static-assets-to-multiple-hosted-no-locblazor-apps"></a><span data-ttu-id="8ede2-164">Dodávání komponent a statických prostředků do více hostovaných Blazor aplikací</span><span class="sxs-lookup"><span data-stu-id="8ede2-164">Supply components and static assets to multiple hosted Blazor apps</span></span>

<span data-ttu-id="8ede2-165">Další informace naleznete v tématu <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="8ede2-165">For more information, see <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="browser-compatibility-analyzer-for-no-locblazor-webassembly"></a><span data-ttu-id="8ede2-166">Analyzátor kompatibility prohlížeče pro Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="8ede2-166">Browser compatibility analyzer for Blazor WebAssembly</span></span>

<span data-ttu-id="8ede2-167">Blazor WebAssembly aplikace cílí na úplnou oblast rozhraní .NET API, ale ne všechna rozhraní API .NET jsou podporovaná ve webovém sestavení vzhledem k omezením izolovaného prostoru (sandbox) prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="8ede2-167">Blazor WebAssembly apps target the full .NET API surface area, but not all .NET APIs are supported on WebAssembly due to browser sandbox constraints.</span></span> <span data-ttu-id="8ede2-168">Nepodporovaná rozhraní API se vyvolávají <xref:System.PlatformNotSupportedException> při spuštění na WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="8ede2-168">Unsupported APIs throw <xref:System.PlatformNotSupportedException> when running on WebAssembly.</span></span> <span data-ttu-id="8ede2-169">Analyzátor kompatibility platforem upozorní vývojáře, když aplikace používá rozhraní API, která nejsou podporovaná cílovými platformami aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ede2-169">A platform compatibility analyzer warns the developer when the app uses APIs that aren't supported by the app's target platforms.</span></span> <span data-ttu-id="8ede2-170">U Blazor WebAssembly aplikací to znamená, že se v prohlížečích podporují rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="8ede2-170">For Blazor WebAssembly apps, this means checking that APIs are supported in browsers.</span></span> <span data-ttu-id="8ede2-171">Přidání poznámek k rozhraním API .NET Framework pro analyzátor kompatibility je probíhající proces, takže ne všechna rozhraní .NET Framework API jsou momentálně Poznáma.</span><span class="sxs-lookup"><span data-stu-id="8ede2-171">Annotating .NET framework APIs for the compatibility analyzer is an on-going process, so not all .NET framework API is currently annotated.</span></span>

<span data-ttu-id="8ede2-172">Blazor WebAssemblyRazorprojekty knihovny tříd a *automaticky* umožňují compatibilty kontroly prohlížeče přidáním `browser` jako podporované platformy s `SupportedPlatform` položkou MSBuild.</span><span class="sxs-lookup"><span data-stu-id="8ede2-172">Blazor WebAssembly and Razor class library projects *automatically* enable browser compatibilty checks by adding `browser` as a supported platform with the `SupportedPlatform` MSBuild item.</span></span> <span data-ttu-id="8ede2-173">Vývojáři knihovny mohou ručně přidat `SupportedPlatform` položku do souboru projektu knihovny a povolit tak funkci:</span><span class="sxs-lookup"><span data-stu-id="8ede2-173">Library developers can manually add the `SupportedPlatform` item to a library's project file to enable the feature:</span></span>

```xml
<ItemGroup>
  <SupportedPlatform Include="browser" />
</ItemGroup>
```

<span data-ttu-id="8ede2-174">Při vytváření knihovny je nutné určit, že konkrétní rozhraní API není v prohlížečích podporováno zadáním `browser` <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute> :</span><span class="sxs-lookup"><span data-stu-id="8ede2-174">When authoring a library, indicate that a particular API isn't supported in browsers by specifying `browser` to <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute>:</span></span>

```csharp
[UnsupportedOSPlatform("browser")]
private static string GetLoggingDirectory()
{
    ...
}
```

<span data-ttu-id="8ede2-175">Další informace najdete v tématu věnovaném vytváření [poznámek na rozhraních API na konkrétních platformách (dotnet/Designs v úložišti GitHubu)](https://github.com/dotnet/designs/blob/main/accepted/2020/platform-exclusion/platform-exclusion.md#build-configuration-for-platforms).</span><span class="sxs-lookup"><span data-stu-id="8ede2-175">For more information, see [Annotating APIs as unsupported on specific platforms (dotnet/designs GitHub repository](https://github.com/dotnet/designs/blob/main/accepted/2020/platform-exclusion/platform-exclusion.md#build-configuration-for-platforms).</span></span>

## <a name="no-locblazor-javascript-isolation-and-object-references"></a><span data-ttu-id="8ede2-176">Blazor JavaScript – izolace a odkazy na objekty</span><span class="sxs-lookup"><span data-stu-id="8ede2-176">Blazor JavaScript isolation and object references</span></span>

<span data-ttu-id="8ede2-177">Blazor povoluje izolaci JavaScriptu ve standardních [modulech JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span><span class="sxs-lookup"><span data-stu-id="8ede2-177">Blazor enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="8ede2-178">Izolace JavaScriptu nabízí následující výhody:</span><span class="sxs-lookup"><span data-stu-id="8ede2-178">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="8ede2-179">Importovaný JavaScript již neznečišťující globální obor názvů.</span><span class="sxs-lookup"><span data-stu-id="8ede2-179">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="8ede2-180">Příjemci knihovny a součásti nejsou vyžadováni k ručnímu importování souvisejícího JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="8ede2-180">Consumers of the library and components aren't required to manually import the related JavaScript.</span></span>

<span data-ttu-id="8ede2-181">Další informace naleznete v tématu <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.</span><span class="sxs-lookup"><span data-stu-id="8ede2-181">For more information, see <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.</span></span>

::: moniker-end

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="8ede2-182">Sestavení, balení a odeslání do NuGet</span><span class="sxs-lookup"><span data-stu-id="8ede2-182">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="8ede2-183">Knihovny součástí jsou standardní knihovny .NET, balení a jejich odeslání do NuGet se neliší od balení a předává jakékoli knihovny do NuGet.</span><span class="sxs-lookup"><span data-stu-id="8ede2-183">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="8ede2-184">Balení se provádí pomocí [`dotnet pack`](/dotnet/core/tools/dotnet-pack) příkazu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="8ede2-184">Packaging is performed using the [`dotnet pack`](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="8ede2-185">Nahrajte balíček do NuGet pomocí [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) příkazu v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="8ede2-185">Upload the package to NuGet using the [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8ede2-186">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="8ede2-186">Additional resources</span></span>

::: moniker range=">= aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="8ede2-187">Přidat konfigurační soubor trávníku pro převodní jazyk (IL) XML do knihovny</span><span class="sxs-lookup"><span data-stu-id="8ede2-187">Add an XML Intermediate Language (IL) Trimmer configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-trimmer)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="8ede2-188">Přidat konfigurační soubor linkeru jazyka XML Intermediate Language (IL) do knihovny</span><span class="sxs-lookup"><span data-stu-id="8ede2-188">Add an XML Intermediate Language (IL) Linker configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)

::: moniker-end
