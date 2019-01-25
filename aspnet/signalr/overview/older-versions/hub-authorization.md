---
uid: signalr/overview/older-versions/hub-authorization
title: Ověřování a autorizace Center SignalR (SignalR 1.x) | Dokumentace Microsoftu
author: bradygaster
description: Toto téma popisuje, jak omezit, kteří uživatelé nebo rolí se dostanete metod rozbočovače.
ms.author: bradyg
ms.date: 10/17/2013
ms.assetid: 3d2dfc0e-eac2-4076-a468-325d3d01cc7b
msc.legacyurl: /signalr/overview/older-versions/hub-authorization
msc.type: authoredcontent
ms.openlocfilehash: 7f4a76109111f19dc4381ad01e642afdabade336
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836896"
---
<a name="authentication-and-authorization-for-signalr-hubs-signalr-1x"></a><span data-ttu-id="ac904-103">Ověřování a autorizace center SignalR (SignalR 1.x)</span><span class="sxs-lookup"><span data-stu-id="ac904-103">Authentication and Authorization for SignalR Hubs (SignalR 1.x)</span></span>
====================
<span data-ttu-id="ac904-104">podle [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="ac904-104">by [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="ac904-105">Toto téma popisuje, jak omezit, kteří uživatelé nebo rolí se dostanete metod rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="ac904-105">This topic describes how to restrict which users or roles can access hub methods.</span></span>


## <a name="overview"></a><span data-ttu-id="ac904-106">Přehled</span><span class="sxs-lookup"><span data-stu-id="ac904-106">Overview</span></span>

<span data-ttu-id="ac904-107">Toto téma obsahuje následující oddíly:</span><span class="sxs-lookup"><span data-stu-id="ac904-107">This topic contains the following sections:</span></span>

- [<span data-ttu-id="ac904-108">Povolit atribut</span><span class="sxs-lookup"><span data-stu-id="ac904-108">Authorize attribute</span></span>](#authorizeattribute)
- [<span data-ttu-id="ac904-109">Vyžadovat ověřování pro všechna centra</span><span class="sxs-lookup"><span data-stu-id="ac904-109">Require authentication for all hubs</span></span>](#requireauth)
- [<span data-ttu-id="ac904-110">Vlastní autorizace</span><span class="sxs-lookup"><span data-stu-id="ac904-110">Customized authorization</span></span>](#custom)
- [<span data-ttu-id="ac904-111">Předat ověřovací informace pro klienty</span><span class="sxs-lookup"><span data-stu-id="ac904-111">Pass authentication information to clients</span></span>](#passauth)
- [<span data-ttu-id="ac904-112">Možnosti ověřování pro klienty .NET</span><span class="sxs-lookup"><span data-stu-id="ac904-112">Authentication options for .NET clients</span></span>](#authoptions)

    - [<span data-ttu-id="ac904-113">Soubor cookie ověřování pomocí formulářů</span><span class="sxs-lookup"><span data-stu-id="ac904-113">Cookie with Forms Authentication</span></span>](#cookie)
    - [<span data-ttu-id="ac904-114">Ověřování Windows</span><span class="sxs-lookup"><span data-stu-id="ac904-114">Windows Authentication</span></span>](#windows)
    - [<span data-ttu-id="ac904-115">Připojení záhlaví</span><span class="sxs-lookup"><span data-stu-id="ac904-115">Connection header</span></span>](#header)
    - [<span data-ttu-id="ac904-116">Certifikát</span><span class="sxs-lookup"><span data-stu-id="ac904-116">Certificate</span></span>](#certificate)

<a id="authorizeattribute"></a>

## <a name="authorize-attribute"></a><span data-ttu-id="ac904-117">Povolit atribut</span><span class="sxs-lookup"><span data-stu-id="ac904-117">Authorize attribute</span></span>

<span data-ttu-id="ac904-118">Funkce SignalR poskytuje [Authorize](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.authorizeattribute(v=vs.111).aspx) atribut určit, které uživatele nebo role mají přístup do centra nebo metody.</span><span class="sxs-lookup"><span data-stu-id="ac904-118">SignalR provides the [Authorize](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.authorizeattribute(v=vs.111).aspx) attribute to specify which users or roles have access to a hub or method.</span></span> <span data-ttu-id="ac904-119">Tento atribut je umístěn v `Microsoft.AspNet.SignalR` oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="ac904-119">This attribute is located in the `Microsoft.AspNet.SignalR` namespace.</span></span> <span data-ttu-id="ac904-120">Můžete použít `Authorize` atribut rozbočovač nebo konkrétní metody v rozbočovači.</span><span class="sxs-lookup"><span data-stu-id="ac904-120">You apply the `Authorize` attribute to either a hub or particular methods in a hub.</span></span> <span data-ttu-id="ac904-121">Pokud použijete `Authorize` atribut třídy rozbočovače, požadavek na autorizaci zadané platí pro všechny metody v rozbočovači.</span><span class="sxs-lookup"><span data-stu-id="ac904-121">When you apply the `Authorize` attribute to a hub class, the specified authorization requirement is applied to all of the methods in the hub.</span></span> <span data-ttu-id="ac904-122">Ověření požadavků, které můžete použít různé typy jsou uvedeny níže.</span><span class="sxs-lookup"><span data-stu-id="ac904-122">The different types of authorization requirements that you can apply are shown below.</span></span> <span data-ttu-id="ac904-123">Bez `Authorize` atribut, všechny veřejné metody v rozbočovači jsou k dispozici pro klienta, který je připojený k rozbočovači.</span><span class="sxs-lookup"><span data-stu-id="ac904-123">Without the `Authorize` attribute, all public methods on the hub are available to a client that is connected to the hub.</span></span>

<span data-ttu-id="ac904-124">Pokud jste definovali role ve vaší webové aplikaci s názvem "Admin", můžete zadat, že jenom uživatelé v této roli můžete přístup k centru s následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="ac904-124">If you have defined a role named "Admin" in your web application, you could specify that only users in that role can access a hub with the following code.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample1.cs)]

<span data-ttu-id="ac904-125">Nebo můžete určit, že Centrum obsahuje jednu metodu, která je k dispozici všem uživatelům a druhá metoda, která je pouze ověřeným uživatelům k dispozici, jak je znázorněno níže.</span><span class="sxs-lookup"><span data-stu-id="ac904-125">Or, you can specify that a hub contains one method that is available to all users, and a second method that is only available to authenticated users, as shown below.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample2.cs)]

<span data-ttu-id="ac904-126">Následující příklady různých autorizace situacích:</span><span class="sxs-lookup"><span data-stu-id="ac904-126">The following examples address different authorization scenarios:</span></span>

- <span data-ttu-id="ac904-127">`[Authorize]` – jen ověření uživatelé</span><span class="sxs-lookup"><span data-stu-id="ac904-127">`[Authorize]` – only authenticated users</span></span>
- <span data-ttu-id="ac904-128">`[Authorize(Roles = "Admin,Manager")]` – jen ověření uživatelé v zadaných rolí</span><span class="sxs-lookup"><span data-stu-id="ac904-128">`[Authorize(Roles = "Admin,Manager")]` – only authenticated users in the specified roles</span></span>
- <span data-ttu-id="ac904-129">`[Authorize(Users = "user1,user2")]` – pouze pro ověřené uživatele s ze zadaných uživatelských jmen</span><span class="sxs-lookup"><span data-stu-id="ac904-129">`[Authorize(Users = "user1,user2")]` – only authenticated users with the specified user names</span></span>
- <span data-ttu-id="ac904-130">`[Authorize(RequireOutgoing=false)]` – pouze pro ověřené uživatele můžete vyvolat centra, ale volání ze serveru zpátky do klientů nejsou omezeny autorizace, jako je třeba když jenom určitým uživatelům můžete odeslat zprávu, ale všechny ostatní zprávy.</span><span class="sxs-lookup"><span data-stu-id="ac904-130">`[Authorize(RequireOutgoing=false)]` – only authenticated users can invoke the hub, but calls from the server back to clients are not limited by authorization, such as, when only certain users can send a message but all others can receive the message.</span></span> <span data-ttu-id="ac904-131">Vlastnost RequireOutgoing dá používat jedině pro celé centrum není pro jednotlivce metody v rozbočovači.</span><span class="sxs-lookup"><span data-stu-id="ac904-131">The RequireOutgoing property can only be applied to the entire hub, not on individuals methods within the hub.</span></span> <span data-ttu-id="ac904-132">Když RequireOutgoing není nastavená na hodnotu false, jenom uživatelé, kteří splní požadavek na autorizaci se nazývají ze serveru.</span><span class="sxs-lookup"><span data-stu-id="ac904-132">When RequireOutgoing is not set to false, only users that meet the authorization requirement are called from the server.</span></span>

<a id="requireauth"></a>

## <a name="require-authentication-for-all-hubs"></a><span data-ttu-id="ac904-133">Vyžadovat ověřování pro všechna centra</span><span class="sxs-lookup"><span data-stu-id="ac904-133">Require authentication for all hubs</span></span>

<span data-ttu-id="ac904-134">Můžete vyžadovat ověřování pro všechny rozbočovače a metody ve vaší aplikaci pomocí volání [RequireAuthentication](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubpipelineextensions.requireauthentication(v=vs.111).aspx) metoda při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="ac904-134">You can require authentication for all hubs and hub methods in your application by calling the [RequireAuthentication](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubpipelineextensions.requireauthentication(v=vs.111).aspx) method when the application starts.</span></span> <span data-ttu-id="ac904-135">Tuto metodu můžete použít, když máte více rozbočovače a chcete vynutit požadavek na ověření pro všechny z nich.</span><span class="sxs-lookup"><span data-stu-id="ac904-135">You might use this method when you have multiple hubs and want to enforce an authentication requirement for all of them.</span></span> <span data-ttu-id="ac904-136">S touto metodou nelze zadat role, uživatele nebo odchozí autorizace.</span><span class="sxs-lookup"><span data-stu-id="ac904-136">With this method, you cannot specify role, user, or outgoing authorization.</span></span> <span data-ttu-id="ac904-137">Můžete zadat pouze omezen přístup k metodám centra ověřeným uživatelům.</span><span class="sxs-lookup"><span data-stu-id="ac904-137">You can only specify that access to the hub methods is restricted to authenticated users.</span></span> <span data-ttu-id="ac904-138">Atribut Authorize však můžete použít na rozbočovače a metody zadat další požadavky.</span><span class="sxs-lookup"><span data-stu-id="ac904-138">However, you can still apply the Authorize attribute to hubs or methods to specify additional requirements.</span></span> <span data-ttu-id="ac904-139">Všechny požadavky, které zadáte v atributech platí kromě základní požadavek ověřování.</span><span class="sxs-lookup"><span data-stu-id="ac904-139">Any requirement you specify in attributes is applied in addition to the basic requirement of authentication.</span></span>

<span data-ttu-id="ac904-140">Následující příklad ukazuje soubor Global.asax, který omezuje všech metod rozbočovače na ověřeným uživatelům.</span><span class="sxs-lookup"><span data-stu-id="ac904-140">The following example shows a Global.asax file which restricts all hub methods to authenticated users.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample3.cs)]

<span data-ttu-id="ac904-141">Při volání `RequireAuthentication()` metoda po zpracování žádost SignalR, vyvolá výjimku SignalR `InvalidOperationException` výjimky.</span><span class="sxs-lookup"><span data-stu-id="ac904-141">If you call the `RequireAuthentication()` method after a SignalR request has been processed, SignalR will throw a `InvalidOperationException` exception.</span></span> <span data-ttu-id="ac904-142">Tato výjimka je vyvolána, protože modul nelze přidat do kanálu rozbočovače přidán po zavolání kanálu.</span><span class="sxs-lookup"><span data-stu-id="ac904-142">This exception is thrown because you cannot add a module to the HubPipeline after the pipeline has been invoked.</span></span> <span data-ttu-id="ac904-143">Předchozí příklad ukazuje volání `RequireAuthentication` metodu `Application_Start` metodu, která provádí jednou před prvního požadavku na zpracování.</span><span class="sxs-lookup"><span data-stu-id="ac904-143">The previous example shows calling the `RequireAuthentication` method in the `Application_Start` method which is executed one time prior to handling the first request.</span></span>

<a id="custom"></a>

## <a name="customized-authorization"></a><span data-ttu-id="ac904-144">Vlastní autorizace</span><span class="sxs-lookup"><span data-stu-id="ac904-144">Customized authorization</span></span>

<span data-ttu-id="ac904-145">Pokud je potřeba upravit, jak se určují autorizaci, můžete vytvořit třídu, která je odvozena z `AuthorizeAttribute` a přepsat [UserAuthorized](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.authorizeattribute.userauthorized(v=vs.111).aspx) metody.</span><span class="sxs-lookup"><span data-stu-id="ac904-145">If you need to customize how authorization is determined, you can create a class that derives from `AuthorizeAttribute` and override the [UserAuthorized](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.authorizeattribute.userauthorized(v=vs.111).aspx) method.</span></span> <span data-ttu-id="ac904-146">Tato metoda je volána pro každý požadavek pro určení, zda je uživatel oprávnění k dokončení požadavku.</span><span class="sxs-lookup"><span data-stu-id="ac904-146">This method is called for each request to determine whether the user is authorized to complete the request.</span></span> <span data-ttu-id="ac904-147">Přepsané metody poskytují logiku potřebnou pro váš scénář autorizace.</span><span class="sxs-lookup"><span data-stu-id="ac904-147">In the overridden method, you provide the necessary logic for your authorization scenario.</span></span> <span data-ttu-id="ac904-148">Následující příklad ukazuje, jak vynutit autorizaci prostřednictvím založené na deklaracích identity.</span><span class="sxs-lookup"><span data-stu-id="ac904-148">The following example shows how to enforce authorization through claims-based identity.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample4.cs)]

<a id="passauth"></a>

## <a name="pass-authentication-information-to-clients"></a><span data-ttu-id="ac904-149">Předat ověřovací informace pro klienty</span><span class="sxs-lookup"><span data-stu-id="ac904-149">Pass authentication information to clients</span></span>

<span data-ttu-id="ac904-150">Budete muset použít informace o ověřování v kódu, který běží na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="ac904-150">You may need to use authentication information in the code that runs on the client.</span></span> <span data-ttu-id="ac904-151">Předáním požadovaných informací při volání metody na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="ac904-151">You pass the required information when calling the methods on the client.</span></span> <span data-ttu-id="ac904-152">Například metoda aplikace chat mohli předat jako parametr uživatelské jméno osoby, odesílání zpráv, jak je znázorněno níže.</span><span class="sxs-lookup"><span data-stu-id="ac904-152">For example, a chat application method could pass as a parameter the user name of the person posting a message, as shown below.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample5.cs)]

<span data-ttu-id="ac904-153">Nebo můžete vytvořit objekt reprezentující informace o ověřování a tento objekt předat jako parametr, jak je znázorněno níže.</span><span class="sxs-lookup"><span data-stu-id="ac904-153">Or, you can create an object to represent the authentication information and pass that object as a parameter, as shown below.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample6.cs)]

