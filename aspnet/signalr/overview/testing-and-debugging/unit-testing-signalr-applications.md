---
uid: signalr/overview/testing-and-debugging/unit-testing-signalr-applications
title: Testování aplikace SignalR | Dokumentace Microsoftu
author: bradygaster
description: Tento článek popisuje, jak používat funkce testování částí 2.0 SignalR.
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: d1983524-e0d5-4ee6-9d87-1f552f7cb964
msc.legacyurl: /signalr/overview/testing-and-debugging/unit-testing-signalr-applications
msc.type: authoredcontent
ms.openlocfilehash: cb4eb25aeedfe31ac2606de9fe7d280eb95ce2e6
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836035"
---
<a name="unit-testing-signalr-applications"></a><span data-ttu-id="26d15-103">Testování jednotek aplikace knihovnou SignalR</span><span class="sxs-lookup"><span data-stu-id="26d15-103">Unit Testing SignalR Applications</span></span>
====================
<span data-ttu-id="26d15-104">podle [Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="26d15-104">by [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="26d15-105">Tento článek popisuje použití funkce SignalR 2 testování částí.</span><span class="sxs-lookup"><span data-stu-id="26d15-105">This article describes using the Unit Testing features of SignalR 2.</span></span>
>
> ## <a name="software-versions-used-in-this-topic"></a><span data-ttu-id="26d15-106">Verze softwaru použitým v tomto tématu</span><span class="sxs-lookup"><span data-stu-id="26d15-106">Software versions used in this topic</span></span>
>
>
> - [<span data-ttu-id="26d15-107">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="26d15-107">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="26d15-108">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="26d15-108">.NET 4.5</span></span>
> - <span data-ttu-id="26d15-109">Funkce SignalR verze 2</span><span class="sxs-lookup"><span data-stu-id="26d15-109">SignalR version 2</span></span>
>
>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="26d15-110">Otázky a komentáře</span><span class="sxs-lookup"><span data-stu-id="26d15-110">Questions and comments</span></span>
>
> <span data-ttu-id="26d15-111">Napište prosím zpětnou vazbu o tom, jak vám líbilo v tomto kurzu a co můžeme zlepšit v komentářích v dolní části stránky.</span><span class="sxs-lookup"><span data-stu-id="26d15-111">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="26d15-112">Pokud máte nějaké otázky, které přímo nesouvisejí, najdete v tomto kurzu, můžete je publikovat [fórum ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) nebo [StackOverflow.com](http://stackoverflow.com/).</span><span class="sxs-lookup"><span data-stu-id="26d15-112">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>


<a id="unit"></a>
## <a name="unit-testing-signalr-applications"></a><span data-ttu-id="26d15-113">Testování aplikace knihovnou SignalR</span><span class="sxs-lookup"><span data-stu-id="26d15-113">Unit testing SignalR applications</span></span>

<span data-ttu-id="26d15-114">Funkce testování částí v SignalR 2 slouží k vytvoření testů jednotek pro aplikace SignalR.</span><span class="sxs-lookup"><span data-stu-id="26d15-114">You can use the unit test features in SignalR 2 to create unit tests for your SignalR application.</span></span> <span data-ttu-id="26d15-115">Funkce SignalR 2 zahrnuje [IHubCallerConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.ihubcallerconnectioncontext(v=vs.118).aspx) rozhraní, které lze použít k vytvoření mock objektu k simulaci vaší metod rozbočovače pro testování.</span><span class="sxs-lookup"><span data-stu-id="26d15-115">SignalR 2 includes the [IHubCallerConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.ihubcallerconnectioncontext(v=vs.118).aspx) interface, which can be used to create a mock object to simulate your hub methods for testing.</span></span>

<span data-ttu-id="26d15-116">V této části přidáte testů jednotek pro aplikace vytvořené v [kurzu Začínáme](../getting-started/tutorial-getting-started-with-signalr.md) pomocí [XUnit.net](https://github.com/xunit/xunit) a [Moq](https://github.com/Moq/moq4).</span><span class="sxs-lookup"><span data-stu-id="26d15-116">In this section, you'll add unit tests for the application created in the [Getting Started tutorial](../getting-started/tutorial-getting-started-with-signalr.md) using [XUnit.net](https://github.com/xunit/xunit) and [Moq](https://github.com/Moq/moq4).</span></span>

<span data-ttu-id="26d15-117">XUnit.net se dá používat k ovládání testu; Moq se použije k vytvoření [napodobení](http://en.wikipedia.org/wiki/Mock_object) objektu pro testování.</span><span class="sxs-lookup"><span data-stu-id="26d15-117">XUnit.net will be used to control the test; Moq will be used to create a [mock](http://en.wikipedia.org/wiki/Mock_object) object for testing.</span></span> <span data-ttu-id="26d15-118">Další napodobování architektury je možné v případě potřeby; [NSubstitute](http://nsubstitute.github.io/) je také vhodná.</span><span class="sxs-lookup"><span data-stu-id="26d15-118">Other mocking frameworks can be used if desired; [NSubstitute](http://nsubstitute.github.io/) is also a good choice.</span></span> <span data-ttu-id="26d15-119">Tento kurz ukazuje, jak nastavit mock objektu dvěma způsoby: Nejprve pomocí `dynamic` objektu (představíme v rozhraní .NET Framework 4) a druhý, pomocí rozhraní.</span><span class="sxs-lookup"><span data-stu-id="26d15-119">This tutorial demonstrates how to set up the mock object in two ways: First, using a `dynamic` object (introduced in .NET Framework 4), and second, using an interface.</span></span>

### <a name="contents"></a><span data-ttu-id="26d15-120">Obsah</span><span class="sxs-lookup"><span data-stu-id="26d15-120">Contents</span></span>

<span data-ttu-id="26d15-121">Tento kurz obsahuje následující části.</span><span class="sxs-lookup"><span data-stu-id="26d15-121">This tutorial contains the following sections.</span></span>

- [<span data-ttu-id="26d15-122">Testování částí pomocí dynamické</span><span class="sxs-lookup"><span data-stu-id="26d15-122">Unit testing with Dynamic</span></span>](#dynamic)
- [<span data-ttu-id="26d15-123">Podle typu testování částí</span><span class="sxs-lookup"><span data-stu-id="26d15-123">Unit testing by type</span></span>](#type)

<a id="dynamic"></a>
### <a name="unit-testing-with-dynamic"></a><span data-ttu-id="26d15-124">Testování částí pomocí dynamické</span><span class="sxs-lookup"><span data-stu-id="26d15-124">Unit testing with Dynamic</span></span>

<span data-ttu-id="26d15-125">V této části přidáte testování částí pro aplikace vytvořené v [kurzu Začínáme](../getting-started/tutorial-getting-started-with-signalr.md) používání dynamických objektů.</span><span class="sxs-lookup"><span data-stu-id="26d15-125">In this section, you'll add a unit test for the application created in the [Getting Started tutorial](../getting-started/tutorial-getting-started-with-signalr.md) using a dynamic object.</span></span>

1. <span data-ttu-id="26d15-126">Nainstalujte [XUnit Runner rozšíření](https://visualstudiogallery.msdn.microsoft.com/463c5987-f82b-46c8-a97e-b1cde42b9099) pro Visual Studio 2013.</span><span class="sxs-lookup"><span data-stu-id="26d15-126">Install the [XUnit Runner extension](https://visualstudiogallery.msdn.microsoft.com/463c5987-f82b-46c8-a97e-b1cde42b9099) for Visual Studio 2013.</span></span>
2. <span data-ttu-id="26d15-127">Dokončit [kurzu Začínáme](../getting-started/tutorial-getting-started-with-signalr.md), nebo stáhnout hotovou aplikaci [galerie kódů MSDN](https://code.msdn.microsoft.com/SignalR-Getting-Started-b9d18aa9).</span><span class="sxs-lookup"><span data-stu-id="26d15-127">Either complete the [Getting Started tutorial](../getting-started/tutorial-getting-started-with-signalr.md), or download the completed application from [MSDN Code Gallery](https://code.msdn.microsoft.com/SignalR-Getting-Started-b9d18aa9).</span></span>
3. <span data-ttu-id="26d15-128">Pokud používáte verzi aplikace, jak začít, otevřete **Konzola správce balíčků** a klikněte na tlačítko **obnovení** přidat do projektu balíček SignalR.</span><span class="sxs-lookup"><span data-stu-id="26d15-128">If you are using the download version of the Getting Started application, open **Package Manager Console** and click **Restore** to add the SignalR package to the project.</span></span>

    ![Obnovení balíčků](unit-testing-signalr-applications/_static/image1.png)
4. <span data-ttu-id="26d15-130">Přidáte projekt do řešení pro testování částí.</span><span class="sxs-lookup"><span data-stu-id="26d15-130">Add a project to the solution for the unit test.</span></span> <span data-ttu-id="26d15-131">Klikněte pravým tlačítkem na řešení v **Průzkumníka řešení** a vyberte **přidat**, **nový projekt...** . V části **jazyka C#** uzlu, vyberte **Windows** uzlu.</span><span class="sxs-lookup"><span data-stu-id="26d15-131">Right-click your solution in **Solution Explorer** and select **Add**, **New Project...**. Under the **C#** node, select the **Windows** node.</span></span> <span data-ttu-id="26d15-132">Vyberte **knihovna tříd**.</span><span class="sxs-lookup"><span data-stu-id="26d15-132">Select **Class Library**.</span></span> <span data-ttu-id="26d15-133">Název nového projektu **TestLibrary** a klikněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="26d15-133">Name the new project **TestLibrary** and click **OK**.</span></span>

    ![Vytvořte knihovnu testu](unit-testing-signalr-applications/_static/image2.png)
5. <span data-ttu-id="26d15-135">Přidáte odkaz v testovém projektu knihovny do projektu SignalRChat.</span><span class="sxs-lookup"><span data-stu-id="26d15-135">Add a reference in the test library project to the SignalRChat project.</span></span> <span data-ttu-id="26d15-136">Klikněte pravým tlačítkem myši **TestLibrary** projektu a vyberte **přidat**, **odkaz...** . Vyberte **projekty** pod uzlem **řešení** uzlu a kontrola **SignalRChat**.</span><span class="sxs-lookup"><span data-stu-id="26d15-136">Right-click the **TestLibrary** project and select **Add**, **Reference...**. Select the **Projects** node under the **Solution** node, and check **SignalRChat**.</span></span> <span data-ttu-id="26d15-137">Klikněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="26d15-137">Click **OK**.</span></span>

    ![Přidat odkaz na projekt](unit-testing-signalr-applications/_static/image3.png)
6. <span data-ttu-id="26d15-139">Přidat balíčky SignalR, Moq a XUnit **TestLibrary** projektu.</span><span class="sxs-lookup"><span data-stu-id="26d15-139">Add the SignalR, Moq, and XUnit packages to the **TestLibrary** project.</span></span> <span data-ttu-id="26d15-140">V **Konzola správce balíčků**, nastavte **výchozí projekt** rozevírací nabídku, která **TestLibrary**.</span><span class="sxs-lookup"><span data-stu-id="26d15-140">In the **Package Manager Console**, set the **Default Project** dropdown to **TestLibrary**.</span></span> <span data-ttu-id="26d15-141">V okně konzoly spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="26d15-141">Run the following commands in the console window:</span></span>

   - `Install-Package Microsoft.AspNet.SignalR`
   - `Install-Package Moq`
   - `Install-Package XUnit`

     ![Instalace balíčků](unit-testing-signalr-applications/_static/image4.png)
7. <span data-ttu-id="26d15-143">Vytvoření testovacího souboru.</span><span class="sxs-lookup"><span data-stu-id="26d15-143">Create the test file.</span></span> <span data-ttu-id="26d15-144">Klikněte pravým tlačítkem myši **TestLibrary** projektu a klikněte na tlačítko **přidat...** , **Třídy**.</span><span class="sxs-lookup"><span data-stu-id="26d15-144">Right-click the **TestLibrary** project and click **Add...**, **Class**.</span></span> <span data-ttu-id="26d15-145">Pojmenujte novou třídu **Tests.cs**.</span><span class="sxs-lookup"><span data-stu-id="26d15-145">Name the new class **Tests.cs**.</span></span>
8. <span data-ttu-id="26d15-146">Nahraďte obsah Tests.cs následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="26d15-146">Replace the contents of Tests.cs with the following code.</span></span>

    [!code-csharp[Main](unit-testing-signalr-applications/samples/sample1.cs)]

    <span data-ttu-id="26d15-147">Ve výše uvedeném kódu, je vytvořen pomocí testovacího klienta `Mock` objektu z [Moq](https://github.com/Moq/moq4) knihovny typu [IHubCallerConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.ihubcallerconnectioncontext(v=vs.118).aspx) (v SignalR 2.1 přiřadit `dynamic` pro typ parametr.) `IHubCallerConnectionContext` Rozhraní je objekt proxy serveru, pomocí kterého můžete volat metody na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="26d15-147">In the code above, a test client is created using the `Mock` object from the [Moq](https://github.com/Moq/moq4) library, of type [IHubCallerConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.ihubcallerconnectioncontext(v=vs.118).aspx) (in SignalR 2.1, assign `dynamic` for the type parameter.) The `IHubCallerConnectionContext` interface is the proxy object with which you invoke methods on the client.</span></span> <span data-ttu-id="26d15-148">`broadcastMessage` Funkce potom definovaná pro mock klienta tak, že je možné vyvolat v `ChatHub` třídy.</span><span class="sxs-lookup"><span data-stu-id="26d15-148">The `broadcastMessage` function is then defined for the mock client so that it can be called by the `ChatHub` class.</span></span> <span data-ttu-id="26d15-149">Modul testu pak zavolá `Send` metodu `ChatHub` třídu, která volá imitaci `broadcastMessage` funkce.</span><span class="sxs-lookup"><span data-stu-id="26d15-149">The test engine then calls the `Send` method of the `ChatHub` class, which in turn calls the mocked `broadcastMessage` function.</span></span>
9. <span data-ttu-id="26d15-150">Sestavte řešení stisknutím kombinace kláves **F6**.</span><span class="sxs-lookup"><span data-stu-id="26d15-150">Build the solution by pressing **F6**.</span></span>
10. <span data-ttu-id="26d15-151">Spusťte Jednotkový test.</span><span class="sxs-lookup"><span data-stu-id="26d15-151">Run the unit test.</span></span> <span data-ttu-id="26d15-152">V sadě Visual Studio, vyberte **testovací**, **Windows**, **Průzkumník testů**.</span><span class="sxs-lookup"><span data-stu-id="26d15-152">In Visual Studio, select **Test**, **Windows**, **Test Explorer**.</span></span> <span data-ttu-id="26d15-153">V okně Průzkumník testů, klikněte pravým tlačítkem na **HubsAreMockableViaDynamic** a vyberte **spustit vybrané testy**.</span><span class="sxs-lookup"><span data-stu-id="26d15-153">In the Test Explorer window, right-click **HubsAreMockableViaDynamic** and select **Run Selected Tests**.</span></span>

    ![Průzkumník testů](unit-testing-signalr-applications/_static/image5.png)
11. <span data-ttu-id="26d15-155">Ověřte, že test proběhl úspěšně kontrolou dolním podokně, v okně Průzkumníka testů.</span><span class="sxs-lookup"><span data-stu-id="26d15-155">Verify that the test passed by checking the lower pane in the Test Explorer window.</span></span> <span data-ttu-id="26d15-156">V okně se zobrazí, že test proběhl úspěšně.</span><span class="sxs-lookup"><span data-stu-id="26d15-156">The window will show that the test passed.</span></span>

    ![Test proběhl úspěšně](unit-testing-signalr-applications/_static/image6.png)

<a id="type"></a>
### <a name="unit-testing-by-type"></a><span data-ttu-id="26d15-158">Podle typu testování částí</span><span class="sxs-lookup"><span data-stu-id="26d15-158">Unit testing by type</span></span>

<span data-ttu-id="26d15-159">V této části přidáte testu pro aplikaci vytvořenou v [kurzu Začínáme](../getting-started/tutorial-getting-started-with-signalr.md) pomocí rozhraní, který obsahuje metodu, která má být testována.</span><span class="sxs-lookup"><span data-stu-id="26d15-159">In this section, you'll add a test for the application created in the [Getting Started tutorial](../getting-started/tutorial-getting-started-with-signalr.md) using an interface that contains the method to be tested.</span></span>

1. <span data-ttu-id="26d15-160">Proveďte kroky 1 až 7 [testování částí pomocí dynamické](#dynamic) kurzu výše.</span><span class="sxs-lookup"><span data-stu-id="26d15-160">Complete steps 1-7 in the [Unit testing with Dynamic](#dynamic) tutorial above.</span></span>
2. <span data-ttu-id="26d15-161">Nahraďte obsah Tests.cs následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="26d15-161">Replace the contents of Tests.cs with the following code.</span></span>

    [!code-csharp[Main](unit-testing-signalr-applications/samples/sample2.cs)]

    <span data-ttu-id="26d15-162">Ve výše uvedeném kódu, se vytvoří rozhraní definuje podpis metody `broadcastMessage` metodu, pro kterou se vytvoří testovací stroj mock klienta.</span><span class="sxs-lookup"><span data-stu-id="26d15-162">In the code above, an interface is created defining the signature of the `broadcastMessage` method for which the test engine will create a mock client.</span></span> <span data-ttu-id="26d15-163">Mock klienta se pak vytvoří pomocí `Mock` objekt typu [IHubCallerConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.ihubcallerconnectioncontext(v=vs.118).aspx) (v SignalR 2.1 přiřadit `dynamic` pro parametr typu.) `IHubCallerConnectionContext` Rozhraní je objekt proxy serveru, pomocí kterého můžete volat metody na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="26d15-163">A mock client is then created using the `Mock` object, of type [IHubCallerConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.ihubcallerconnectioncontext(v=vs.118).aspx) (in SignalR 2.1, assign `dynamic` for the type parameter.) The `IHubCallerConnectionContext` interface is the proxy object with which you invoke methods on the client.</span></span>

    <span data-ttu-id="26d15-164">Test pak vytvoří instanci `ChatHub`a potom vytvoří na cvičnou verzi `broadcastMessage` metodu, která pak je vyvolána voláním `Send` metodu v rozbočovači.</span><span class="sxs-lookup"><span data-stu-id="26d15-164">The test then creates an instance of `ChatHub`, and then creates a mock version of the `broadcastMessage` method, which in turn is invoked by calling the `Send` method on the hub.</span></span>
3. <span data-ttu-id="26d15-165">Sestavte řešení stisknutím kombinace kláves **F6**.</span><span class="sxs-lookup"><span data-stu-id="26d15-165">Build the solution by pressing **F6**.</span></span>
4. <span data-ttu-id="26d15-166">Spusťte Jednotkový test.</span><span class="sxs-lookup"><span data-stu-id="26d15-166">Run the unit test.</span></span> <span data-ttu-id="26d15-167">V sadě Visual Studio, vyberte **testovací**, **Windows**, **Průzkumník testů**.</span><span class="sxs-lookup"><span data-stu-id="26d15-167">In Visual Studio, select **Test**, **Windows**, **Test Explorer**.</span></span> <span data-ttu-id="26d15-168">V okně Průzkumník testů, klikněte pravým tlačítkem na **HubsAreMockableViaDynamic** a vyberte **spustit vybrané testy**.</span><span class="sxs-lookup"><span data-stu-id="26d15-168">In the Test Explorer window, right-click **HubsAreMockableViaDynamic** and select **Run Selected Tests**.</span></span>

    ![Průzkumník testů](unit-testing-signalr-applications/_static/image7.png)
5. <span data-ttu-id="26d15-170">Ověřte, že test proběhl úspěšně kontrolou dolním podokně, v okně Průzkumníka testů.</span><span class="sxs-lookup"><span data-stu-id="26d15-170">Verify that the test passed by checking the lower pane in the Test Explorer window.</span></span> <span data-ttu-id="26d15-171">V okně se zobrazí, že test proběhl úspěšně.</span><span class="sxs-lookup"><span data-stu-id="26d15-171">The window will show that the test passed.</span></span>

    ![Test proběhl úspěšně](unit-testing-signalr-applications/_static/image8.png)
