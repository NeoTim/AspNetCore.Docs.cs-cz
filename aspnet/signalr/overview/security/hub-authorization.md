---
uid: signalr/overview/security/hub-authorization
title: Ověřování a autorizace Center SignalR | Dokumentace Microsoftu
author: bradygaster
description: Toto téma popisuje, jak omezit, kteří uživatelé nebo rolí se dostanete metod rozbočovače. Verze softwaru použitým v tomto tématu ve Visual Studio 2013 .NET 4.5 SignalR...
ms.author: bradyg
ms.date: 01/05/2015
ms.assetid: a610c796-c131-473c-baef-2e6c568cb2a2
msc.legacyurl: /signalr/overview/security/hub-authorization
msc.type: authoredcontent
ms.openlocfilehash: bfea212283165facc046e5355571c1e6d9c7cd7d
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837413"
---
<a name="authentication-and-authorization-for-signalr-hubs"></a><span data-ttu-id="ebbaf-104">Ověřování a autorizace center SignalR</span><span class="sxs-lookup"><span data-stu-id="ebbaf-104">Authentication and Authorization for SignalR Hubs</span></span>
====================
<span data-ttu-id="ebbaf-105">podle [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="ebbaf-105">by [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="ebbaf-106">Toto téma popisuje, jak omezit, kteří uživatelé nebo rolí se dostanete metod rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-106">This topic describes how to restrict which users or roles can access hub methods.</span></span>
>
> ## <a name="software-versions-used-in-this-topic"></a><span data-ttu-id="ebbaf-107">Verze softwaru použitým v tomto tématu</span><span class="sxs-lookup"><span data-stu-id="ebbaf-107">Software versions used in this topic</span></span>
>
>
> - [<span data-ttu-id="ebbaf-108">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="ebbaf-108">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="ebbaf-109">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="ebbaf-109">.NET 4.5</span></span>
> - <span data-ttu-id="ebbaf-110">Funkce SignalR verze 2</span><span class="sxs-lookup"><span data-stu-id="ebbaf-110">SignalR version 2</span></span>
>
>
>
> ## <a name="previous-versions-of-this-topic"></a><span data-ttu-id="ebbaf-111">Předchozích verzích tohoto tématu</span><span class="sxs-lookup"><span data-stu-id="ebbaf-111">Previous versions of this topic</span></span>
>
> <span data-ttu-id="ebbaf-112">Informace o předchozích verzích systému SignalR naleznete v tématu [starší verze funkce SignalR](../older-versions/index.md).</span><span class="sxs-lookup"><span data-stu-id="ebbaf-112">For information about earlier versions of SignalR, see [SignalR Older Versions](../older-versions/index.md).</span></span>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="ebbaf-113">Otázky a komentáře</span><span class="sxs-lookup"><span data-stu-id="ebbaf-113">Questions and comments</span></span>
>
> <span data-ttu-id="ebbaf-114">Napište prosím zpětnou vazbu o tom, jak vám líbilo v tomto kurzu a co můžeme zlepšit v komentářích v dolní části stránky.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-114">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="ebbaf-115">Pokud máte nějaké otázky, které přímo nesouvisejí, najdete v tomto kurzu, můžete je publikovat [fórum ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) nebo [StackOverflow.com](http://stackoverflow.com/).</span><span class="sxs-lookup"><span data-stu-id="ebbaf-115">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>


## <a name="overview"></a><span data-ttu-id="ebbaf-116">Přehled</span><span class="sxs-lookup"><span data-stu-id="ebbaf-116">Overview</span></span>

<span data-ttu-id="ebbaf-117">Toto téma obsahuje následující oddíly:</span><span class="sxs-lookup"><span data-stu-id="ebbaf-117">This topic contains the following sections:</span></span>

- [<span data-ttu-id="ebbaf-118">Povolit atribut</span><span class="sxs-lookup"><span data-stu-id="ebbaf-118">Authorize attribute</span></span>](#authorizeattribute)
- [<span data-ttu-id="ebbaf-119">Vyžadovat ověřování pro všechna centra</span><span class="sxs-lookup"><span data-stu-id="ebbaf-119">Require authentication for all hubs</span></span>](#requireauth)
- [<span data-ttu-id="ebbaf-120">Vlastní autorizace</span><span class="sxs-lookup"><span data-stu-id="ebbaf-120">Customized authorization</span></span>](#custom)
- [<span data-ttu-id="ebbaf-121">Předat ověřovací informace pro klienty</span><span class="sxs-lookup"><span data-stu-id="ebbaf-121">Pass authentication information to clients</span></span>](#passauth)
- [<span data-ttu-id="ebbaf-122">Možnosti ověřování pro klienty .NET</span><span class="sxs-lookup"><span data-stu-id="ebbaf-122">Authentication options for .NET clients</span></span>](#authoptions)

    - [<span data-ttu-id="ebbaf-123">Soubor cookie ověřování pomocí formulářů</span><span class="sxs-lookup"><span data-stu-id="ebbaf-123">Cookie with Forms Authentication</span></span>](#cookie)
    - [<span data-ttu-id="ebbaf-124">Ověřování Windows</span><span class="sxs-lookup"><span data-stu-id="ebbaf-124">Windows Authentication</span></span>](#windows)
    - [<span data-ttu-id="ebbaf-125">Připojení záhlaví</span><span class="sxs-lookup"><span data-stu-id="ebbaf-125">Connection header</span></span>](#header)
    - [<span data-ttu-id="ebbaf-126">Certifikát</span><span class="sxs-lookup"><span data-stu-id="ebbaf-126">Certificate</span></span>](#certificate)

<a id="authorizeattribute"></a>

## <a name="authorize-attribute"></a><span data-ttu-id="ebbaf-127">Povolit atribut</span><span class="sxs-lookup"><span data-stu-id="ebbaf-127">Authorize attribute</span></span>

<span data-ttu-id="ebbaf-128">Funkce SignalR poskytuje [Authorize](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.authorizeattribute(v=vs.111).aspx) atribut určit, které uživatele nebo role mají přístup do centra nebo metody.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-128">SignalR provides the [Authorize](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.authorizeattribute(v=vs.111).aspx) attribute to specify which users or roles have access to a hub or method.</span></span> <span data-ttu-id="ebbaf-129">Tento atribut je umístěn v `Microsoft.AspNet.SignalR` oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-129">This attribute is located in the `Microsoft.AspNet.SignalR` namespace.</span></span> <span data-ttu-id="ebbaf-130">Můžete použít `Authorize` atribut rozbočovač nebo konkrétní metody v rozbočovači.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-130">You apply the `Authorize` attribute to either a hub or particular methods in a hub.</span></span> <span data-ttu-id="ebbaf-131">Pokud použijete `Authorize` atribut třídy rozbočovače, požadavek na autorizaci zadané platí pro všechny metody v rozbočovači.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-131">When you apply the `Authorize` attribute to a hub class, the specified authorization requirement is applied to all of the methods in the hub.</span></span> <span data-ttu-id="ebbaf-132">Toto téma obsahuje příklady různých typů ověření požadavků, které můžete použít.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-132">This topic provides examples of the different types of authorization requirements that you can apply.</span></span> <span data-ttu-id="ebbaf-133">Bez `Authorize` atribut připojeného klient může získat všechny veřejné metody v rozbočovači.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-133">Without the `Authorize` attribute, a connected client can access any public method on the hub.</span></span>

<span data-ttu-id="ebbaf-134">Pokud jste definovali role ve vaší webové aplikaci s názvem "Admin", můžete zadat, že jenom uživatelé v této roli můžete přístup k centru s následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-134">If you have defined a role named "Admin" in your web application, you could specify that only users in that role can access a hub with the following code.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample1.cs)]

<span data-ttu-id="ebbaf-135">Nebo můžete určit, že Centrum obsahuje jednu metodu, která je k dispozici všem uživatelům a druhá metoda, která je pouze ověřeným uživatelům k dispozici, jak je znázorněno níže.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-135">Or, you can specify that a hub contains one method that is available to all users, and a second method that is only available to authenticated users, as shown below.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample2.cs)]

<span data-ttu-id="ebbaf-136">Následující příklady různých autorizace situacích:</span><span class="sxs-lookup"><span data-stu-id="ebbaf-136">The following examples address different authorization scenarios:</span></span>

- <span data-ttu-id="ebbaf-137">`[Authorize]` – jen ověření uživatelé</span><span class="sxs-lookup"><span data-stu-id="ebbaf-137">`[Authorize]` – only authenticated users</span></span>
- <span data-ttu-id="ebbaf-138">`[Authorize(Roles = "Admin,Manager")]` – jen ověření uživatelé v zadaných rolí</span><span class="sxs-lookup"><span data-stu-id="ebbaf-138">`[Authorize(Roles = "Admin,Manager")]` – only authenticated users in the specified roles</span></span>
- <span data-ttu-id="ebbaf-139">`[Authorize(Users = "user1,user2")]` – pouze pro ověřené uživatele s ze zadaných uživatelských jmen</span><span class="sxs-lookup"><span data-stu-id="ebbaf-139">`[Authorize(Users = "user1,user2")]` – only authenticated users with the specified user names</span></span>
- <span data-ttu-id="ebbaf-140">`[Authorize(RequireOutgoing=false)]` – pouze pro ověřené uživatele můžete vyvolat centra, ale volání ze serveru zpátky do klientů nejsou omezeny autorizace, jako je třeba když jenom určitým uživatelům můžete odeslat zprávu, ale všechny ostatní zprávy.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-140">`[Authorize(RequireOutgoing=false)]` – only authenticated users can invoke the hub, but calls from the server back to clients are not limited by authorization, such as, when only certain users can send a message but all others can receive the message.</span></span> <span data-ttu-id="ebbaf-141">Vlastnost RequireOutgoing dá používat jedině pro celé centrum není pro jednotlivce metody v rozbočovači.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-141">The RequireOutgoing property can only be applied to the entire hub, not on individuals methods within the hub.</span></span> <span data-ttu-id="ebbaf-142">Když RequireOutgoing není nastavená na hodnotu false, jenom uživatelé, kteří splní požadavek na autorizaci se nazývají ze serveru.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-142">When RequireOutgoing is not set to false, only users that meet the authorization requirement are called from the server.</span></span>

<a id="requireauth"></a>

## <a name="require-authentication-for-all-hubs"></a><span data-ttu-id="ebbaf-143">Vyžadovat ověřování pro všechna centra</span><span class="sxs-lookup"><span data-stu-id="ebbaf-143">Require authentication for all hubs</span></span>

<span data-ttu-id="ebbaf-144">Můžete vyžadovat ověřování pro všechny rozbočovače a metody ve vaší aplikaci pomocí volání [RequireAuthentication](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubpipelineextensions.requireauthentication(v=vs.111).aspx) metoda při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-144">You can require authentication for all hubs and hub methods in your application by calling the [RequireAuthentication](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubpipelineextensions.requireauthentication(v=vs.111).aspx) method when the application starts.</span></span> <span data-ttu-id="ebbaf-145">Tuto metodu můžete použít, když máte více rozbočovače a chcete vynutit požadavek na ověření pro všechny z nich.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-145">You might use this method when you have multiple hubs and want to enforce an authentication requirement for all of them.</span></span> <span data-ttu-id="ebbaf-146">S touto metodou nelze zadat požadavky pro role, uživatele nebo odchozí autorizace.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-146">With this method, you cannot specify requirements for role, user, or outgoing authorization.</span></span> <span data-ttu-id="ebbaf-147">Můžete zadat pouze omezen přístup k metodám centra ověřeným uživatelům.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-147">You can only specify that access to the hub methods is restricted to authenticated users.</span></span> <span data-ttu-id="ebbaf-148">Atribut Authorize však můžete použít na rozbočovače a metody zadat další požadavky.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-148">However, you can still apply the Authorize attribute to hubs or methods to specify additional requirements.</span></span> <span data-ttu-id="ebbaf-149">Všechny požadavky, které zadáte v atributu je přidán do základní požadavek ověřování.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-149">Any requirement you specify in an attribute is added to the basic requirement of authentication.</span></span>

<span data-ttu-id="ebbaf-150">Následující příklad ukazuje spouštěcí soubor, který omezuje všech metod rozbočovače na ověřeným uživatelům.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-150">The following example shows a Startup file which restricts all hub methods to authenticated users.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample3.cs)]

<span data-ttu-id="ebbaf-151">Při volání `RequireAuthentication()` metoda po zpracování žádost SignalR, vyvolá výjimku SignalR `InvalidOperationException` výjimky.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-151">If you call the `RequireAuthentication()` method after a SignalR request has been processed, SignalR will throw a `InvalidOperationException` exception.</span></span> <span data-ttu-id="ebbaf-152">Funkce SignalR vyvolá tuto výjimku, protože modul nelze přidat do kanálu rozbočovače přidán po zavolání kanálu.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-152">SignalR throws this exception because you cannot add a module to the HubPipeline after the pipeline has been invoked.</span></span> <span data-ttu-id="ebbaf-153">Předchozí příklad ukazuje volání `RequireAuthentication` metodu `Configuration` metodu, která provádí jednou před prvního požadavku na zpracování.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-153">The previous example shows calling the `RequireAuthentication` method in the `Configuration` method which is executed one time prior to handling the first request.</span></span>

<a id="custom"></a>

## <a name="customized-authorization"></a><span data-ttu-id="ebbaf-154">Vlastní autorizace</span><span class="sxs-lookup"><span data-stu-id="ebbaf-154">Customized authorization</span></span>

<span data-ttu-id="ebbaf-155">Pokud je potřeba upravit, jak se určují autorizaci, můžete vytvořit třídu, která je odvozena z `AuthorizeAttribute` a přepsat [UserAuthorized](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.authorizeattribute.userauthorized(v=vs.111).aspx) metody.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-155">If you need to customize how authorization is determined, you can create a class that derives from `AuthorizeAttribute` and override the [UserAuthorized](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.authorizeattribute.userauthorized(v=vs.111).aspx) method.</span></span> <span data-ttu-id="ebbaf-156">Pro každý požadavek SignalR volá tuto metodu za účelem určení, zda je uživatel oprávnění k dokončení požadavku.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-156">For each request, SignalR invokes this method to determine whether the user is authorized to complete the request.</span></span> <span data-ttu-id="ebbaf-157">Přepsané metody poskytují logiku potřebnou pro váš scénář autorizace.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-157">In the overridden method, you provide the necessary logic for your authorization scenario.</span></span> <span data-ttu-id="ebbaf-158">Následující příklad ukazuje, jak vynutit autorizaci prostřednictvím založené na deklaracích identity.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-158">The following example shows how to enforce authorization through claims-based identity.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample4.cs)]

<a id="passauth"></a>

## <a name="pass-authentication-information-to-clients"></a><span data-ttu-id="ebbaf-159">Předat ověřovací informace pro klienty</span><span class="sxs-lookup"><span data-stu-id="ebbaf-159">Pass authentication information to clients</span></span>

<span data-ttu-id="ebbaf-160">Budete muset použít informace o ověřování v kódu, který běží na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-160">You may need to use authentication information in the code that runs on the client.</span></span> <span data-ttu-id="ebbaf-161">Předáním požadovaných informací při volání metody na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-161">You pass the required information when calling the methods on the client.</span></span> <span data-ttu-id="ebbaf-162">Například metoda aplikace chat mohli předat jako parametr uživatelské jméno osoby, odesílání zpráv, jak je znázorněno níže.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-162">For example, a chat application method could pass as a parameter the user name of the person posting a message, as shown below.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample5.cs)]

<span data-ttu-id="ebbaf-163">Nebo můžete vytvořit objekt reprezentující informace o ověřování a tento objekt předat jako parametr, jak je znázorněno níže.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-163">Or, you can create an object to represent the authentication information and pass that object as a parameter, as shown below.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample6.cs)]

<span data-ttu-id="ebbaf-164">Nikdy byste neměli předávat id připojení pro jednoho klienta jiným klientům, protože ji uživatel se zlými úmysly třeba použít tak, aby napodoboval žádosti od tohoto klienta.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-164">You should never pass one client's connection id to other clients, as a malicious user could use it to mimic a request from that client.</span></span>

<a id="authoptions"></a>

## <a name="authentication-options-for-net-clients"></a><span data-ttu-id="ebbaf-165">Možnosti ověřování pro klienty .NET</span><span class="sxs-lookup"><span data-stu-id="ebbaf-165">Authentication options for .NET clients</span></span>

<span data-ttu-id="ebbaf-166">Pokud máte klienta .NET, jako je například konzolové aplikace, která komunikuje s rozbočovači, který je omezen na ověřené uživatele, můžete předat přihlašovacími údaji v souboru cookie, záhlaví připojení nebo certifikát.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-166">When you have a .NET client, such as a console app, which interacts with a hub that is limited to authenticated users, you can pass the authentication credentials in a cookie, the connection header, or a certificate.</span></span> <span data-ttu-id="ebbaf-167">Příklady v této části ukazují, jak použít tyto různé metody pro ověřování uživatele.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-167">The examples in this section show how to use those different methods for authenticating a user.</span></span> <span data-ttu-id="ebbaf-168">Nejsou plně funkční aplikace SignalR.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-168">They are not fully-functional SignalR apps.</span></span> <span data-ttu-id="ebbaf-169">Další informace o klientech .NET s knihovnou SignalR naleznete v tématu [pokyny k rozhraní API Center – klient .NET](../guide-to-the-api/hubs-api-guide-net-client.md).</span><span class="sxs-lookup"><span data-stu-id="ebbaf-169">For more information about .NET clients with SignalR, see [Hubs API Guide - .NET Client](../guide-to-the-api/hubs-api-guide-net-client.md).</span></span>

<a id="cookie"></a>

### <a name="cookie"></a><span data-ttu-id="ebbaf-170">Soubor cookie</span><span class="sxs-lookup"><span data-stu-id="ebbaf-170">Cookie</span></span>

<span data-ttu-id="ebbaf-171">Při vašeho klienta .NET interakci s rozbočovači, který používá ověřování pomocí formulářů ASP.NET, musíte ručně nastavit ověřovacího souboru cookie pro připojení.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-171">When your .NET client interacts with a hub that uses ASP.NET Forms Authentication, you will need to manually set the authentication cookie on the connection.</span></span> <span data-ttu-id="ebbaf-172">Přidání souboru cookie `CookieContainer` vlastnost [HubConnection](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.hubs.hubconnection(v=vs.111).aspx) objektu.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-172">You add the cookie to the `CookieContainer` property on the [HubConnection](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.hubs.hubconnection(v=vs.111).aspx) object.</span></span> <span data-ttu-id="ebbaf-173">Následující příklad ukazuje konzolovou aplikaci, která načte soubor cookie ověřování z webové stránky a přidá tento soubor cookie pro připojení.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-173">The following example shows a console app that retrieves an authentication cookie from a web page and adds that cookie to the connection.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample7.cs)]

<span data-ttu-id="ebbaf-174">Konzolová aplikace odesílá pověření, která mají <strong>www.contoso.com/RemoteLogin</strong> který může odkazovat na prázdnou stránku, která obsahuje následující soubor kódu na pozadí.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-174">The console app posts the credentials to <strong>www.contoso.com/RemoteLogin</strong> which could refer to an empty page that contains the following code-behind file.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample8.cs)]

<a id="windows"></a>

### <a name="windows-authentication"></a><span data-ttu-id="ebbaf-175">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="ebbaf-175">Windows authentication</span></span>

<span data-ttu-id="ebbaf-176">Pokud používáte ověřování Windows, můžete předat přihlašovacích údajů aktuálního uživatele pomocí [DefaultCredentials](https://msdn.microsoft.com/library/system.net.credentialcache.defaultcredentials.aspx) vlastnost.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-176">When using Windows authentication, you can pass the current user's credentials by using the [DefaultCredentials](https://msdn.microsoft.com/library/system.net.credentialcache.defaultcredentials.aspx) property.</span></span> <span data-ttu-id="ebbaf-177">Nastavit přihlašovací údaje pro připojení k hodnotě DefaultCredentials.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-177">You set the credentials for the connection to the value of the DefaultCredentials.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample9.cs?highlight=6)]

<a id="header"></a>

### <a name="connection-header"></a><span data-ttu-id="ebbaf-178">Připojení záhlaví</span><span class="sxs-lookup"><span data-stu-id="ebbaf-178">Connection header</span></span>

<span data-ttu-id="ebbaf-179">Pokud vaše aplikace nepoužívá soubory cookie, můžete předat informace o uživateli v hlavičce připojení.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-179">If your application is not using cookies, you can pass user information in the connection header.</span></span> <span data-ttu-id="ebbaf-180">Můžete například předat token v hlavičce připojení.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-180">For example, you can pass a token in the connection header.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample10.cs?highlight=6)]

<span data-ttu-id="ebbaf-181">Poté v rozbočovači, bude ověřit token uživatele.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-181">Then, in the hub, you would verify the user's token.</span></span>

<a id="certificate"></a>

### <a name="certificate"></a><span data-ttu-id="ebbaf-182">Certifikát</span><span class="sxs-lookup"><span data-stu-id="ebbaf-182">Certificate</span></span>

<span data-ttu-id="ebbaf-183">Můžete předat klientský certifikát k ověření uživatele.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-183">You can pass a client certificate to verify the user.</span></span> <span data-ttu-id="ebbaf-184">Přidání certifikátu při vytváření připojení.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-184">You add the certificate when creating the connection.</span></span> <span data-ttu-id="ebbaf-185">Následující příklad ukazuje jenom postup přidání klientský certifikát pro připojení. nezobrazuje se úplná konzolovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-185">The following example shows only how to add a client certificate to the connection; it does not show the full console app.</span></span> <span data-ttu-id="ebbaf-186">Používá [certifikátu x 509](https://msdn.microsoft.com/library/system.security.cryptography.x509certificates.x509certificate.aspx) třída, která poskytuje několik způsobů vytvoření certifikátu.</span><span class="sxs-lookup"><span data-stu-id="ebbaf-186">It uses the [X509Certificate](https://msdn.microsoft.com/library/system.security.cryptography.x509certificates.x509certificate.aspx) class which provides several different ways to create the certificate.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample11.cs?highlight=6)]