<span data-ttu-id="ac904-154">Nikdy byste neměli předávat id připojení pro jednoho klienta jiným klientům, protože ji uživatel se zlými úmysly třeba použít tak, aby napodoboval žádosti od tohoto klienta.</span><span class="sxs-lookup"><span data-stu-id="ac904-154">You should never pass one client's connection id to other clients, as a malicious user could use it to mimic a request from that client.</span></span>

<a id="authoptions"></a>

## <a name="authentication-options-for-net-clients"></a><span data-ttu-id="ac904-155">Možnosti ověřování pro klienty .NET</span><span class="sxs-lookup"><span data-stu-id="ac904-155">Authentication options for .NET clients</span></span>

<span data-ttu-id="ac904-156">Pokud máte klienta .NET, jako je například konzolové aplikace, která komunikuje s rozbočovači, který je omezen na ověřené uživatele, můžete předat přihlašovacími údaji v souboru cookie, záhlaví připojení nebo certifikát.</span><span class="sxs-lookup"><span data-stu-id="ac904-156">When you have a .NET client, such as a console app, which interacts with a hub that is limited to authenticated users, you can pass the authentication credentials in a cookie, the connection header, or a certificate.</span></span> <span data-ttu-id="ac904-157">Příklady v této části ukazují, jak použít tyto různé metody pro ověřování uživatele.</span><span class="sxs-lookup"><span data-stu-id="ac904-157">The examples in this section show how to use those different methods for authenticating a user.</span></span> <span data-ttu-id="ac904-158">Nejsou plně funkční aplikace SignalR.</span><span class="sxs-lookup"><span data-stu-id="ac904-158">They are not fully-functional SignalR apps.</span></span> <span data-ttu-id="ac904-159">Další informace o klientech .NET s knihovnou SignalR naleznete v tématu [pokyny k rozhraní API Center – klient .NET](../guide-to-the-api/hubs-api-guide-net-client.md).</span><span class="sxs-lookup"><span data-stu-id="ac904-159">For more information about .NET clients with SignalR, see [Hubs API Guide - .NET Client](../guide-to-the-api/hubs-api-guide-net-client.md).</span></span>

