---
uid: web-api/overview/odata-support-in-aspnet-web-api/odata-v4/using-a-singleton-in-an-odata-endpoint-in-web-api-22
title: Vytvoření jednotlivý prvek v OData v4 použití rozhraní Web API 2.2 | Microsoft Docs
author: rick-anderson
description: Toto téma ukazuje, jak definovat jednotlivý prvek v koncový bod OData v 2.2 webové rozhraní API.
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/27/2014
ms.topic: article
ms.assetid: 4064ab14-26ee-4d5c-ae58-1bdda525ad06
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/odata-support-in-aspnet-web-api/odata-v4/using-a-singleton-in-an-odata-endpoint-in-web-api-22
msc.type: authoredcontent
ms.openlocfilehash: 92c5056548b1e39defb28ac36f83b001dd108f5f
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2017
ms.locfileid: "26566767"
---
<a name="create-a-singleton-in-odata-v4-using-web-api-22"></a><span data-ttu-id="9f9f5-103">Vytvoření jednotlivý prvek v OData v4 použití rozhraní Web API 2.2</span><span class="sxs-lookup"><span data-stu-id="9f9f5-103">Create a Singleton in OData v4 Using Web API 2.2</span></span>
====================
<span data-ttu-id="9f9f5-104">podle Zoe Luo</span><span class="sxs-lookup"><span data-stu-id="9f9f5-104">by Zoe Luo</span></span>

> <span data-ttu-id="9f9f5-105">Obvyklým entita může používat pouze v případě měla zapouzdřený v sadu entit.</span><span class="sxs-lookup"><span data-stu-id="9f9f5-105">Traditionally, an entity could only be accessed if it were encapsulated inside an entity set.</span></span> <span data-ttu-id="9f9f5-106">Ale OData v4 poskytuje dvě další možnosti, jednotlivý prvek a členství ve skupině, které podporuje WebAPI 2.2.</span><span class="sxs-lookup"><span data-stu-id="9f9f5-106">But OData v4 provides two additional options, Singleton and Containment, both of which WebAPI 2.2 supports.</span></span>


