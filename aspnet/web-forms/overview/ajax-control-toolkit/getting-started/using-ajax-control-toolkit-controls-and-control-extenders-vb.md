---
uid: web-forms/overview/ajax-control-toolkit/getting-started/using-ajax-control-toolkit-controls-and-control-extenders-vb
title: Pomocí ovládacích prvků Toolkit řízení AJAX a řízení Extender (VB) | Microsoft Docs
author: microsoft
description: Informace o postupu přidání ovládacích prvků sadu ovládacích prvků AJAX a rozšíření na stránky ASP.NET.
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/12/2009
ms.topic: article
ms.assetid: 763650a9-ffde-46a9-b779-7a9145dd5d88
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/getting-started/using-ajax-control-toolkit-controls-and-control-extenders-vb
msc.type: authoredcontent
ms.openlocfilehash: 080dd65677d80fb75ab37a20f6c385a38af4e353
ms.sourcegitcommit: f8852267f463b62d7f975e56bea9aa3f68fbbdeb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
<a name="using-ajax-control-toolkit-controls-and-control-extenders-vb"></a><span data-ttu-id="e8df3-103">Pomocí ovládacích prvků Toolkit řízení AJAX a řízení Extender (VB)</span><span class="sxs-lookup"><span data-stu-id="e8df3-103">Using AJAX Control Toolkit Controls and Control Extenders (VB)</span></span>
====================
<span data-ttu-id="e8df3-104">podle [Microsoft](https://github.com/microsoft)</span><span class="sxs-lookup"><span data-stu-id="e8df3-104">by [Microsoft](https://github.com/microsoft)</span></span>

> <span data-ttu-id="e8df3-105">Informace o postupu přidání ovládacích prvků sadu ovládacích prvků AJAX a rozšíření na stránky ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="e8df3-105">Learn how to add AJAX Control Toolkit controls and extenders to your ASP.NET pages.</span></span>


<span data-ttu-id="e8df3-106">Toolkitu AJAX obsahuje sadu ovládacích prvků a řízení rozšíření.</span><span class="sxs-lookup"><span data-stu-id="e8df3-106">The AJAX Control Toolkit contains a set of controls and control extenders.</span></span> <span data-ttu-id="e8df3-107">V tomto kurzu stručný zjistěte, jak přidat ovládací prvky a Extender ovládacího prvku do stránky ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="e8df3-107">In this brief tutorial, you learn how to add both controls and control extenders to an ASP.NET page.</span></span>

> [!NOTE] 
> 
> <span data-ttu-id="e8df3-108">Pokyny k instalaci Toolkitu AJAX a přidání Toolkitu AJAX do sady nástrojů Visual Studio nebo Visual Web Developer naleznete v kurzu [začít pracovat s Toolkitu AJAX](get-started-with-the-ajax-control-toolkit-vb.md).</span><span class="sxs-lookup"><span data-stu-id="e8df3-108">For instructions on installing the AJAX Control Toolkit and adding the AJAX Control Toolkit to the Visual Studio/Visual Web Developer toolbox, see the tutorial [Get Started with the AJAX Control Toolkit](get-started-with-the-ajax-control-toolkit-vb.md).</span></span>


## <a name="using-ajax-control-toolkit-controls"></a><span data-ttu-id="e8df3-109">Použití ovládacích prvků Toolkit AJAX ovládací prvek</span><span class="sxs-lookup"><span data-stu-id="e8df3-109">Using AJAX Control Toolkit Controls</span></span>

<span data-ttu-id="e8df3-110">Ovládací prvek sadu ovládacích prvků AJAX funguje stejně jako normální ovládací prvek ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="e8df3-110">An AJAX Control Toolkit control works just like a normal ASP.NET control.</span></span> <span data-ttu-id="e8df3-111">Můžete přetáhnout ovládací prvek z panelu nástrojů na stránky ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="e8df3-111">You can drag the control from the toolbox onto an ASP.NET page.</span></span> <span data-ttu-id="e8df3-112">Ovládací prvek můžete přidat na stránku v návrhovém zobrazení nebo zobrazení zdroje.</span><span class="sxs-lookup"><span data-stu-id="e8df3-112">You can add the control to the page in either Design view or Source view.</span></span>

<span data-ttu-id="e8df3-113">Při použití ovládacích prvků z Toolkitu AJAX je jeden speciální požadavek.</span><span class="sxs-lookup"><span data-stu-id="e8df3-113">There is one special requirement when using the controls from the AJAX Control Toolkit.</span></span> <span data-ttu-id="e8df3-114">Stránka musí obsahovat ovládací prvek ScriptManager.</span><span class="sxs-lookup"><span data-stu-id="e8df3-114">The page must contain a ScriptManager control.</span></span> <span data-ttu-id="e8df3-115">Ovládací prvek ScriptManager zodpovídá za včetně všechny nezbytné JavaScript vyžaduje sadu ovládacích prvků AJAX ovládací prvky.</span><span class="sxs-lookup"><span data-stu-id="e8df3-115">The ScriptManager control is responsible for including all of the necessary JavaScript required by the AJAX Control Toolkit controls.</span></span>

<span data-ttu-id="e8df3-116">Například na kartě AJAX Control Toolkit obsahuje ovládací prvek s názvem ovládacího prvku Editor.</span><span class="sxs-lookup"><span data-stu-id="e8df3-116">For example, the AJAX Control Toolkit tab includes a control named the Editor control.</span></span> <span data-ttu-id="e8df3-117">Tento ovládací prvek zobrazí bohatě vybavený editor HTML.</span><span class="sxs-lookup"><span data-stu-id="e8df3-117">This control displays a rich HTML editor.</span></span> <span data-ttu-id="e8df3-118">Použijte následující postup přidání ovládacího prvku Editor na stránku:</span><span class="sxs-lookup"><span data-stu-id="e8df3-118">Follow these steps to add the Editor control to a page:</span></span>

1. <span data-ttu-id="e8df3-119">Vytvořit novou stránku ASP.NET s názvem ShowEditor.aspx</span><span class="sxs-lookup"><span data-stu-id="e8df3-119">Create a new ASP.NET page named ShowEditor.aspx</span></span>
2. <span data-ttu-id="e8df3-120">V panelu nástrojů vyberte ovládací prvek ScriptManager from beneath karty rozšíření AJAX a přetáhněte ovládacího prvku na stránku.</span><span class="sxs-lookup"><span data-stu-id="e8df3-120">Select the ScriptManager control from beneath the AJAX Extensions tab in the toolbox and drag the control onto the page.</span></span>
3. <span data-ttu-id="e8df3-121">Vyberte ovládací prvek Editor from beneath kartě sadu ovládacích prvků AJAX v panelu nástrojů a přetáhněte ovládací prvek na stránce (viz obrázek 1).</span><span class="sxs-lookup"><span data-stu-id="e8df3-121">Select the Editor control from beneath the AJAX Control Toolkit tab in the toolbox and drag the control onto the page (see Figure 1).</span></span> <span data-ttu-id="e8df3-122">Návrhář by měl vypadat jako na obrázku 2.</span><span class="sxs-lookup"><span data-stu-id="e8df3-122">The Designer should look like Figure 2.</span></span>
4. <span data-ttu-id="e8df3-123">Spusťte web tak, že vyberete možnost nabídky **ladit, spustit ladění** nebo stiskne klávesu F5.</span><span class="sxs-lookup"><span data-stu-id="e8df3-123">Run the web site by selecting the menu option **Debug, Start Debugging** or hitting the F5 key.</span></span>
5. <span data-ttu-id="e8df3-124">Měli byste vidět stránky na obrázku 3.</span><span class="sxs-lookup"><span data-stu-id="e8df3-124">You should see the page in Figure 3.</span></span>


<span data-ttu-id="e8df3-125">[![Výběr ovládacího prvku HTML Editor](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image1.jpg)](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image1.png)</span><span class="sxs-lookup"><span data-stu-id="e8df3-125">[![Selecting the HTML Editor control](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image1.jpg)](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image1.png)</span></span>

<span data-ttu-id="e8df3-126">**Obrázek 01**: výběr ovládacího prvku HTML Editor ([Kliknutím zobrazit obrázek v plné velikosti](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image2.png))</span><span class="sxs-lookup"><span data-stu-id="e8df3-126">**Figure 01**: Selecting the HTML Editor control([Click to view full-size image](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image2.png))</span></span>


<span data-ttu-id="e8df3-127">[![Návrháři sady Visual Studio s ovládací prvek ScriptManager a úpravy](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image2.jpg)](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image3.png)</span><span class="sxs-lookup"><span data-stu-id="e8df3-127">[![Visual Studio Designer with ScriptManager and Edit control](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image2.jpg)](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image3.png)</span></span>

<span data-ttu-id="e8df3-128">**Obrázek 02**: návrháři sady Visual Studio s ovládací prvek ScriptManager a úpravy ([Kliknutím zobrazit obrázek v plné velikosti](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image4.png))</span><span class="sxs-lookup"><span data-stu-id="e8df3-128">**Figure 02**: Visual Studio Designer with ScriptManager and Edit control([Click to view full-size image](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image4.png))</span></span>


<span data-ttu-id="e8df3-129">[![Stránka DisplayEditor.aspx](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image3.jpg)](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image5.png)</span><span class="sxs-lookup"><span data-stu-id="e8df3-129">[![The DisplayEditor.aspx page](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image3.jpg)](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image5.png)</span></span>

<span data-ttu-id="e8df3-130">**Obrázek 03**: stránka DisplayEditor.aspx ([Kliknutím zobrazit obrázek v plné velikosti](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image6.png))</span><span class="sxs-lookup"><span data-stu-id="e8df3-130">**Figure 03**: The DisplayEditor.aspx page([Click to view full-size image](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image6.png))</span></span>


## <a name="using-ajax-control-toolkit-control-extenders"></a><span data-ttu-id="e8df3-131">Pomocí AJAX řízení Toolkit řízení Extender</span><span class="sxs-lookup"><span data-stu-id="e8df3-131">Using AJAX Control Toolkit Control Extenders</span></span>

<span data-ttu-id="e8df3-132">Toolkitu AJAX také obsahuje Extender ovládacího prvku.</span><span class="sxs-lookup"><span data-stu-id="e8df3-132">The AJAX Control Toolkit also contains control extenders.</span></span> <span data-ttu-id="e8df3-133">Jak naznačuje název rozšiřujícího objektu ovládacího prvku rozšiřuje funkce existujícího ovládacího prvku.</span><span class="sxs-lookup"><span data-stu-id="e8df3-133">As its name suggests, a control extender extends the functionality of an existing control.</span></span> <span data-ttu-id="e8df3-134">Například rozšiřujícího objektu ovládacího prvku ConfirmButton rozšiřuje standardního ovládacího prvku tlačítko ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="e8df3-134">For example, the ConfirmButton control extender extends the standard ASP.NET Button control.</span></span> <span data-ttu-id="e8df3-135">Zařízení extender změní chování s ovládací prvek tlačítko tak, aby na tlačítko se zobrazí dialogové okno potvrzení po kliknutí na tlačítko.</span><span class="sxs-lookup"><span data-stu-id="e8df3-135">The extender changes the Button control�s behavior so that the Button displays a confirmation dialog when you click it.</span></span>

<span data-ttu-id="e8df3-136">Ovládací prvek rozšiřujícího objektu, stejně jako ovládací prvek sadu ovládacích prvků AJAX vyžaduje ovládací prvek ScriptManager.</span><span class="sxs-lookup"><span data-stu-id="e8df3-136">A control extender, just like an AJAX Control Toolkit control, requires a ScriptManager control.</span></span> <span data-ttu-id="e8df3-137">Ovládací prvek ScriptManager musí přidat na stránku, než začnete používat ovládací prvek Extender na stránce.</span><span class="sxs-lookup"><span data-stu-id="e8df3-137">You must add a ScriptManager control to a page before you start using control extenders in the page.</span></span>

<span data-ttu-id="e8df3-138">Postupujte podle těchto kroků pro použití rozšíření ConfirmButton ovládacího prvku:</span><span class="sxs-lookup"><span data-stu-id="e8df3-138">Follow these steps to use the ConfirmButton control extender:</span></span>

1. <span data-ttu-id="e8df3-139">Vytvořit novou stránku ASP.NET s názvem ShowConfirmButton.aspx</span><span class="sxs-lookup"><span data-stu-id="e8df3-139">Create a new ASP.NET page named ShowConfirmButton.aspx</span></span>
2. <span data-ttu-id="e8df3-140">Přidáte ovládací prvek ScriptManager na stránku přetažením ovládacího prvku na stránce from beneath karty rozšíření AJAX.</span><span class="sxs-lookup"><span data-stu-id="e8df3-140">Add a ScriptManager control to the page by dragging the control onto the page from beneath the AJAX Extensions tab.</span></span>
3. <span data-ttu-id="e8df3-141">Přidáte standardního ovládacího prvku tlačítko na stránku přetažením tlačítko from beneath standardní karta v panelu nástrojů na plochu návrháře.</span><span class="sxs-lookup"><span data-stu-id="e8df3-141">Add a standard Button control to the page by dragging the Button from beneath the Standard tab in the toolbox onto the Designer surface.</span></span>
4. <span data-ttu-id="e8df3-142">Klikněte **přidat rozšiřujícího objektu** úloh možnost (viz obrázek 4).</span><span class="sxs-lookup"><span data-stu-id="e8df3-142">Click the **Add Extender** task option (see Figure 4).</span></span>
5. <span data-ttu-id="e8df3-143">V dialogovém okně Zvolit rozšíření, vyberte ConfirmButtonExtender (viz obrázek 5) a klikněte na tlačítko OK.</span><span class="sxs-lookup"><span data-stu-id="e8df3-143">In the Choose Extender dialog, select ConfirmButtonExtender (see Figure 5) and click the OK button.</span></span>
6. <span data-ttu-id="e8df3-144">Vyberte ovládací prvek tlačítko v návrháři a rozbalte Extender, Button1\_ConfirmButtonExtender uzlu v okně vlastností (viz obrázek 6).</span><span class="sxs-lookup"><span data-stu-id="e8df3-144">Select the Button control in the Designer and expand the Extenders, Button1\_ConfirmButtonExtender node in the Properties window (see Figure 6).</span></span> <span data-ttu-id="e8df3-145">Přiřadí hodnotu *skutečně?* ConfirmText vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="e8df3-145">Assign the value *�Really?�* to the ConfirmText property.</span></span>
7. <span data-ttu-id="e8df3-146">Spuštění stránky tak, že vyberete možnost nabídky **ladit, spustit ladění** nebo stiskněte klávesu F5.</span><span class="sxs-lookup"><span data-stu-id="e8df3-146">Run the page by selecting the menu option **Debug, Start Debugging** or hit the F5 key.</span></span>


<span data-ttu-id="e8df3-147">[![Možnost Přidat rozšiřujícího objektu úlohy](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image4.jpg)](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image7.png)</span><span class="sxs-lookup"><span data-stu-id="e8df3-147">[![The Add Extender task option](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image4.jpg)](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image7.png)</span></span>

<span data-ttu-id="e8df3-148">**Obrázek 04**: rozšiřujícího objektu přidat úloha možnost ([Kliknutím zobrazit obrázek v plné velikosti](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image8.png))</span><span class="sxs-lookup"><span data-stu-id="e8df3-148">**Figure 04**: The Add Extender task option([Click to view full-size image](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image8.png))</span></span>


<span data-ttu-id="e8df3-149">[![Výběr rozšiřujícího objektu ovládacího prvku ConfirmButton](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image5.jpg)](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image9.png)</span><span class="sxs-lookup"><span data-stu-id="e8df3-149">[![Selecting the ConfirmButton control extender](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image5.jpg)](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image9.png)</span></span>

<span data-ttu-id="e8df3-150">**Obrázek 05**: výběr rozšiřujícího objektu ovládacího prvku ConfirmButton ([Kliknutím zobrazit obrázek v plné velikosti](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image10.png))</span><span class="sxs-lookup"><span data-stu-id="e8df3-150">**Figure 05**: Selecting the ConfirmButton control extender([Click to view full-size image](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image10.png))</span></span>


<span data-ttu-id="e8df3-151">[![Nastavení vlastnosti ConfirmButton](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image6.jpg)](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image11.png)</span><span class="sxs-lookup"><span data-stu-id="e8df3-151">[![Setting a ConfirmButton property](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image6.jpg)](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image11.png)</span></span>

<span data-ttu-id="e8df3-152">**Obrázek 06**: nastavení vlastnosti ConfirmButton ([Kliknutím zobrazit obrázek v plné velikosti](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image12.png))</span><span class="sxs-lookup"><span data-stu-id="e8df3-152">**Figure 06**: Setting a ConfirmButton property([Click to view full-size image](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image12.png))</span></span>


<span data-ttu-id="e8df3-153">Po otevření stránky, měli byste vidět tlačítko.</span><span class="sxs-lookup"><span data-stu-id="e8df3-153">When the page opens, you should see a button.</span></span> <span data-ttu-id="e8df3-154">Když kliknete na tlačítko, zobrazí dialogové okno pro potvrzení na obrázku 7.</span><span class="sxs-lookup"><span data-stu-id="e8df3-154">When you click the button, you get the confirmation dialog in Figure 7.</span></span>


<span data-ttu-id="e8df3-155">[![Zobrazení dialogového okna potvrzení](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image7.jpg)](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image13.png)</span><span class="sxs-lookup"><span data-stu-id="e8df3-155">[![Displaying the confirmation dialog](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image7.jpg)](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image13.png)</span></span>

<span data-ttu-id="e8df3-156">**Obrázek 07**: zobrazení dialogového okna potvrzení ([Kliknutím zobrazit obrázek v plné velikosti](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image14.png))</span><span class="sxs-lookup"><span data-stu-id="e8df3-156">**Figure 07**: Displaying the confirmation dialog([Click to view full-size image](using-ajax-control-toolkit-controls-and-control-extenders-vb/_static/image14.png))</span></span>


<span data-ttu-id="e8df3-157">Všimněte si, že obvykle není přetažení rozšiřujícího objektu ovládacího prvku na stránku.</span><span class="sxs-lookup"><span data-stu-id="e8df3-157">Notice that you normally do not drag a control extender onto a page.</span></span> <span data-ttu-id="e8df3-158">Místo toho použít **přidat rozšiřujícího objektu** úloh možnost Přidat extender do ovládacího prvku, který jste už přidali na stránku.</span><span class="sxs-lookup"><span data-stu-id="e8df3-158">Instead, you use the **Add Extender** task option to add an extender to a control that you have already added to a page.</span></span> <span data-ttu-id="e8df3-159">Všimněte si kromě toho nastavit řízení rozšiřujícího objektu vlastnosti otevřením seznamu vlastností pro ovládací prvek rozšiřovanou.</span><span class="sxs-lookup"><span data-stu-id="e8df3-159">Notice, furthermore, that you set control extender properties by opening the property sheet for the control being extended.</span></span>

<span data-ttu-id="e8df3-160">Jeden ovládací prvek ASP.NET můžete rozšířit pomocí více Extender ovládacího prvku.</span><span class="sxs-lookup"><span data-stu-id="e8df3-160">A single ASP.NET control can be extended by multiple control extenders.</span></span> <span data-ttu-id="e8df3-161">Seznam vlastností ovládacího prvku rozšiřovanou zobrazí seznam všech Extender ovládací prvek přidružený k ovládacímu prvku.</span><span class="sxs-lookup"><span data-stu-id="e8df3-161">The property sheet for the control being extended will list all of the control extenders associated with the control.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e8df3-162">[Předchozí](get-started-with-the-ajax-control-toolkit-vb.md)
> [další](creating-a-custom-ajax-control-toolkit-control-extender-vb.md)</span><span class="sxs-lookup"><span data-stu-id="e8df3-162">[Previous](get-started-with-the-ajax-control-toolkit-vb.md)
[Next](creating-a-custom-ajax-control-toolkit-control-extender-vb.md)</span></span>