<a id="cookie"></a>

### <a name="cookie"></a><span data-ttu-id="ac904-160">Soubor cookie</span><span class="sxs-lookup"><span data-stu-id="ac904-160">Cookie</span></span>

<span data-ttu-id="ac904-161">Při vašeho klienta .NET interakci s rozbočovači, který používá ověřování pomocí formulářů ASP.NET, musíte ručně nastavit ověřovacího souboru cookie pro připojení.</span><span class="sxs-lookup"><span data-stu-id="ac904-161">When your .NET client interacts with a hub that uses ASP.NET Forms Authentication, you will need to manually set the authentication cookie on the connection.</span></span> <span data-ttu-id="ac904-162">Přidání souboru cookie `CookieContainer` vlastnost [HubConnection](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.hubs.hubconnection(v=vs.111).aspx) objektu.</span><span class="sxs-lookup"><span data-stu-id="ac904-162">You add the cookie to the `CookieContainer` property on the [HubConnection](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.hubs.hubconnection(v=vs.111).aspx) object.</span></span> <span data-ttu-id="ac904-163">Následující příklad ukazuje konzolovou aplikaci, která načte soubor cookie ověřování z webové stránky a přidá tento soubor cookie pro připojení.</span><span class="sxs-lookup"><span data-stu-id="ac904-163">The following example shows a console app that retrieves an authentication cookie from a web page and adds that cookie to the connection.</span></span> <span data-ttu-id="ac904-164">Adresa URL `https://www.contoso.com/RemoteLogin` v příkladu odkazuje na webovou stránku, která je potřeba vytvořit.</span><span class="sxs-lookup"><span data-stu-id="ac904-164">The URL `https://www.contoso.com/RemoteLogin` in the example points to a web page that you would need to create.</span></span> <span data-ttu-id="ac904-165">Na stránce by načíst odeslaných uživatelské jméno a heslo a pokus o přihlášení uživatele s přihlašovacími údaji.</span><span class="sxs-lookup"><span data-stu-id="ac904-165">The page would retrieve the posted user name and password, and attempt to log in the user with the credentials.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample7.cs)]

