---
uid: web-forms/overview/ajax-control-toolkit/animation/picking-one-animation-out-of-a-list-cs
title: "Výběr jednoho animace mimo seznam (C#) | Microsoft Docs"
author: wenz
description: "V sadě nástrojů ovládacího prvku ASP.NET AJAX ovládacího prvku animace není právě ovládací prvek ale celé rozhraní pro přidání do ovládacího prvku animace. Rozhraní framework také povolit..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: 06a776fe-7c73-4ca7-8e02-5260a86edc03
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/animation/picking-one-animation-out-of-a-list-cs
msc.type: authoredcontent
ms.openlocfilehash: a24c4ffe49df4eb663f833eb1814f7cbcf15e07e
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2017
---
<a name="picking-one-animation-out-of-a-list-c"></a><span data-ttu-id="4ecf3-104">Výběr jednoho animace mimo seznam (C#)</span><span class="sxs-lookup"><span data-stu-id="4ecf3-104">Picking One Animation Out Of a List (C#)</span></span>
====================
<span data-ttu-id="4ecf3-105">podle [Christian Wenz](https://github.com/wenz)</span><span class="sxs-lookup"><span data-stu-id="4ecf3-105">by [Christian Wenz](https://github.com/wenz)</span></span>

<span data-ttu-id="4ecf3-106">[Stáhněte si kód](http://download.microsoft.com/download/f/9/a/f9a26acd-8df4-4484-8a18-199e4598f411/Animation5.cs.zip) nebo [stáhnout PDF](http://download.microsoft.com/download/6/7/1/6718d452-ff89-4d3f-a90e-c74ec2d636a3/animation5CS.pdf)</span><span class="sxs-lookup"><span data-stu-id="4ecf3-106">[Download Code](http://download.microsoft.com/download/f/9/a/f9a26acd-8df4-4484-8a18-199e4598f411/Animation5.cs.zip) or [Download PDF](http://download.microsoft.com/download/6/7/1/6718d452-ff89-4d3f-a90e-c74ec2d636a3/animation5CS.pdf)</span></span>

> <span data-ttu-id="4ecf3-107">V sadě nástrojů ovládacího prvku ASP.NET AJAX ovládacího prvku animace není právě ovládací prvek ale celé rozhraní pro přidání do ovládacího prvku animace.</span><span class="sxs-lookup"><span data-stu-id="4ecf3-107">The Animation control in the ASP.NET AJAX Control Toolkit is not just a control but a whole framework to add animations to a control.</span></span> <span data-ttu-id="4ecf3-108">Rozhraní framework také umožňuje programátorů vybrat jeden animace mimo seznam animace, v závislosti na vyhodnocení určitý kód JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4ecf3-108">The framework also allows the programmer to pick one animation out of a list of animations, depending on the evaluation of some JavaScript code.</span></span>


## <a name="overview"></a><span data-ttu-id="4ecf3-109">Přehled</span><span class="sxs-lookup"><span data-stu-id="4ecf3-109">Overview</span></span>

<span data-ttu-id="4ecf3-110">V sadě nástrojů ovládacího prvku ASP.NET AJAX ovládacího prvku animace není právě ovládací prvek ale celé rozhraní pro přidání do ovládacího prvku animace.</span><span class="sxs-lookup"><span data-stu-id="4ecf3-110">The Animation control in the ASP.NET AJAX Control Toolkit is not just a control but a whole framework to add animations to a control.</span></span> <span data-ttu-id="4ecf3-111">Rozhraní framework také umožňuje programátorů vybrat jeden animace mimo seznam animace, v závislosti na vyhodnocení určitý kód JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4ecf3-111">The framework also allows the programmer to pick one animation out of a list of animations, depending on the evaluation of some JavaScript code.</span></span>

## <a name="steps"></a><span data-ttu-id="4ecf3-112">Kroky</span><span class="sxs-lookup"><span data-stu-id="4ecf3-112">Steps</span></span>

<span data-ttu-id="4ecf3-113">První řadě zahrnují `ScriptManager` na stránce; potom knihovny ASP.NET AJAX je načtena, aby bylo možné použít Toolkitu:</span><span class="sxs-lookup"><span data-stu-id="4ecf3-113">First of all, include the `ScriptManager` in the page; then, the ASP.NET AJAX library is loaded, making it possible to use the Control Toolkit:</span></span>

[!code-aspx[Main](picking-one-animation-out-of-a-list-cs/samples/sample1.aspx)]

<span data-ttu-id="4ecf3-114">Animace se použijí na panel textu, který vypadá takto:</span><span class="sxs-lookup"><span data-stu-id="4ecf3-114">The animation will be applied to a panel of text which looks like this:</span></span>

[!code-aspx[Main](picking-one-animation-out-of-a-list-cs/samples/sample2.aspx)]

<span data-ttu-id="4ecf3-115">Související třídy CSS pro panel definovat barvu pozadí dobrý a také nastavit pevnou šířku pro panel:</span><span class="sxs-lookup"><span data-stu-id="4ecf3-115">In the associated CSS class for the panel, define a nice background color and also set a fixed width for the panel:</span></span>

[!code-css[Main](picking-one-animation-out-of-a-list-cs/samples/sample3.css)]

<span data-ttu-id="4ecf3-116">Poté, přidejte `AnimationExtender` na stránku, poskytuje `ID`, `TargetControlID` atribut a povinný údaj`runat="server":`</span><span class="sxs-lookup"><span data-stu-id="4ecf3-116">Then, add the `AnimationExtender` to the page, providing an `ID`, the `TargetControlID` attribute and the obligatory `runat="server":`</span></span>

[!code-aspx[Main](picking-one-animation-out-of-a-list-cs/samples/sample4.aspx)]

<span data-ttu-id="4ecf3-117">V rámci `<Animations>` uzlu, použijte `<OnLoad>` ke spuštění animací, jakmile byla úplným načtením stránky.</span><span class="sxs-lookup"><span data-stu-id="4ecf3-117">Within the `<Animations>` node, use `<OnLoad>` to run the animations once the page has been fully loaded.</span></span> <span data-ttu-id="4ecf3-118">Místo mezi regulární animace `<Case>` element se stává play.</span><span class="sxs-lookup"><span data-stu-id="4ecf3-118">Instead of one of the regular animations, the `<Case>` element comes into play.</span></span> <span data-ttu-id="4ecf3-119">Hodnota atributu jeho SelectScript vyhodnotí; Návratová hodnota musí být číselná.</span><span class="sxs-lookup"><span data-stu-id="4ecf3-119">The value of its SelectScript attribute is evaluated; the return value must be numerical.</span></span> <span data-ttu-id="4ecf3-120">V závislosti na toto číslo, jeden z subanimations v rámci &lt;případ&gt; se spustí.</span><span class="sxs-lookup"><span data-stu-id="4ecf3-120">Depending on this number, one of the subanimations within &lt;Case&gt; is executed.</span></span> <span data-ttu-id="4ecf3-121">Například pokud je výsledkem SelectScript 2, Toolkitu spouští třetí animace v rámci &lt;případ&gt; (počítání spustí 0).</span><span class="sxs-lookup"><span data-stu-id="4ecf3-121">For instance, if SelectScript evaluates to 2, the Control Toolkit runs the third animation within &lt;Case&gt; (counting starts at 0).</span></span>

<span data-ttu-id="4ecf3-122">Následující kód definuje tři subanimations: Změna velikosti šířku, změny velikosti výšku a pozvolného vysouvání. Kód jazyka JavaScript (`Math.floor(3 * Math.random())`) potom vybere číslo mezi 0 a 2, tak, aby mezi tři animace se spustí:</span><span class="sxs-lookup"><span data-stu-id="4ecf3-122">The following markup defines three subanimations: Resizing the width, resizing the height, and fading out. The JavaScript code (`Math.floor(3 * Math.random())`) then picks a number between 0 and 2, so that one of the three animations is run:</span></span>

[!code-aspx[Main](picking-one-animation-out-of-a-list-cs/samples/sample5.aspx)]


<span data-ttu-id="4ecf3-123">[![Jedním z možných tři animací: panelu získá širší](picking-one-animation-out-of-a-list-cs/_static/image2.png)](picking-one-animation-out-of-a-list-cs/_static/image1.png)</span><span class="sxs-lookup"><span data-stu-id="4ecf3-123">[![One of the possible three animations: The panel gets wider](picking-one-animation-out-of-a-list-cs/_static/image2.png)](picking-one-animation-out-of-a-list-cs/_static/image1.png)</span></span>

<span data-ttu-id="4ecf3-124">Jedním z možných tři animací: panelu získá širší ([Kliknutím zobrazit obrázek v plné velikosti](picking-one-animation-out-of-a-list-cs/_static/image3.png))</span><span class="sxs-lookup"><span data-stu-id="4ecf3-124">One of the possible three animations: The panel gets wider ([Click to view full-size image](picking-one-animation-out-of-a-list-cs/_static/image3.png))</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="4ecf3-125">[Předchozí](animation-depending-on-a-condition-cs.md)
[další](animating-in-response-to-user-interaction-cs.md)</span><span class="sxs-lookup"><span data-stu-id="4ecf3-125">[Previous](animation-depending-on-a-condition-cs.md)
[Next](animating-in-response-to-user-interaction-cs.md)</span></span>