<span data-ttu-id="9f9f5-107">Tento článek ukazuje, jak definovat jednotlivý prvek v koncový bod OData v 2.2 webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="9f9f5-107">This article shows how to define a singleton in an OData endpoint in Web API 2.2.</span></span> <span data-ttu-id="9f9f5-108">Informace o jaký typ singleton je a jak můžete využívat výhod použití najdete v tématu [pomocí typu singleton, abyste definovali speciální entitě](https://blogs.msdn.com/b/odatateam/archive/2014/03/05/use-singleton-to-define-your-special-entity.aspx).</span><span class="sxs-lookup"><span data-stu-id="9f9f5-108">For information on what a singleton is and how you can benefit from using it, see [Using a singleton to define your special entity](https://blogs.msdn.com/b/odatateam/archive/2014/03/05/use-singleton-to-define-your-special-entity.aspx).</span></span> <span data-ttu-id="9f9f5-109">Vytvořit koncový bod OData V4 v rozhraní Web API, přečtěte si článek [vytvoření OData v4 koncový bod pomocí rozhraní ASP.NET Web API 2.2](create-an-odata-v4-endpoint.md).</span><span class="sxs-lookup"><span data-stu-id="9f9f5-109">To create an OData V4 endpoint in Web API, see [Create an OData v4 Endpoint Using ASP.NET Web API 2.2](create-an-odata-v4-endpoint.md).</span></span> 

<span data-ttu-id="9f9f5-110">Vytvoříme jednotlivý prvek v projektu webového rozhraní API pomocí modelu následující data:</span><span class="sxs-lookup"><span data-stu-id="9f9f5-110">We'll create a singleton in your Web API project using the following data model:</span></span>

![Datový Model](using-a-singleton-in-an-odata-endpoint-in-web-api-22/_static/image1.png)

<span data-ttu-id="9f9f5-112">Jednotlivý prvek s názvem `Umbrella` bude určena podle typu `Company`a sadu s názvem entit `Employees` bude určena podle typu `Employee`.</span><span class="sxs-lookup"><span data-stu-id="9f9f5-112">A singleton named `Umbrella` will be defined based on type `Company`, and an entity set named `Employees` will be defined based on type `Employee`.</span></span>

<span data-ttu-id="9f9f5-113">Řešení používá v tomto kurzu lze stáhnout z [webu CodePlex](http://aspnet.codeplex.com/sourcecontrol/latest#Samples/WebApi/OData/v4/ODataSingletonSample/).</span><span class="sxs-lookup"><span data-stu-id="9f9f5-113">The solution used in this tutorial can be downloaded from [CodePlex](http://aspnet.codeplex.com/sourcecontrol/latest#Samples/WebApi/OData/v4/ODataSingletonSample/).</span></span>

## <a name="define-the-data-model"></a><span data-ttu-id="9f9f5-114">Definování datového modelu</span><span class="sxs-lookup"><span data-stu-id="9f9f5-114">Define the data model</span></span>

1. <span data-ttu-id="9f9f5-115">Definování typů CLR.</span><span class="sxs-lookup"><span data-stu-id="9f9f5-115">Define the CLR types.</span></span>

    [!code-csharp[Main](using-a-singleton-in-an-odata-endpoint-in-web-api-22/samples/sample1.cs)]
2. <span data-ttu-id="9f9f5-116">Generovat model EDM založený na typy CLR.</span><span class="sxs-lookup"><span data-stu-id="9f9f5-116">Generate the EDM model based on the CLR types.</span></span>

    [!code-csharp[Main](using-a-singleton-in-an-odata-endpoint-in-web-api-22/samples/sample2.cs)]

    <span data-ttu-id="9f9f5-117">Zde `builder.Singleton<Company>("Umbrella")` informuje Tvůrce modelu k vytvoření jednotlivý prvek s názvem `Umbrella` v modelu EDM.</span><span class="sxs-lookup"><span data-stu-id="9f9f5-117">Here, `builder.Singleton<Company>("Umbrella")` tells the model builder to create a singleton named `Umbrella` in the EDM model.</span></span>

    <span data-ttu-id="9f9f5-118">Vygenerovaný metadata bude vypadat takto:</span><span class="sxs-lookup"><span data-stu-id="9f9f5-118">The generated metadata will look like the following:</span></span>

    [!code-xml[Main](using-a-singleton-in-an-odata-endpoint-in-web-api-22/samples/sample3.xml)]

    <span data-ttu-id="9f9f5-119">Z metadat jsme uvidíte, že navigační vlastnost `Company` v `Employees` sady entit je vázán k singleton `Umbrella`.</span><span class="sxs-lookup"><span data-stu-id="9f9f5-119">From the metadata we can see that the navigation property `Company` in the `Employees` entity set is bound to the singleton `Umbrella`.</span></span> <span data-ttu-id="9f9f5-120">Vazba se provádí automaticky `ODataConventionModelBuilder`, vzhledem k tomu jenom `Umbrella` má `Company` typu.</span><span class="sxs-lookup"><span data-stu-id="9f9f5-120">The binding is done automatically by `ODataConventionModelBuilder`, since only `Umbrella` has the `Company` type.</span></span> <span data-ttu-id="9f9f5-121">Pokud neexistuje žádné nejednoznačnosti v modelu, můžete použít `HasSingletonBinding` explicitně vytvořit vazbu na navigační vlastnost typu singleton; `HasSingletonBinding` má stejný účinek jako pomocí `Singleton` atribut v definici typu CLR:</span><span class="sxs-lookup"><span data-stu-id="9f9f5-121">If there is any ambiguity in the model, you can use `HasSingletonBinding` to explicitly bind a navigation property to a singleton; `HasSingletonBinding` has the same effect as using the `Singleton` attribute in the CLR type definition:</span></span>

    [!code-csharp[Main](using-a-singleton-in-an-odata-endpoint-in-web-api-22/samples/sample4.cs)]

## <a name="define-the-singleton-controller"></a><span data-ttu-id="9f9f5-122">Zadejte řadič singleton</span><span class="sxs-lookup"><span data-stu-id="9f9f5-122">Define the singleton controller</span></span>

<span data-ttu-id="9f9f5-123">Jako řadič EntitySet, zdědí řadičem singleton `ODataController`, a měl by být názvu řadiče singleton `[singletonName]Controller`.</span><span class="sxs-lookup"><span data-stu-id="9f9f5-123">Like the EntitySet controller, the singleton controller inherits from `ODataController`, and the singleton controller name should be `[singletonName]Controller`.</span></span>

[!code-csharp[Main](using-a-singleton-in-an-odata-endpoint-in-web-api-22/samples/sample5.cs)]

<span data-ttu-id="9f9f5-124">Aby bylo možné zpracovávat různé druhy požadavky, akce musí být předem definované v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="9f9f5-124">In order to handle different kinds of requests, actions are required to be pre-defined in the controller.</span></span> <span data-ttu-id="9f9f5-125">**Atribut směrování** je povoleno ve výchozím nastavení v WebApi 2.2.</span><span class="sxs-lookup"><span data-stu-id="9f9f5-125">**Attribute routing** is enabled by default in WebApi 2.2.</span></span> <span data-ttu-id="9f9f5-126">Například můžete definovat akce pro zpracování dotazování `Revenue` z `Company` pomocí atributu směrování, použijte následující:</span><span class="sxs-lookup"><span data-stu-id="9f9f5-126">For example, to define an action to handle querying `Revenue` from `Company` using attribute routing, use the following:</span></span>

[!code-csharp[Main](using-a-singleton-in-an-odata-endpoint-in-web-api-22/samples/sample6.cs)]

<span data-ttu-id="9f9f5-127">Pokud si nejste ochotni definovat atributy pro každou akci, pouze definuje vaše akce následující [konvencí směrování protokolu OData](../odata-routing-conventions.md).</span><span class="sxs-lookup"><span data-stu-id="9f9f5-127">If you are not willing to define attributes for each action, just define your actions following [OData Routing Conventions](../odata-routing-conventions.md).</span></span> <span data-ttu-id="9f9f5-128">Vzhledem k tomu, že klíč se nevyžaduje pro dotazování typu singleton, akce definované v kontroleru typu singleton se mírně liší od akce definované v kontroleru entityset.</span><span class="sxs-lookup"><span data-stu-id="9f9f5-128">Since a key is not required for querying a singleton, the actions defined in the singleton controller are slightly different from actions defined in the entityset controller.</span></span>

<span data-ttu-id="9f9f5-129">Pro srovnání podpisů metoda pro každou definici akce v kontroleru singleton jsou uvedeny níže.</span><span class="sxs-lookup"><span data-stu-id="9f9f5-129">For reference, method signatures for every action definition in the singleton controller are listed below.</span></span>

[!code-csharp[Main](using-a-singleton-in-an-odata-endpoint-in-web-api-22/samples/sample7.cs)]

<span data-ttu-id="9f9f5-130">V podstatě to je všechno, co musíte udělat na straně služby.</span><span class="sxs-lookup"><span data-stu-id="9f9f5-130">Basically, this is all you need to do on the service side.</span></span> <span data-ttu-id="9f9f5-131">[Ukázkový projekt](http://aspnet.codeplex.com/sourcecontrol/latest#Samples/WebApi/OData/v4/ODataSingletonSample/) obsahuje všechny kódu pro řešení a klient OData, který ukazuje způsob použití singleton.</span><span class="sxs-lookup"><span data-stu-id="9f9f5-131">The [sample project](http://aspnet.codeplex.com/sourcecontrol/latest#Samples/WebApi/OData/v4/ODataSingletonSample/) contains all of the code for the solution and the OData client that shows how to use the singleton.</span></span> <span data-ttu-id="9f9f5-132">Klient je sestavena kroků v [vytvořit aplikaci OData v4 klienta](create-an-odata-v4-client-app.md).</span><span class="sxs-lookup"><span data-stu-id="9f9f5-132">The client is built by following the steps in [Create an OData v4 Client App](create-an-odata-v4-client-app.md).</span></span>

<span data-ttu-id="9f9f5-133">.</span><span class="sxs-lookup"><span data-stu-id="9f9f5-133">.</span></span> 

<span data-ttu-id="9f9f5-134">*Díky Leo Hu pro původní obsah tohoto článku.*</span><span class="sxs-lookup"><span data-stu-id="9f9f5-134">*Thanks to Leo Hu for the original content of this article.*</span></span>