<span data-ttu-id="ac904-166">Konzolová aplikace odesílá pověření, která mají www.contoso.com/RemoteLogin který může odkazovat na prázdnou stránku, která obsahuje následující soubor kódu na pozadí.</span><span class="sxs-lookup"><span data-stu-id="ac904-166">The console app posts the credentials to www.contoso.com/RemoteLogin which could refer to an empty page that contains the following code-behind file.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample8.cs)]

<a id="windows"></a>

### <a name="windows-authentication"></a><span data-ttu-id="ac904-167">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="ac904-167">Windows authentication</span></span>

<span data-ttu-id="ac904-168">Pokud používáte ověřování Windows, můžete předat přihlašovacích údajů aktuálního uživatele pomocí [DefaultCredentials](https://msdn.microsoft.com/library/system.net.credentialcache.defaultcredentials.aspx) vlastnost.</span><span class="sxs-lookup"><span data-stu-id="ac904-168">When using Windows authentication, you can pass the current user's credentials by using the [DefaultCredentials](https://msdn.microsoft.com/library/system.net.credentialcache.defaultcredentials.aspx) property.</span></span> <span data-ttu-id="ac904-169">Nastavit přihlašovací údaje pro připojení k hodnotě DefaultCredentials.</span><span class="sxs-lookup"><span data-stu-id="ac904-169">You set the credentials for the connection to the value of the DefaultCredentials.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample9.cs?highlight=6)]

<a id="header"></a>

### <a name="connection-header"></a><span data-ttu-id="ac904-170">Připojení záhlaví</span><span class="sxs-lookup"><span data-stu-id="ac904-170">Connection header</span></span>

<span data-ttu-id="ac904-171">Pokud vaše aplikace nepoužívá soubory cookie, můžete předat informace o uživateli v hlavičce připojení.</span><span class="sxs-lookup"><span data-stu-id="ac904-171">If your application is not using cookies, you can pass user information in the connection header.</span></span> <span data-ttu-id="ac904-172">Můžete například předat token v hlavičce připojení.</span><span class="sxs-lookup"><span data-stu-id="ac904-172">For example, you can pass a token in the connection header.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample10.cs?highlight=6)]

<span data-ttu-id="ac904-173">Poté v rozbočovači, bude ověřit token uživatele.</span><span class="sxs-lookup"><span data-stu-id="ac904-173">Then, in the hub, you would verify the user's token.</span></span>

<a id="certificate"></a>

### <a name="certificate"></a><span data-ttu-id="ac904-174">Certifikát</span><span class="sxs-lookup"><span data-stu-id="ac904-174">Certificate</span></span>

<span data-ttu-id="ac904-175">Můžete předat klientský certifikát k ověření uživatele.</span><span class="sxs-lookup"><span data-stu-id="ac904-175">You can pass a client certificate to verify the user.</span></span> <span data-ttu-id="ac904-176">Přidání certifikátu při vytváření připojení.</span><span class="sxs-lookup"><span data-stu-id="ac904-176">You add the certificate when creating the connection.</span></span> <span data-ttu-id="ac904-177">Následující příklad ukazuje jenom postup přidání klientský certifikát pro připojení. nezobrazuje se úplná konzolovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ac904-177">The following example shows only how to add a client certificate to the connection; it does not show the full console app.</span></span> <span data-ttu-id="ac904-178">Používá [certifikátu x 509](https://msdn.microsoft.com/library/system.security.cryptography.x509certificates.x509certificate.aspx) třída, která poskytuje několik způsobů vytvoření certifikátu.</span><span class="sxs-lookup"><span data-stu-id="ac904-178">It uses the [X509Certificate](https://msdn.microsoft.com/library/system.security.cryptography.x509certificates.x509certificate.aspx) class which provides several different ways to create the certificate.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample11.cs?highlight=6)]
