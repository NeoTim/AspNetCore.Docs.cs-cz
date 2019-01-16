---
title: Stav relace a aplikace v ASP.NET Core
author: rick-anderson
description: Objevte přístupy k zachování stavu relace a aplikace mezi požadavky.
ms.author: riande
ms.custom: mvc
ms.date: 06/14/2018
uid: fundamentals/app-state
ms.openlocfilehash: 23bbb3063d696f588abecb1fe05ea3381ab2346d
ms.sourcegitcommit: 42a8164b8aba21f322ffefacb92301bdfb4d3c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54341690"
---
# <a name="session-and-app-state-in-aspnet-core"></a><span data-ttu-id="7c57b-103">Stav relace a aplikace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7c57b-103">Session and app state in ASP.NET Core</span></span>

<span data-ttu-id="7c57b-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), [Diana LaRose](https://github.com/DianaLaRose), a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7c57b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), [Diana LaRose](https://github.com/DianaLaRose), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="7c57b-105">HTTP je Bezstavová protokol.</span><span class="sxs-lookup"><span data-stu-id="7c57b-105">HTTP is a stateless protocol.</span></span> <span data-ttu-id="7c57b-106">Bez dalších kroků, požadavky HTTP jsou nezávislé zprávy, které nechcete zachovat hodnoty uživatele nebo stav aplikace.</span><span class="sxs-lookup"><span data-stu-id="7c57b-106">Without taking additional steps, HTTP requests are independent messages that don't retain user values or app state.</span></span> <span data-ttu-id="7c57b-107">Tento článek popisuje několik přístupů, které chcete zachovat data a aplikace stavu mezi žádostí uživatele.</span><span class="sxs-lookup"><span data-stu-id="7c57b-107">This article describes several approaches to preserve user data and app state between requests.</span></span>

<span data-ttu-id="7c57b-108">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/app-state/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7c57b-108">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/app-state/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="state-management"></a><span data-ttu-id="7c57b-109">Správa stavu</span><span class="sxs-lookup"><span data-stu-id="7c57b-109">State management</span></span>

<span data-ttu-id="7c57b-110">Stav může být uložen několik přístupů.</span><span class="sxs-lookup"><span data-stu-id="7c57b-110">State can be stored using several approaches.</span></span> <span data-ttu-id="7c57b-111">Každý přístup je popsána dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="7c57b-111">Each approach is described later in this topic.</span></span>

| <span data-ttu-id="7c57b-112">Přístup k ukládání</span><span class="sxs-lookup"><span data-stu-id="7c57b-112">Storage approach</span></span> | <span data-ttu-id="7c57b-113">Mechanismus úložiště</span><span class="sxs-lookup"><span data-stu-id="7c57b-113">Storage mechanism</span></span> |
| ---------------- | ----------------- |
| [<span data-ttu-id="7c57b-114">Soubory cookie</span><span class="sxs-lookup"><span data-stu-id="7c57b-114">Cookies</span></span>](#cookies) | <span data-ttu-id="7c57b-115">Soubory cookie protokolu HTTP (můžou zahrnovat data uložená pomocí kódu aplikace na straně serveru)</span><span class="sxs-lookup"><span data-stu-id="7c57b-115">HTTP cookies (may include data stored using server-side app code)</span></span> |
| [<span data-ttu-id="7c57b-116">Stav relace</span><span class="sxs-lookup"><span data-stu-id="7c57b-116">Session state</span></span>](#session-state) | <span data-ttu-id="7c57b-117">Soubory cookie protokolu HTTP a kódu aplikace na straně serveru</span><span class="sxs-lookup"><span data-stu-id="7c57b-117">HTTP cookies and server-side app code</span></span> |
| [<span data-ttu-id="7c57b-118">TempData</span><span class="sxs-lookup"><span data-stu-id="7c57b-118">TempData</span></span>](#tempdata) | <span data-ttu-id="7c57b-119">Soubory cookie protokolu HTTP nebo stav relace</span><span class="sxs-lookup"><span data-stu-id="7c57b-119">HTTP cookies or session state</span></span> |
| [<span data-ttu-id="7c57b-120">Řetězce dotazů</span><span class="sxs-lookup"><span data-stu-id="7c57b-120">Query strings</span></span>](#query-strings) | <span data-ttu-id="7c57b-121">Řetězce dotazu HTTP</span><span class="sxs-lookup"><span data-stu-id="7c57b-121">HTTP query strings</span></span> |
| [<span data-ttu-id="7c57b-122">Skrytá pole</span><span class="sxs-lookup"><span data-stu-id="7c57b-122">Hidden fields</span></span>](#hidden-fields) | <span data-ttu-id="7c57b-123">Pole formuláře HTTP</span><span class="sxs-lookup"><span data-stu-id="7c57b-123">HTTP form fields</span></span> |
| [<span data-ttu-id="7c57b-124">HttpContext.Items</span><span class="sxs-lookup"><span data-stu-id="7c57b-124">HttpContext.Items</span></span>](#httpcontextitems) | <span data-ttu-id="7c57b-125">Kód aplikace na straně serveru</span><span class="sxs-lookup"><span data-stu-id="7c57b-125">Server-side app code</span></span> |
| [<span data-ttu-id="7c57b-126">Mezipaměť</span><span class="sxs-lookup"><span data-stu-id="7c57b-126">Cache</span></span>](#cache) | <span data-ttu-id="7c57b-127">Kód aplikace na straně serveru</span><span class="sxs-lookup"><span data-stu-id="7c57b-127">Server-side app code</span></span> |
| [<span data-ttu-id="7c57b-128">Injektáž závislostí</span><span class="sxs-lookup"><span data-stu-id="7c57b-128">Dependency Injection</span></span>](#dependency-injection) | <span data-ttu-id="7c57b-129">Kód aplikace na straně serveru</span><span class="sxs-lookup"><span data-stu-id="7c57b-129">Server-side app code</span></span> |

## <a name="cookies"></a><span data-ttu-id="7c57b-130">Soubory cookie</span><span class="sxs-lookup"><span data-stu-id="7c57b-130">Cookies</span></span>

<span data-ttu-id="7c57b-131">Soubory cookie ukládat data napříč požadavky.</span><span class="sxs-lookup"><span data-stu-id="7c57b-131">Cookies store data across requests.</span></span> <span data-ttu-id="7c57b-132">Protože soubory cookie jsou odesílány při každé žádosti, jejich velikosti by měla omezit na minimum.</span><span class="sxs-lookup"><span data-stu-id="7c57b-132">Because cookies are sent with every request, their size should be kept to a minimum.</span></span> <span data-ttu-id="7c57b-133">V ideálním případě by měla pouze identifikátor uložena do souboru cookie s daty uloženými v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7c57b-133">Ideally, only an identifier should be stored in a cookie with the data stored by the app.</span></span> <span data-ttu-id="7c57b-134">Většina prohlížečů omezit velikost souboru cookie do 4096 bajtů.</span><span class="sxs-lookup"><span data-stu-id="7c57b-134">Most browsers restrict cookie size to 4096 bytes.</span></span> <span data-ttu-id="7c57b-135">Pouze omezený počet soubory cookie jsou k dispozici pro každou doménu.</span><span class="sxs-lookup"><span data-stu-id="7c57b-135">Only a limited number of cookies are available for each domain.</span></span>

<span data-ttu-id="7c57b-136">Protože soubory cookie jsou v souladu s úmyslné poškozování, musí být ověřené aplikací.</span><span class="sxs-lookup"><span data-stu-id="7c57b-136">Because cookies are subject to tampering, they must be validated by the app.</span></span> <span data-ttu-id="7c57b-137">Soubory cookie může odstranit uživatelů a vypršení platnosti na klientských počítačích.</span><span class="sxs-lookup"><span data-stu-id="7c57b-137">Cookies can be deleted by users and expire on clients.</span></span> <span data-ttu-id="7c57b-138">Soubory cookie jsou však obvykle nejvíce trvalý formou trvalost dat na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="7c57b-138">However, cookies are generally the most durable form of data persistence on the client.</span></span>

<span data-ttu-id="7c57b-139">Soubory cookie se často používají pro přizpůsobení, kde je obsah přizpůsobit pro známé uživatele.</span><span class="sxs-lookup"><span data-stu-id="7c57b-139">Cookies are often used for personalization, where content is customized for a known user.</span></span> <span data-ttu-id="7c57b-140">Uživatel je identifikovat jen a ve většině případů není ověřen.</span><span class="sxs-lookup"><span data-stu-id="7c57b-140">The user is only identified and not authenticated in most cases.</span></span> <span data-ttu-id="7c57b-141">Soubor cookie může ukládat jméno uživatele, název účtu nebo jedinečné ID uživatele (například identifikátor GUID).</span><span class="sxs-lookup"><span data-stu-id="7c57b-141">The cookie can store the user's name, account name, or unique user ID (such as a GUID).</span></span> <span data-ttu-id="7c57b-142">Pak můžete soubor cookie pro přístup k individuální nastavení uživatele, jako je barva pozadí své upřednostňované webu.</span><span class="sxs-lookup"><span data-stu-id="7c57b-142">You can then use the cookie to access the user's personalized settings, such as their preferred website background color.</span></span>

<span data-ttu-id="7c57b-143">Mějte na paměti o [Evropské unie obecného nařízení (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection) při vydávání soubory cookie a týkající se ochrany osobních údajů se vztahuje na.</span><span class="sxs-lookup"><span data-stu-id="7c57b-143">Be mindful of the [European Union General Data Protection Regulations (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection) when issuing cookies and dealing with privacy concerns.</span></span> <span data-ttu-id="7c57b-144">Další informace najdete v tématu [podpora obecného Regulation (GDPR) v ASP.NET Core](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="7c57b-144">For more information, see [General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr).</span></span>

## <a name="session-state"></a><span data-ttu-id="7c57b-145">Stav relace</span><span class="sxs-lookup"><span data-stu-id="7c57b-145">Session state</span></span>

<span data-ttu-id="7c57b-146">Stav relace je scénář ASP.NET Core určené k ukládání uživatelských dat, zatímco uživatel prochází webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="7c57b-146">Session state is an ASP.NET Core scenario for storage of user data while the user browses a web app.</span></span> <span data-ttu-id="7c57b-147">Stav relace používá úložiště udržovat aplikace pro uchovávání dat napříč požadavky klientů.</span><span class="sxs-lookup"><span data-stu-id="7c57b-147">Session state uses a store maintained by the app to persist data across requests from a client.</span></span> <span data-ttu-id="7c57b-148">Data relace je založená na mezipaměti a považují za dočasné data&mdash;webu by měl dál fungovat bez data relace.</span><span class="sxs-lookup"><span data-stu-id="7c57b-148">The session data is backed by a cache and considered ephemeral data&mdash;the site should continue to function without the session data.</span></span>

> [!NOTE]
> <span data-ttu-id="7c57b-149">Relace není podporována v [SignalR](xref:signalr/index) aplikace protože [rozbočovače SignalR](xref:signalr/hubs) se dá provádět nezávislé kontextu HTTP.</span><span class="sxs-lookup"><span data-stu-id="7c57b-149">Session isn't supported in [SignalR](xref:signalr/index) apps because a [SignalR Hub](xref:signalr/hubs) may execute independent of an HTTP context.</span></span> <span data-ttu-id="7c57b-150">Například tato situace může nastat při dlouho dotazování požadavku je otevřena hub za dobu života kontext žádosti HTTP.</span><span class="sxs-lookup"><span data-stu-id="7c57b-150">For example, this can occur when a long polling request is held open by a hub beyond the lifetime of the request's HTTP context.</span></span>

<span data-ttu-id="7c57b-151">ASP.NET Core udržuje stav relace tím, že poskytuje soubor cookie klientovi, který obsahuje Identifikátor relace, která se odesílá do aplikace spolu s každou žádostí.</span><span class="sxs-lookup"><span data-stu-id="7c57b-151">ASP.NET Core maintains session state by providing a cookie to the client that contains a session ID, which is sent to the app with each request.</span></span> <span data-ttu-id="7c57b-152">Aplikace použije Identifikátor relace k načtení dat relace.</span><span class="sxs-lookup"><span data-stu-id="7c57b-152">The app uses the session ID to fetch the session data.</span></span>

<span data-ttu-id="7c57b-153">Stav relace je třeba následujícího chování:</span><span class="sxs-lookup"><span data-stu-id="7c57b-153">Session state exhibits the following behaviors:</span></span>

* <span data-ttu-id="7c57b-154">Protože soubor cookie relace je specifická pro prohlížeče, relace nejsou sdíleny napříč prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="7c57b-154">Because the session cookie is specific to the browser, sessions aren't shared across browsers.</span></span>
* <span data-ttu-id="7c57b-155">Soubory cookie relace se odstraní při ukončení relace prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="7c57b-155">Session cookies are deleted when the browser session ends.</span></span>
* <span data-ttu-id="7c57b-156">Pokud pro relaci vypršela platnost souboru cookie, je vytvořit novou relaci, která používá stejný soubor cookie relace.</span><span class="sxs-lookup"><span data-stu-id="7c57b-156">If a cookie is received for an expired session, a new session is created that uses the same session cookie.</span></span>
* <span data-ttu-id="7c57b-157">Prázdná relace nejsou zachovány&mdash;relace musí mít alespoň jednu hodnotu sady k uchování relace napříč požadavky.</span><span class="sxs-lookup"><span data-stu-id="7c57b-157">Empty sessions aren't retained&mdash;the session must have at least one value set into it to persist the session across requests.</span></span> <span data-ttu-id="7c57b-158">Pokud relace není zachována, nové ID relace se vygeneruje pro každý nový požadavek.</span><span class="sxs-lookup"><span data-stu-id="7c57b-158">When a session isn't retained, a new session ID is generated for each new request.</span></span>
* <span data-ttu-id="7c57b-159">Aplikace si zachová relace po omezenou dobu od poslední žádosti.</span><span class="sxs-lookup"><span data-stu-id="7c57b-159">The app retains a session for a limited time after the last request.</span></span> <span data-ttu-id="7c57b-160">Aplikace nastaví časový limit relace nebo výchozí hodnotu 20 minut.</span><span class="sxs-lookup"><span data-stu-id="7c57b-160">The app either sets the session timeout or uses the default value of 20 minutes.</span></span> <span data-ttu-id="7c57b-161">Stav relace je ideální pro ukládání uživatelských dat, která je specifická pro konkrétní relace, ale pokud dat nevyžaduje, aby trvalého úložiště napříč relacemi.</span><span class="sxs-lookup"><span data-stu-id="7c57b-161">Session state is ideal for storing user data that's specific to a particular session but where the data doesn't require permanent storage across sessions.</span></span>
* <span data-ttu-id="7c57b-162">Odstranit data relace buď pokud [ISession.Clear](/dotnet/api/microsoft.aspnetcore.http.isession.clear) volána implementace nebo vypršení platnosti relace.</span><span class="sxs-lookup"><span data-stu-id="7c57b-162">Session data is deleted either when the [ISession.Clear](/dotnet/api/microsoft.aspnetcore.http.isession.clear) implementation is called or when the session expires.</span></span>
* <span data-ttu-id="7c57b-163">Neexistuje žádný výchozí mechanismus k informování kód aplikace, že bylo ukončeno prohlížeče klienta nebo když souboru cookie relace se odstraní nebo vypršení platnosti na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="7c57b-163">There's no default mechanism to inform app code that a client browser has been closed or when the session cookie is deleted or expired on the client.</span></span>
<span data-ttu-id="7c57b-164">Šablony ASP.NET Core MVC a Razor stránky zahrnují podporu pro [obecného Regulation (GDPR) podporu](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="7c57b-164">The ASP.NET Core MVC and Razor pages templates include support for [General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span> <span data-ttu-id="7c57b-165">[Soubory cookie pro stav relace nejsou nezbytné](xref:security/gdpr#tempdata-provider-and-session-state-cookies-are-not-essential), stav relace není funkční, při sledování je vypnuté.</span><span class="sxs-lookup"><span data-stu-id="7c57b-165">[Session state cookies aren't essential](xref:security/gdpr#tempdata-provider-and-session-state-cookies-are-not-essential), session state isn't functional when tracking is disabled.</span></span>

> [!WARNING]
> <span data-ttu-id="7c57b-166">Citlivá data neukládejte do stavu relace.</span><span class="sxs-lookup"><span data-stu-id="7c57b-166">Don't store sensitive data in session state.</span></span> <span data-ttu-id="7c57b-167">Uživatel nemusí zavřete prohlížeč a zrušte souboru cookie relace.</span><span class="sxs-lookup"><span data-stu-id="7c57b-167">The user might not close the browser and clear the session cookie.</span></span> <span data-ttu-id="7c57b-168">Některé prohlížeče udržovat platné soubory cookie v prohlížeči windows.</span><span class="sxs-lookup"><span data-stu-id="7c57b-168">Some browsers maintain valid session cookies across browser windows.</span></span> <span data-ttu-id="7c57b-169">Relace nemusí být omezeny na jednoho uživatele&mdash;dalšího uživatele může nadále procházet aplikace pomocí stejného souboru cookie relace.</span><span class="sxs-lookup"><span data-stu-id="7c57b-169">A session might not be restricted to a single user&mdash;the next user might continue to browse the app with the same session cookie.</span></span>

<span data-ttu-id="7c57b-170">Poskytovatel mezipaměti v paměti ukládá data relace v paměti na server, ve kterém se aplikace nachází.</span><span class="sxs-lookup"><span data-stu-id="7c57b-170">The in-memory cache provider stores session data in the memory of the server where the app resides.</span></span> <span data-ttu-id="7c57b-171">Ve scénáři farmy serveru:</span><span class="sxs-lookup"><span data-stu-id="7c57b-171">In a server farm scenario:</span></span>

* <span data-ttu-id="7c57b-172">Použití *rychlé relace* a jejich zapojení každá relace pro konkrétní aplikaci instance na jednotlivých serverech.</span><span class="sxs-lookup"><span data-stu-id="7c57b-172">Use *sticky sessions* to tie each session to a specific app instance on an individual server.</span></span> <span data-ttu-id="7c57b-173">[Azure App Service](https://azure.microsoft.com/services/app-service/) používá [požádat o směrování žádostí na aplikace](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) vynutit rychlé relace ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="7c57b-173">[Azure App Service](https://azure.microsoft.com/services/app-service/) uses [Application Request Routing (ARR)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) to enforce sticky sessions by default.</span></span> <span data-ttu-id="7c57b-174">Rychlé relace však může ovlivnit škálovatelnost a zkomplikovat aktualizace webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="7c57b-174">However, sticky sessions can affect scalability and complicate web app updates.</span></span> <span data-ttu-id="7c57b-175">Lepším řešením je použití Redis nebo SQL Server distribuovaná mezipaměť, která nevyžaduje rychlé relace.</span><span class="sxs-lookup"><span data-stu-id="7c57b-175">A better approach is to use a Redis or SQL Server distributed cache, which doesn't require sticky sessions.</span></span> <span data-ttu-id="7c57b-176">Další informace naleznete v tématu <xref:performance/caching/distributed>.</span><span class="sxs-lookup"><span data-stu-id="7c57b-176">For more information, see <xref:performance/caching/distributed>.</span></span>
* <span data-ttu-id="7c57b-177">Soubor cookie relace se šifruje pomocí [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector).</span><span class="sxs-lookup"><span data-stu-id="7c57b-177">The session cookie is encrypted via [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector).</span></span> <span data-ttu-id="7c57b-178">Ochrana dat musejí být správně nakonfigurovány ke čtení souborů cookie relací na každém počítači.</span><span class="sxs-lookup"><span data-stu-id="7c57b-178">Data Protection must be properly configured to read session cookies on each machine.</span></span> <span data-ttu-id="7c57b-179">Další informace najdete v tématu <xref:security/data-protection/introduction> a [zprostředkovateli úložiště klíčů](xref:security/data-protection/implementation/key-storage-providers).</span><span class="sxs-lookup"><span data-stu-id="7c57b-179">For more information, see <xref:security/data-protection/introduction> and [Key storage providers](xref:security/data-protection/implementation/key-storage-providers).</span></span>

### <a name="configure-session-state"></a><span data-ttu-id="7c57b-180">Nakonfigurovat stav relace</span><span class="sxs-lookup"><span data-stu-id="7c57b-180">Configure session state</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="7c57b-181">[Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) balíček, který je součástí [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app), poskytuje middleware pro správu stavu relace.</span><span class="sxs-lookup"><span data-stu-id="7c57b-181">The [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), provides middleware for managing session state.</span></span> <span data-ttu-id="7c57b-182">Povolit relace middleware `Startup` musí obsahovat:</span><span class="sxs-lookup"><span data-stu-id="7c57b-182">To enable the session middleware, `Startup` must contain:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="7c57b-183">[Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) balíček poskytuje middleware pro správu stavu relace.</span><span class="sxs-lookup"><span data-stu-id="7c57b-183">The [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) package provides middleware for managing session state.</span></span> <span data-ttu-id="7c57b-184">Povolit relace middleware `Startup` musí obsahovat:</span><span class="sxs-lookup"><span data-stu-id="7c57b-184">To enable the session middleware, `Startup` must contain:</span></span>

::: moniker-end

* <span data-ttu-id="7c57b-185">Některé z [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache) mezipaměti paměti.</span><span class="sxs-lookup"><span data-stu-id="7c57b-185">Any of the [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache) memory caches.</span></span> <span data-ttu-id="7c57b-186">`IDistributedCache` Implementace se používá jako záložní úložiště pro relaci.</span><span class="sxs-lookup"><span data-stu-id="7c57b-186">The `IDistributedCache` implementation is used as a backing store for session.</span></span> <span data-ttu-id="7c57b-187">Další informace naleznete v tématu <xref:performance/caching/distributed>.</span><span class="sxs-lookup"><span data-stu-id="7c57b-187">For more information, see <xref:performance/caching/distributed>.</span></span>
* <span data-ttu-id="7c57b-188">Volání [AddSession](/dotnet/api/microsoft.extensions.dependencyinjection.sessionservicecollectionextensions.addsession) v `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7c57b-188">A call to [AddSession](/dotnet/api/microsoft.extensions.dependencyinjection.sessionservicecollectionextensions.addsession) in `ConfigureServices`.</span></span>
* <span data-ttu-id="7c57b-189">Volání [UseSession](/dotnet/api/microsoft.aspnetcore.builder.sessionmiddlewareextensions#methods_) v `Configure`.</span><span class="sxs-lookup"><span data-stu-id="7c57b-189">A call to [UseSession](/dotnet/api/microsoft.aspnetcore.builder.sessionmiddlewareextensions#methods_) in `Configure`.</span></span>

<span data-ttu-id="7c57b-190">Následující kód ukazuje, jak nastavit Zprostředkovatel relací v paměti s výchozí implementace v paměti `IDistributedCache`:</span><span class="sxs-lookup"><span data-stu-id="7c57b-190">The following code shows how to set up the in-memory session provider with a default in-memory implementation of `IDistributedCache`:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-state/samples/2.x/SessionSample/Startup.cs?name=snippet1&highlight=11,13-18,39)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](app-state/samples/1.x/SessionSample/Startup.cs?name=snippet1&highlight=5,7-12,19)]

::: moniker-end

<span data-ttu-id="7c57b-191">Je důležité pořadí middlewaru.</span><span class="sxs-lookup"><span data-stu-id="7c57b-191">The order of middleware is important.</span></span> <span data-ttu-id="7c57b-192">V předchozím příkladu `InvalidOperationException` dojde k výjimce při `UseSession` je volána poté, co `UseMvc`.</span><span class="sxs-lookup"><span data-stu-id="7c57b-192">In the preceding example, an `InvalidOperationException` exception occurs when `UseSession` is invoked after `UseMvc`.</span></span> <span data-ttu-id="7c57b-193">Další informace najdete v tématu [Middleware řazení](xref:fundamentals/middleware/index#order).</span><span class="sxs-lookup"><span data-stu-id="7c57b-193">For more information, see [Middleware Ordering](xref:fundamentals/middleware/index#order).</span></span>

<span data-ttu-id="7c57b-194">[HttpContext.Session](/dotnet/api/microsoft.aspnetcore.http.httpcontext.session) je k dispozici po dokončení konfigurace stavu relace.</span><span class="sxs-lookup"><span data-stu-id="7c57b-194">[HttpContext.Session](/dotnet/api/microsoft.aspnetcore.http.httpcontext.session) is available after session state is configured.</span></span>

<span data-ttu-id="7c57b-195">`HttpContext.Session` Nelze získat přístup před `UseSession` byla volána.</span><span class="sxs-lookup"><span data-stu-id="7c57b-195">`HttpContext.Session` can't be accessed before `UseSession` has been called.</span></span>

<span data-ttu-id="7c57b-196">Po aplikaci byl zahájen zápis do datového proudu odpovědí nelze vytvořit novou relaci s nového souboru cookie relace.</span><span class="sxs-lookup"><span data-stu-id="7c57b-196">A new session with a new session cookie can't be created after the app has begun writing to the response stream.</span></span> <span data-ttu-id="7c57b-197">Výjimky je zaznamenána do protokolu webového serveru a nezobrazuje se v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="7c57b-197">The exception is recorded in the web server log and not displayed in the browser.</span></span>

### <a name="load-session-state-asynchronously"></a><span data-ttu-id="7c57b-198">Načíst stav relace asynchronně</span><span class="sxs-lookup"><span data-stu-id="7c57b-198">Load session state asynchronously</span></span>

<span data-ttu-id="7c57b-199">Výchozí zprostředkovatel relací v ASP.NET Core načte záznamy relace ze základního [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache) asynchronně pouze tehdy, pokud záložní úložiště [ISession.LoadAsync](/dotnet/api/microsoft.aspnetcore.http.isession.loadasync) explicitně volána metoda před [TryGetValue](/dotnet/api/microsoft.aspnetcore.http.isession.trygetvalue), [nastavit](/dotnet/api/microsoft.aspnetcore.http.isession.set), nebo [odebrat](/dotnet/api/microsoft.aspnetcore.http.isession.remove) metody.</span><span class="sxs-lookup"><span data-stu-id="7c57b-199">The default session provider in ASP.NET Core loads session records from the underlying [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache) backing store asynchronously only if the [ISession.LoadAsync](/dotnet/api/microsoft.aspnetcore.http.isession.loadasync) method is explicitly called before the [TryGetValue](/dotnet/api/microsoft.aspnetcore.http.isession.trygetvalue), [Set](/dotnet/api/microsoft.aspnetcore.http.isession.set), or [Remove](/dotnet/api/microsoft.aspnetcore.http.isession.remove) methods.</span></span> <span data-ttu-id="7c57b-200">Pokud `LoadAsync` není volána nejprve základní záznam relace načtení synchronně, což může způsobit snížení výkonu ve velkém měřítku.</span><span class="sxs-lookup"><span data-stu-id="7c57b-200">If `LoadAsync` isn't called first, the underlying session record is loaded synchronously, which can incur a performance penalty at scale.</span></span>

<span data-ttu-id="7c57b-201">Pokud chcete, aby aplikace vynucení tohoto modelu, zabalit [DistributedSessionStore](/dotnet/api/microsoft.aspnetcore.session.distributedsessionstore) a [DistributedSession](/dotnet/api/microsoft.aspnetcore.session.distributedsession) implementace s verzemi, které vyvolá výjimku, pokud se `LoadAsync` metoda není volána před provedením `TryGetValue`, `Set`, nebo `Remove`.</span><span class="sxs-lookup"><span data-stu-id="7c57b-201">To have apps enforce this pattern, wrap the [DistributedSessionStore](/dotnet/api/microsoft.aspnetcore.session.distributedsessionstore) and [DistributedSession](/dotnet/api/microsoft.aspnetcore.session.distributedsession) implementations with versions that throw an exception if the `LoadAsync` method isn't called before `TryGetValue`, `Set`, or `Remove`.</span></span> <span data-ttu-id="7c57b-202">Zaregistrujte zabalené verze v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="7c57b-202">Register the wrapped versions in the services container.</span></span>

### <a name="session-options"></a><span data-ttu-id="7c57b-203">Možnosti relace</span><span class="sxs-lookup"><span data-stu-id="7c57b-203">Session options</span></span>

<span data-ttu-id="7c57b-204">Chcete-li přepsat výchozí relace, použijte [SessionOptions](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions).</span><span class="sxs-lookup"><span data-stu-id="7c57b-204">To override session defaults, use [SessionOptions](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions).</span></span>

::: moniker range=">= aspnetcore-2.0"

| <span data-ttu-id="7c57b-205">Možnost</span><span class="sxs-lookup"><span data-stu-id="7c57b-205">Option</span></span> | <span data-ttu-id="7c57b-206">Popis</span><span class="sxs-lookup"><span data-stu-id="7c57b-206">Description</span></span> |
| ------ | ----------- |
| [<span data-ttu-id="7c57b-207">Soubor cookie</span><span class="sxs-lookup"><span data-stu-id="7c57b-207">Cookie</span></span>](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.cookie) | <span data-ttu-id="7c57b-208">Určuje nastavení použité k vytvoření souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="7c57b-208">Determines the settings used to create the cookie.</span></span> <span data-ttu-id="7c57b-209">[Název](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.name) výchozí hodnota je [SessionDefaults.CookieName](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiename) (`.AspNetCore.Session`).</span><span class="sxs-lookup"><span data-stu-id="7c57b-209">[Name](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.name) defaults to [SessionDefaults.CookieName](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiename) (`.AspNetCore.Session`).</span></span> <span data-ttu-id="7c57b-210">[Cesta](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.path) výchozí hodnota je [SessionDefaults.CookiePath](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiepath) (`/`).</span><span class="sxs-lookup"><span data-stu-id="7c57b-210">[Path](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.path) defaults to [SessionDefaults.CookiePath](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiepath) (`/`).</span></span> <span data-ttu-id="7c57b-211">[SameSite](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.samesite) výchozí hodnota je [SameSiteMode.Lax](/dotnet/api/microsoft.aspnetcore.http.samesitemode) (`1`).</span><span class="sxs-lookup"><span data-stu-id="7c57b-211">[SameSite](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.samesite) defaults to [SameSiteMode.Lax](/dotnet/api/microsoft.aspnetcore.http.samesitemode) (`1`).</span></span> <span data-ttu-id="7c57b-212">[HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) výchozí hodnota je `true`.</span><span class="sxs-lookup"><span data-stu-id="7c57b-212">[HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) defaults to `true`.</span></span> <span data-ttu-id="7c57b-213">[IsEssential](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.isessential) výchozí hodnota je `false`.</span><span class="sxs-lookup"><span data-stu-id="7c57b-213">[IsEssential](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.isessential) defaults to `false`.</span></span> |
| [<span data-ttu-id="7c57b-214">IdleTimeout</span><span class="sxs-lookup"><span data-stu-id="7c57b-214">IdleTimeout</span></span>](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) | <span data-ttu-id="7c57b-215">`IdleTimeout` Určuje, jak dlouho může být relace nečinná předtím, než jsou opuštěných jeho obsah.</span><span class="sxs-lookup"><span data-stu-id="7c57b-215">The `IdleTimeout` indicates how long the session can be idle before its contents are abandoned.</span></span> <span data-ttu-id="7c57b-216">Každá relace přístup obnoví časový limit.</span><span class="sxs-lookup"><span data-stu-id="7c57b-216">Each session access resets the timeout.</span></span> <span data-ttu-id="7c57b-217">Toto nastavení platí jenom pro obsah relace, není soubor cookie.</span><span class="sxs-lookup"><span data-stu-id="7c57b-217">This setting only applies to the content of the session, not the cookie.</span></span> <span data-ttu-id="7c57b-218">Výchozí hodnota je 20 minut.</span><span class="sxs-lookup"><span data-stu-id="7c57b-218">The default is 20 minutes.</span></span> |
| [<span data-ttu-id="7c57b-219">IOTimeout</span><span class="sxs-lookup"><span data-stu-id="7c57b-219">IOTimeout</span></span>](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.iotimeout) | <span data-ttu-id="7c57b-220">Maximální množství času povoleny relace načtení z úložiště nebo k potvrzení zpět do úložiště.</span><span class="sxs-lookup"><span data-stu-id="7c57b-220">The maximim amount of time allowed to load a session from the store or to commit it back to the store.</span></span> <span data-ttu-id="7c57b-221">Toto nastavení může platí jenom pro asynchronní operace.</span><span class="sxs-lookup"><span data-stu-id="7c57b-221">This setting may only apply to asynchronous operations.</span></span> <span data-ttu-id="7c57b-222">Tento časový limit se dají zakázat pomocí [InfiniteTimeSpan](/dotnet/api/system.threading.timeout.infinitetimespan).</span><span class="sxs-lookup"><span data-stu-id="7c57b-222">This timeout can be disabled using [InfiniteTimeSpan](/dotnet/api/system.threading.timeout.infinitetimespan).</span></span> <span data-ttu-id="7c57b-223">Výchozí hodnota je 1 minuta.</span><span class="sxs-lookup"><span data-stu-id="7c57b-223">The default is 1 minute.</span></span> |

<span data-ttu-id="7c57b-224">Relace do souboru cookie používá ke sledování a identifikace požadavků z jediného prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="7c57b-224">Session uses a cookie to track and identify requests from a single browser.</span></span> <span data-ttu-id="7c57b-225">Ve výchozím nastavení, je tento soubor cookie s názvem `.AspNetCore.Session`, a používá cestu k `/`.</span><span class="sxs-lookup"><span data-stu-id="7c57b-225">By default, this cookie is named `.AspNetCore.Session`, and it uses a path of `/`.</span></span> <span data-ttu-id="7c57b-226">Protože výchozí soubor cookie nemá určenou doménu, to nebude zpřístupněn pro skript na straně klienta na stránce (protože [HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) výchozí hodnota je `true`).</span><span class="sxs-lookup"><span data-stu-id="7c57b-226">Because the cookie default doesn't specify a domain, it isn't made available to the client-side script on the page (because [HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) defaults to `true`).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

| <span data-ttu-id="7c57b-227">Možnost</span><span class="sxs-lookup"><span data-stu-id="7c57b-227">Option</span></span> | <span data-ttu-id="7c57b-228">Popis</span><span class="sxs-lookup"><span data-stu-id="7c57b-228">Description</span></span> |
| ------ | ----------- |
| [<span data-ttu-id="7c57b-229">CookieDomain</span><span class="sxs-lookup"><span data-stu-id="7c57b-229">CookieDomain</span></span>](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.cookiedomain) | <span data-ttu-id="7c57b-230">Určuje doménu použitou k vytvoření souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="7c57b-230">Determines the domain used to create the cookie.</span></span> <span data-ttu-id="7c57b-231">`CookieDomain` není ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="7c57b-231">`CookieDomain` isn't set by default.</span></span> |
| [<span data-ttu-id="7c57b-232">CookieHttpOnly</span><span class="sxs-lookup"><span data-stu-id="7c57b-232">CookieHttpOnly</span></span>](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.cookiehttponly) | <span data-ttu-id="7c57b-233">Určuje, pokud prohlížeč by měl povolit soubory cookie k přístupná pomocí jazyka JavaScript na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="7c57b-233">Determines if the browser should allow the cookie to be accessed by client-side JavaScript.</span></span> <span data-ttu-id="7c57b-234">Výchozí hodnota je `true`, což znamená, že soubor cookie je pouze předáván požadavkům HTTP a nebude zpřístupněn pro skript na stránce.</span><span class="sxs-lookup"><span data-stu-id="7c57b-234">The default is `true`, which means the cookie is only passed to HTTP requests and isn't made available to script on the page.</span></span> |
| [<span data-ttu-id="7c57b-235">CookieName</span><span class="sxs-lookup"><span data-stu-id="7c57b-235">CookieName</span></span>](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.cookiename) | <span data-ttu-id="7c57b-236">Určuje název souboru cookie použité k uchování ID relace.</span><span class="sxs-lookup"><span data-stu-id="7c57b-236">Determines the cookie name used to persist the session ID.</span></span> <span data-ttu-id="7c57b-237">Výchozí hodnota je [SessionDefaults.CookieName](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiename) (`.AspNetCore.Session`).</span><span class="sxs-lookup"><span data-stu-id="7c57b-237">The default is [SessionDefaults.CookieName](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiename) (`.AspNetCore.Session`).</span></span> |
| [<span data-ttu-id="7c57b-238">CookiePath</span><span class="sxs-lookup"><span data-stu-id="7c57b-238">CookiePath</span></span>](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.cookiepath) | <span data-ttu-id="7c57b-239">Určuje cestu použitou k vytvoření souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="7c57b-239">Determines the path used to create the cookie.</span></span> <span data-ttu-id="7c57b-240">Výchozí hodnota je [SessionDefaults.CookiePath](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiepath) (`/`).</span><span class="sxs-lookup"><span data-stu-id="7c57b-240">Defaults to [SessionDefaults.CookiePath](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiepath) (`/`).</span></span> |
| [<span data-ttu-id="7c57b-241">CookieSecure</span><span class="sxs-lookup"><span data-stu-id="7c57b-241">CookieSecure</span></span>](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.cookiesecure) | <span data-ttu-id="7c57b-242">Určuje, pokud by měl soubor cookie přenášet pouze na požadavky HTTPS.</span><span class="sxs-lookup"><span data-stu-id="7c57b-242">Determines if the cookie should only be transmitted on HTTPS requests.</span></span> <span data-ttu-id="7c57b-243">Výchozí hodnota je [CookieSecurePolicy.None](/dotnet/api/microsoft.aspnetcore.http.cookiesecurepolicy) (`2`).</span><span class="sxs-lookup"><span data-stu-id="7c57b-243">The default is [CookieSecurePolicy.None](/dotnet/api/microsoft.aspnetcore.http.cookiesecurepolicy) (`2`).</span></span> |
| [<span data-ttu-id="7c57b-244">IdleTimeout</span><span class="sxs-lookup"><span data-stu-id="7c57b-244">IdleTimeout</span></span>](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) | <span data-ttu-id="7c57b-245">`IdleTimeout` Určuje, jak dlouho může být relace nečinná předtím, než jsou opuštěných jeho obsah.</span><span class="sxs-lookup"><span data-stu-id="7c57b-245">The `IdleTimeout` indicates how long the session can be idle before its contents are abandoned.</span></span> <span data-ttu-id="7c57b-246">Každá relace přístup obnoví časový limit.</span><span class="sxs-lookup"><span data-stu-id="7c57b-246">Each session access resets the timeout.</span></span> <span data-ttu-id="7c57b-247">Všimněte si, že toto platí jenom pro obsah relace, není soubor cookie.</span><span class="sxs-lookup"><span data-stu-id="7c57b-247">Note this only applies to the content of the session, not the cookie.</span></span> <span data-ttu-id="7c57b-248">Výchozí hodnota je 20 minut.</span><span class="sxs-lookup"><span data-stu-id="7c57b-248">The default is 20 minutes.</span></span> |

<span data-ttu-id="7c57b-249">Relace do souboru cookie používá ke sledování a identifikace požadavků z jediného prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="7c57b-249">Session uses a cookie to track and identify requests from a single browser.</span></span> <span data-ttu-id="7c57b-250">Ve výchozím nastavení, je tento soubor cookie s názvem `.AspNet.Session`, a používá cestu k `/`.</span><span class="sxs-lookup"><span data-stu-id="7c57b-250">By default, this cookie is named `.AspNet.Session`, and it uses a path of `/`.</span></span>

::: moniker-end

<span data-ttu-id="7c57b-251">Chcete-li přepsat výchozí hodnoty souboru cookie relace, použijte `SessionOptions`:</span><span class="sxs-lookup"><span data-stu-id="7c57b-251">To override cookie session defaults, use `SessionOptions`:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-state/samples_snapshot/2.x/SessionSample/Startup.cs?name=snippet1&highlight=13-18)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](app-state/samples_snapshot/1.x/SessionSample/Startup.cs?name=snippet1&highlight=5-9)]

::: moniker-end

<span data-ttu-id="7c57b-252">Tato aplikace používá [IdleTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) a určí, jak dlouho může být relace nečinná předtím, než jsou opuštěných jeho obsah v mezipaměti serveru.</span><span class="sxs-lookup"><span data-stu-id="7c57b-252">The app uses the [IdleTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) property to determine how long a session can be idle before its contents in the server's cache are abandoned.</span></span> <span data-ttu-id="7c57b-253">Tato vlastnost je nezávislá na vypršení platnosti souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="7c57b-253">This property is independent of the cookie expiration.</span></span> <span data-ttu-id="7c57b-254">Každý požadavek, který prochází [relace Middleware](/dotnet/api/microsoft.aspnetcore.session.sessionmiddleware) obnoví časový limit.</span><span class="sxs-lookup"><span data-stu-id="7c57b-254">Each request that passes through the [Session Middleware](/dotnet/api/microsoft.aspnetcore.session.sessionmiddleware) resets the timeout.</span></span>

<span data-ttu-id="7c57b-255">Stav relace je *nezamykací*.</span><span class="sxs-lookup"><span data-stu-id="7c57b-255">Session state is *non-locking*.</span></span> <span data-ttu-id="7c57b-256">Když dva požadavky současně pokusí změnit obsah relace, poslední žádosti přepíše první.</span><span class="sxs-lookup"><span data-stu-id="7c57b-256">If two requests simultaneously attempt to modify the contents of a session, the last request overrides the first.</span></span> <span data-ttu-id="7c57b-257">`Session` je implementován jako *přeměnit relace*, což znamená, že veškerý obsah se ukládají společně.</span><span class="sxs-lookup"><span data-stu-id="7c57b-257">`Session` is implemented as a *coherent session*, which means that all the contents are stored together.</span></span> <span data-ttu-id="7c57b-258">Když dva požadavky se snaží změnit hodnoty jinou relací, poslední žádosti může přepsat relace změny provedené v prvním.</span><span class="sxs-lookup"><span data-stu-id="7c57b-258">When two requests seek to modify different session values, the last request may override session changes made by the first.</span></span>

### <a name="set-and-get-session-values"></a><span data-ttu-id="7c57b-259">Nastavení a získání hodnoty relace</span><span class="sxs-lookup"><span data-stu-id="7c57b-259">Set and get Session values</span></span>

<span data-ttu-id="7c57b-260">Stav relace se přistupuje ze stránek Razor [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) třídy nebo MVC [řadič](/dotnet/api/microsoft.aspnetcore.mvc.controller) třídy s [HttpContext.Session](/dotnet/api/microsoft.aspnetcore.http.httpcontext.session).</span><span class="sxs-lookup"><span data-stu-id="7c57b-260">Session state is accessed from a Razor Pages [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) class or MVC [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller) class with [HttpContext.Session](/dotnet/api/microsoft.aspnetcore.http.httpcontext.session).</span></span> <span data-ttu-id="7c57b-261">Tato vlastnost je [ISession](/dotnet/api/microsoft.aspnetcore.http.isession) implementace.</span><span class="sxs-lookup"><span data-stu-id="7c57b-261">This property is an [ISession](/dotnet/api/microsoft.aspnetcore.http.isession) implementation.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="7c57b-262">`ISession` Implementace poskytuje několik metod rozšíření pro nastavení a načtení celé číslo a řetězec hodnoty.</span><span class="sxs-lookup"><span data-stu-id="7c57b-262">The `ISession` implementation provides several extension methods to set and retrieve integer and string values.</span></span> <span data-ttu-id="7c57b-263">Rozšiřující metody jsou v [Microsoft.AspNetCore.Http](/dotnet/api/microsoft.aspnetcore.http) obor názvů (přidání `using Microsoft.AspNetCore.Http;` prohlášení k získání přístupu k rozšiřující metody) při [Microsoft.AspNetCore.Http.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions/) balíček se odkazuje v projektu.</span><span class="sxs-lookup"><span data-stu-id="7c57b-263">The extension methods are in the [Microsoft.AspNetCore.Http](/dotnet/api/microsoft.aspnetcore.http) namespace (add a `using Microsoft.AspNetCore.Http;` statement to gain access to the extension methods) when the [Microsoft.AspNetCore.Http.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions/) package is referenced by the project.</span></span> <span data-ttu-id="7c57b-264">Oba balíčky jsou součástí [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="7c57b-264">Both packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="7c57b-265">`ISession` Implementace poskytuje několik metod rozšíření pro nastavení a načtení celé číslo a řetězec hodnoty.</span><span class="sxs-lookup"><span data-stu-id="7c57b-265">The `ISession` implementation provides several extension methods to set and retreive integer and string values.</span></span> <span data-ttu-id="7c57b-266">Rozšiřující metody jsou v [Microsoft.AspNetCore.Http](/dotnet/api/microsoft.aspnetcore.http) obor názvů (přidání `using Microsoft.AspNetCore.Http;` prohlášení k získání přístupu k rozšiřující metody) při [Microsoft.AspNetCore.Http.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions/) balíček se odkazuje v projektu.</span><span class="sxs-lookup"><span data-stu-id="7c57b-266">The extension methods are in the [Microsoft.AspNetCore.Http](/dotnet/api/microsoft.aspnetcore.http) namespace (add a `using Microsoft.AspNetCore.Http;` statement to gain access to the extension methods) when the [Microsoft.AspNetCore.Http.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions/) package is referenced by the project.</span></span>

::: moniker-end

<span data-ttu-id="7c57b-267">`ISession` rozšiřující metody:</span><span class="sxs-lookup"><span data-stu-id="7c57b-267">`ISession` extension methods:</span></span>

* [<span data-ttu-id="7c57b-268">Get (ISession, String)</span><span class="sxs-lookup"><span data-stu-id="7c57b-268">Get(ISession, String)</span></span>](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.get)
* [<span data-ttu-id="7c57b-269">GetInt32(ISession, String)</span><span class="sxs-lookup"><span data-stu-id="7c57b-269">GetInt32(ISession, String)</span></span>](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.getint32)
* [<span data-ttu-id="7c57b-270">GetString (ISession, String)</span><span class="sxs-lookup"><span data-stu-id="7c57b-270">GetString(ISession, String)</span></span>](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.getstring)
* [<span data-ttu-id="7c57b-271">Setint32 – (ISession, řetězec, Int32)</span><span class="sxs-lookup"><span data-stu-id="7c57b-271">SetInt32(ISession, String, Int32)</span></span>](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.setint32)
* [<span data-ttu-id="7c57b-272">SetString – (ISession, String, String)</span><span class="sxs-lookup"><span data-stu-id="7c57b-272">SetString(ISession, String, String)</span></span>](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.setstring)

<span data-ttu-id="7c57b-273">Následující příklad načte hodnota relace `IndexModel.SessionKeyName` klíč (`_Name` v ukázkové aplikaci) na stránce pro stránky Razor:</span><span class="sxs-lookup"><span data-stu-id="7c57b-273">The following example retrieves the session value for the `IndexModel.SessionKeyName` key (`_Name` in the sample app) in a Razor Pages page:</span></span>

```csharp
@page
@using Microsoft.AspNetCore.Http
@model IndexModel

...

Name: @HttpContext.Session.GetString(IndexModel.SessionKeyName)
```

<span data-ttu-id="7c57b-274">Následující příklad ukazuje, jak nastavit a získat celé číslo a řetězec:</span><span class="sxs-lookup"><span data-stu-id="7c57b-274">The following example shows how to set and get an integer and a string:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=18-19,22-23)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](app-state/samples/1.x/SessionSample/Controllers/HomeController.cs?name=snippet1&highlight=10-11,18-19)]

::: moniker-end

<span data-ttu-id="7c57b-275">Všechna data relace se musí serializovat povolit scénáře distribuované mezipaměti, i když se používá mezipaměť v paměti.</span><span class="sxs-lookup"><span data-stu-id="7c57b-275">All session data must be serialized to enable a distributed cache scenario, even when using the in-memory cache.</span></span> <span data-ttu-id="7c57b-276">Jsou k dispozici minimální řetězcové a číselné serializátory (podívejte se, metody a metody rozšíření [ISession](/dotnet/api/microsoft.aspnetcore.http.isession)).</span><span class="sxs-lookup"><span data-stu-id="7c57b-276">Minimal string and number serializers are provided (see the methods and extension methods of [ISession](/dotnet/api/microsoft.aspnetcore.http.isession)).</span></span> <span data-ttu-id="7c57b-277">Komplexní typy se musí serializovat uživatelem pomocí jiného mechanismu, jako je JSON.</span><span class="sxs-lookup"><span data-stu-id="7c57b-277">Complex types must be serialized by the user using another mechanism, such as JSON.</span></span>

<span data-ttu-id="7c57b-278">Přidejte následující metody rozšíření pro nastavení a získání serializovatelné objekty:</span><span class="sxs-lookup"><span data-stu-id="7c57b-278">Add the following extension methods to set and get serializable objects:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-state/samples/2.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](app-state/samples/1.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="7c57b-279">Následující příklad ukazuje, jak nastavit a získat serializovatelný objekt s metodami rozšíření:</span><span class="sxs-lookup"><span data-stu-id="7c57b-279">The following example shows how to set and get a serializable object with the extension methods:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](app-state/samples/1.x/SessionSample/Controllers/HomeController.cs?name=snippet2&highlight=4,12)]

::: moniker-end

## <a name="tempdata"></a><span data-ttu-id="7c57b-280">TempData</span><span class="sxs-lookup"><span data-stu-id="7c57b-280">TempData</span></span>

<span data-ttu-id="7c57b-281">Zpřístupňuje ASP.NET Core [TempData vlastnost modelu stránky Razor Pages](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.tempdata) nebo [TempData kontroler MVC](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata).</span><span class="sxs-lookup"><span data-stu-id="7c57b-281">ASP.NET Core exposes the [TempData property of a Razor Pages page model](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.tempdata) or [TempData of an MVC controller](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata).</span></span> <span data-ttu-id="7c57b-282">Tato vlastnost ukládá data, dokud je pro čtení.</span><span class="sxs-lookup"><span data-stu-id="7c57b-282">This property stores data until it's read.</span></span> <span data-ttu-id="7c57b-283">[Zachovat](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.itempdatadictionary.keep) a [Náhled](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.itempdatadictionary.peek) metody můžete použít k prozkoumání dat bez potřeby jejich odstranění.</span><span class="sxs-lookup"><span data-stu-id="7c57b-283">The [Keep](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.itempdatadictionary.keep) and [Peek](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.itempdatadictionary.peek) methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="7c57b-284">TempData je zvláště užitečná pro přesměrování, pokud jsou data požadovaná pro více než jeden požadavek.</span><span class="sxs-lookup"><span data-stu-id="7c57b-284">TempData is particularly useful for redirection when data is required for more than a single request.</span></span> <span data-ttu-id="7c57b-285">TempData implementují TempData zprostředkovatelů pomocí souborů cookie nebo stav relace.</span><span class="sxs-lookup"><span data-stu-id="7c57b-285">TempData is implemented by TempData providers using either cookies or session state.</span></span>

### <a name="tempdata-providers"></a><span data-ttu-id="7c57b-286">Poskytovatelé TempData</span><span class="sxs-lookup"><span data-stu-id="7c57b-286">TempData providers</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="7c57b-287">V technologii ASP.NET Core 2.0 nebo novější TempData zprostředkovatele na základě souboru cookie se používá ve výchozím nastavení k uložení TempData v souborech cookie.</span><span class="sxs-lookup"><span data-stu-id="7c57b-287">In ASP.NET Core 2.0 or later, the cookie-based TempData provider is used by default to store TempData in cookies.</span></span>

<span data-ttu-id="7c57b-288">Soubor cookie data se šifrují pomocí [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector)kódovanou s [Base64UrlTextEncoder](/dotnet/api/microsoft.aspnetcore.webutilities.base64urltextencoder), pak rozdělený do bloků dat.</span><span class="sxs-lookup"><span data-stu-id="7c57b-288">The cookie data is encrypted using [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector), encoded with [Base64UrlTextEncoder](/dotnet/api/microsoft.aspnetcore.webutilities.base64urltextencoder), then chunked.</span></span> <span data-ttu-id="7c57b-289">Protože souboru cookie, který je rozdělený do bloků dat, velikost jednoho souboru cookie v ASP.NET Core 1.x neplatí omezení.</span><span class="sxs-lookup"><span data-stu-id="7c57b-289">Because the cookie is chunked, the single cookie size limit found in ASP.NET Core 1.x doesn't apply.</span></span> <span data-ttu-id="7c57b-290">Data souborů cookie není komprimována, protože komprese šifrovaná data může vést k problémům zabezpečení, jako [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) a [porušení](https://wikipedia.org/wiki/BREACH_(security_exploit)) útoky.</span><span class="sxs-lookup"><span data-stu-id="7c57b-290">The cookie data isn't compressed because compressing encrypted data can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span> <span data-ttu-id="7c57b-291">Další informace o poskytovateli TempData založené na souborech cookie najdete v tématu [CookieTempDataProvider](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.cookietempdataprovider).</span><span class="sxs-lookup"><span data-stu-id="7c57b-291">For more information on the cookie-based TempData provider, see [CookieTempDataProvider](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.cookietempdataprovider).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="7c57b-292">Zprostředkovatel stavu relací TempData v ASP.NET Core 1.0 a 1.1, je výchozím zprostředkovatelem.</span><span class="sxs-lookup"><span data-stu-id="7c57b-292">In ASP.NET Core 1.0 and 1.1, the session state TempData provider is the default provider.</span></span>

::: moniker-end

### <a name="choose-a-tempdata-provider"></a><span data-ttu-id="7c57b-293">Zvolte poskytovatele TempData</span><span class="sxs-lookup"><span data-stu-id="7c57b-293">Choose a TempData provider</span></span>

<span data-ttu-id="7c57b-294">Výběru poskytovatele TempData zahrnuje třeba mít na paměti, jako například:</span><span class="sxs-lookup"><span data-stu-id="7c57b-294">Choosing a TempData provider involves several considerations, such as:</span></span>

1. <span data-ttu-id="7c57b-295">Aplikace již používá stav relace?</span><span class="sxs-lookup"><span data-stu-id="7c57b-295">Does the app already use session state?</span></span> <span data-ttu-id="7c57b-296">Pokud ano, použití TempData poskytovatele stavu relace má bez dalších nákladů na aplikaci (kromě množství dat).</span><span class="sxs-lookup"><span data-stu-id="7c57b-296">If so, using the session state TempData provider has no additional cost to the app (aside from the size of the data).</span></span>
2. <span data-ttu-id="7c57b-297">Aplikace používá TempData pouze zřídka u relativně malých objemů dat (až 500 bajtů)?</span><span class="sxs-lookup"><span data-stu-id="7c57b-297">Does the app use TempData only sparingly for relatively small amounts of data (up to 500 bytes)?</span></span> <span data-ttu-id="7c57b-298">Pokud tedy TempData zprostředkovatele souborů cookie přidá s malými náklady pro každý požadavek, který představuje TempData.</span><span class="sxs-lookup"><span data-stu-id="7c57b-298">If so, the cookie TempData provider adds a small cost to each request that carries TempData.</span></span> <span data-ttu-id="7c57b-299">V opačném případě může být výhodné vyhnout verzemi velké množství dat v každém požadavku, dokud spotřebované TempData TempData zprostředkovatel stavu relací.</span><span class="sxs-lookup"><span data-stu-id="7c57b-299">If not, the session state TempData provider can be beneficial to avoid round-tripping a large amount of data in each request until the TempData is consumed.</span></span>
3. <span data-ttu-id="7c57b-300">Aplikace běží v serverové farmě na více serverů?</span><span class="sxs-lookup"><span data-stu-id="7c57b-300">Does the app run in a server farm on multiple servers?</span></span> <span data-ttu-id="7c57b-301">Pokud tedy není žádná další konfigurace povinná používat poskytovatele TempData souboru cookie mimo ochrany dat (viz <xref:security/data-protection/introduction> a [zprostředkovateli úložiště klíčů](xref:security/data-protection/implementation/key-storage-providers)).</span><span class="sxs-lookup"><span data-stu-id="7c57b-301">If so, there's no additional configuration required to use the cookie TempData provider outside of Data Protection (see <xref:security/data-protection/introduction> and [Key storage providers](xref:security/data-protection/implementation/key-storage-providers)).</span></span>

> [!NOTE]
> <span data-ttu-id="7c57b-302">Většina webových klientů (například webové prohlížeče) vynutit omezení maximální velikosti jednotlivých souborů cookie a celkový počet souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="7c57b-302">Most web clients (such as web browsers) enforce limits on the maximum size of each cookie, the total number of cookies, or both.</span></span> <span data-ttu-id="7c57b-303">Při použití zprostředkovatele TempData souboru cookie, ověřte, že aplikace nebude tato omezení překročí.</span><span class="sxs-lookup"><span data-stu-id="7c57b-303">When using the cookie TempData provider, verify the app won't exceed these limits.</span></span> <span data-ttu-id="7c57b-304">Celková velikost dat vezměte v úvahu.</span><span class="sxs-lookup"><span data-stu-id="7c57b-304">Consider the total size of the data.</span></span> <span data-ttu-id="7c57b-305">Účet pro zvýšení velikost souboru cookie kvůli šifrování a dělením dat do bloků.</span><span class="sxs-lookup"><span data-stu-id="7c57b-305">Account for increases in cookie size due to encryption and chunking.</span></span>

### <a name="configure-the-tempdata-provider"></a><span data-ttu-id="7c57b-306">Konfigurace poskytovatele TempData</span><span class="sxs-lookup"><span data-stu-id="7c57b-306">Configure the TempData provider</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="7c57b-307">Ve výchozím nastavení je povolen zprostředkovatel TempData na základě souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="7c57b-307">The cookie-based TempData provider is enabled by default.</span></span>

<span data-ttu-id="7c57b-308">Povolit poskytovatele TempData založeného na relacích, použijte [AddSessionStateTempDataProvider](/dotnet/api/microsoft.extensions.dependencyinjection.mvcviewfeaturesmvcbuilderextensions.addsessionstatetempdataprovider) – metoda rozšíření:</span><span class="sxs-lookup"><span data-stu-id="7c57b-308">To enable the session-based TempData provider, use the [AddSessionStateTempDataProvider](/dotnet/api/microsoft.extensions.dependencyinjection.mvcviewfeaturesmvcbuilderextensions.addsessionstatetempdataprovider) extension method:</span></span>

[!code-csharp[](app-state/samples_snapshot_2/2.x/SessionSample/Startup.cs?name=snippet1&highlight=11,13,32)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="7c57b-309">Následující `Startup` kód třídy nakonfiguruje TempData zprostředkovatele na základě relace:</span><span class="sxs-lookup"><span data-stu-id="7c57b-309">The following `Startup` class code configures the session-based TempData provider:</span></span>

[!code-csharp[](app-state/samples_snapshot_2/1.x/SessionSample/Startup.cs?name=snippet1&highlight=4,9)]

::: moniker-end

<span data-ttu-id="7c57b-310">Je důležité pořadí middlewaru.</span><span class="sxs-lookup"><span data-stu-id="7c57b-310">The order of middleware is important.</span></span> <span data-ttu-id="7c57b-311">V předchozím příkladu `InvalidOperationException` dojde k výjimce při `UseSession` je volána poté, co `UseMvc`.</span><span class="sxs-lookup"><span data-stu-id="7c57b-311">In the preceding example, an `InvalidOperationException` exception occurs when `UseSession` is invoked after `UseMvc`.</span></span> <span data-ttu-id="7c57b-312">Další informace najdete v tématu [Middleware řazení](xref:fundamentals/middleware/index#order).</span><span class="sxs-lookup"><span data-stu-id="7c57b-312">For more information, see [Middleware Ordering](xref:fundamentals/middleware/index#order).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7c57b-313">V případě cílení na rozhraní .NET Framework a pomocí zprostředkovatele TempData založeného na relacích přidáte [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) balíčku do projektu.</span><span class="sxs-lookup"><span data-stu-id="7c57b-313">If targeting .NET Framework and using the session-based TempData provider, add the [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) package to the project.</span></span>

## <a name="query-strings"></a><span data-ttu-id="7c57b-314">Řetězce dotazů</span><span class="sxs-lookup"><span data-stu-id="7c57b-314">Query strings</span></span>

<span data-ttu-id="7c57b-315">Omezené množství dat, je možné předat z jednoho požadavku na jiný tak, že přidáte novou žádost o řetězec dotazu.</span><span class="sxs-lookup"><span data-stu-id="7c57b-315">A limited amount of data can be passed from one request to another by adding it to the new request's query string.</span></span> <span data-ttu-id="7c57b-316">To je užitečné pro zaznamenání stavu trvalé způsobem, který umožňuje propojení s vložený stavu sdílet prostřednictvím e-mailu nebo sociální sítě.</span><span class="sxs-lookup"><span data-stu-id="7c57b-316">This is useful for capturing state in a persistent manner that allows links with embedded state to be shared through email or social networks.</span></span> <span data-ttu-id="7c57b-317">Protože řetězce dotazu adresy URL jsou veřejné, nikdy nepoužívejte řetězce dotazu pro citlivá data.</span><span class="sxs-lookup"><span data-stu-id="7c57b-317">Because URL query strings are public, never use query strings for sensitive data.</span></span>

<span data-ttu-id="7c57b-318">Kromě neúmyslnému sdílení, včetně dat v řetězcích dotazů můžete vytvořit příležitosti pro [padělání žádosti mezi weby (CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) útoků, které můžou přimět navštívit weby se zlými úmysly při ověření uživatele.</span><span class="sxs-lookup"><span data-stu-id="7c57b-318">In addition to unintended sharing, including data in query strings can create opportunities for [Cross-Site Request Forgery (CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) attacks, which can trick users into visiting malicious sites while authenticated.</span></span> <span data-ttu-id="7c57b-319">Útočníci můžou potom ukrást uživatelská data z aplikace nebo škodlivé akce jménem uživatele.</span><span class="sxs-lookup"><span data-stu-id="7c57b-319">Attackers can then steal user data from the app or take malicious actions on behalf of the user.</span></span> <span data-ttu-id="7c57b-320">Stav relace ani zachovaných aplikace musí chránit proti útokům CSRF.</span><span class="sxs-lookup"><span data-stu-id="7c57b-320">Any preserved app or session state must protect against CSRF attacks.</span></span> <span data-ttu-id="7c57b-321">Další informace najdete v tématu [útokům zabránilo webů žádosti padělání (XSRF/CSRF)](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="7c57b-321">For more information, see [Prevent Cross-Site Request Forgery (XSRF/CSRF) attacks](xref:security/anti-request-forgery).</span></span>

## <a name="hidden-fields"></a><span data-ttu-id="7c57b-322">Skrytá pole</span><span class="sxs-lookup"><span data-stu-id="7c57b-322">Hidden fields</span></span>

<span data-ttu-id="7c57b-323">Data můžete uložit ve skrytých polí ve formuláři a pošle zpátky na další požadavek.</span><span class="sxs-lookup"><span data-stu-id="7c57b-323">Data can be saved in hidden form fields and posted back on the next request.</span></span> <span data-ttu-id="7c57b-324">To je běžné v vícestránkového formulářů.</span><span class="sxs-lookup"><span data-stu-id="7c57b-324">This is common in multi-page forms.</span></span> <span data-ttu-id="7c57b-325">Vzhledem k tomu, že klient může potenciálně manipulovat s daty, aplikace musí vždy znovu ověřit data uložená v skrytá pole.</span><span class="sxs-lookup"><span data-stu-id="7c57b-325">Because the client can potentially tamper with the data, the app must always revalidate the data stored in hidden fields.</span></span>

## <a name="httpcontextitems"></a><span data-ttu-id="7c57b-326">HttpContext.Items</span><span class="sxs-lookup"><span data-stu-id="7c57b-326">HttpContext.Items</span></span>

<span data-ttu-id="7c57b-327">[HttpContext.Items](/dotnet/api/microsoft.aspnetcore.http.httpcontext.items) kolekce se používá k ukládání dat při zpracování jedné žádosti.</span><span class="sxs-lookup"><span data-stu-id="7c57b-327">The [HttpContext.Items](/dotnet/api/microsoft.aspnetcore.http.httpcontext.items) collection is used to store data while processing a single request.</span></span> <span data-ttu-id="7c57b-328">Po zpracování požadavku se zahodí obsah do kolekce.</span><span class="sxs-lookup"><span data-stu-id="7c57b-328">The collection's contents are discarded after a request is processed.</span></span> <span data-ttu-id="7c57b-329">`Items` Kolekci často používá k povolení komponenty nebo middlewaru, který má komunikovat, když fungovat v různých okamžicích v době požadavek a mít přímý způsob, jak předávat parametry.</span><span class="sxs-lookup"><span data-stu-id="7c57b-329">The `Items` collection is often used to allow components or middleware to communicate when they operate at different points in time during a request and have no direct way to pass parameters.</span></span>

<span data-ttu-id="7c57b-330">V následujícím příkladu [middleware](xref:fundamentals/middleware/index) přidá `isVerified` k `Items` kolekce.</span><span class="sxs-lookup"><span data-stu-id="7c57b-330">In the following example, [middleware](xref:fundamentals/middleware/index) adds `isVerified` to the `Items` collection.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // perform some verification
    context.Items["isVerified"] = true;
    await next.Invoke();
});
```

<span data-ttu-id="7c57b-331">Později v kanálu, můžete další middleware přistupovat k hodnotě `isVerified`:</span><span class="sxs-lookup"><span data-stu-id="7c57b-331">Later in the pipeline, another middleware can access the value of `isVerified`:</span></span>

```csharp
app.Run(async (context) =>
{
    await context.Response.WriteAsync($"Verified: {context.Items["isVerified"]}");
});
```

<span data-ttu-id="7c57b-332">Pro middleware, který se používá jenom jednu aplikaci `string` klíče jsou přijatelné.</span><span class="sxs-lookup"><span data-stu-id="7c57b-332">For middleware that's only used by a single app, `string` keys are acceptable.</span></span> <span data-ttu-id="7c57b-333">Middleware, které jsou sdílené mezi instancemi aplikace by měl použít jedinečný objekt klíče pro zabránění kolizím klíče.</span><span class="sxs-lookup"><span data-stu-id="7c57b-333">Middleware shared between app instances should use unique object keys to avoid key collisions.</span></span> <span data-ttu-id="7c57b-334">Následující příklad ukazuje, jak používat klíč jedinečný objekt definovaný ve třídě middleware:</span><span class="sxs-lookup"><span data-stu-id="7c57b-334">The following example shows how to use a unique object key defined in a middleware class:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-state/samples/2.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=4,13)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](app-state/samples/1.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=5,14)]

::: moniker-end

<span data-ttu-id="7c57b-335">Další kód může přistupovat k hodnotu uloženou v `HttpContext.Items` pomocí klíče vystavené middleware třídy:</span><span class="sxs-lookup"><span data-stu-id="7c57b-335">Other code can access the value stored in `HttpContext.Items` using the key exposed by the middleware class:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet3)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](app-state/samples/1.x/SessionSample/Controllers/HomeController.cs?name=snippet3)]

::: moniker-end

<span data-ttu-id="7c57b-336">Tento přístup také nabízí výhodu v podobě odstranění použití klíčů řetězců v kódu.</span><span class="sxs-lookup"><span data-stu-id="7c57b-336">This approach also has the advantage of eliminating the use of key strings in the code.</span></span>

## <a name="cache"></a><span data-ttu-id="7c57b-337">Mezipaměť</span><span class="sxs-lookup"><span data-stu-id="7c57b-337">Cache</span></span>

<span data-ttu-id="7c57b-338">Ukládání do mezipaměti je účinný způsob, jak ukládat a načítat data.</span><span class="sxs-lookup"><span data-stu-id="7c57b-338">Caching is an efficient way to store and retrieve data.</span></span> <span data-ttu-id="7c57b-339">Aplikace může kontrolovat životnost položek v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="7c57b-339">The app can control the lifetime of cached items.</span></span>

<span data-ttu-id="7c57b-340">Data uložená v mezipaměti není spojen s konkrétní požadavek, uživatelem nebo relací.</span><span class="sxs-lookup"><span data-stu-id="7c57b-340">Cached data isn't associated with a specific request, user, or session.</span></span> <span data-ttu-id="7c57b-341">**Buďte opatrní není konkrétního uživatele ukládat data do mezipaměti, který může načíst žádostmi o jiných uživatelů.**</span><span class="sxs-lookup"><span data-stu-id="7c57b-341">**Be careful not to cache user-specific data that may be retrieved by other users' requests.**</span></span>

<span data-ttu-id="7c57b-342">Další informace naleznete v tématu <xref:performance/caching/response>.</span><span class="sxs-lookup"><span data-stu-id="7c57b-342">For more information, see <xref:performance/caching/response>.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="7c57b-343">Injektáž závislostí</span><span class="sxs-lookup"><span data-stu-id="7c57b-343">Dependency Injection</span></span>

<span data-ttu-id="7c57b-344">Použití [injektáž závislostí](xref:fundamentals/dependency-injection) zpřístupnění dat pro všechny uživatele:</span><span class="sxs-lookup"><span data-stu-id="7c57b-344">Use [Dependency Injection](xref:fundamentals/dependency-injection) to make data available to all users:</span></span>

1. <span data-ttu-id="7c57b-345">Definujte službu obsahující data.</span><span class="sxs-lookup"><span data-stu-id="7c57b-345">Define a service containing the data.</span></span> <span data-ttu-id="7c57b-346">Například třída s názvem `MyAppData` je definována:</span><span class="sxs-lookup"><span data-stu-id="7c57b-346">For example, a class named `MyAppData` is defined:</span></span>

    ```csharp
    public class MyAppData
    {
        // Declare properties and methods
    }
    ```

2. <span data-ttu-id="7c57b-347">Přidat třídu služby `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7c57b-347">Add the service class to `Startup.ConfigureServices`:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<MyAppData>();
    }
    ```

3. <span data-ttu-id="7c57b-348">Využívat třídě datové služby:</span><span class="sxs-lookup"><span data-stu-id="7c57b-348">Consume the data service class:</span></span>

    ::: moniker range=">= aspnetcore-2.0"

    ```csharp
    public class IndexModel : PageModel
    {
        public IndexModel(MyAppData myService)
        {
            // Do something with the service
            //    Examples: Read data, store in a field or property
        }
    }
    ```

    ::: moniker-end

    ::: moniker range="< aspnetcore-2.0"

    ```csharp
    public class HomeController : Controller
    {
        public HomeController(MyAppData myService)
        {
            // Do something with the service
            //    Examples: Read data, store in a field or property
        }
    }
    ```

    ::: moniker-end

## <a name="common-errors"></a><span data-ttu-id="7c57b-349">Běžné chyby</span><span class="sxs-lookup"><span data-stu-id="7c57b-349">Common errors</span></span>

* <span data-ttu-id="7c57b-350">"Nepovedlo se přeložit služby pro typ"Microsoft.Extensions.Caching.Distributed.IDistributedCache"při pokusu o aktivaci"Microsoft.AspNetCore.Session.DistributedSessionStore"."</span><span class="sxs-lookup"><span data-stu-id="7c57b-350">"Unable to resolve service for type 'Microsoft.Extensions.Caching.Distributed.IDistributedCache' while attempting to activate 'Microsoft.AspNetCore.Session.DistributedSessionStore'."</span></span>

  <span data-ttu-id="7c57b-351">To je obvykle způsobeno selhání nakonfigurujte alespoň jednu `IDistributedCache` implementace.</span><span class="sxs-lookup"><span data-stu-id="7c57b-351">This is usually caused by failing to configure at least one `IDistributedCache` implementation.</span></span> <span data-ttu-id="7c57b-352">Další informace naleznete v tématu <xref:performance/caching/distributed> a <xref:performance/caching/memory>.</span><span class="sxs-lookup"><span data-stu-id="7c57b-352">For more information, see <xref:performance/caching/distributed> and <xref:performance/caching/memory>.</span></span>

* <span data-ttu-id="7c57b-353">V události, která relace, které middleware nepodaří zachována relaci (například, pokud záložní úložiště není k dispozici), middleware protokoluje výjimku a požadavek pokračuje normálním způsobem.</span><span class="sxs-lookup"><span data-stu-id="7c57b-353">In the event that the session middleware fails to persist a session (for example, if the backing store isn't available), the middleware logs the exception and the request continues normally.</span></span> <span data-ttu-id="7c57b-354">To vede k nepředvídatelné chování.</span><span class="sxs-lookup"><span data-stu-id="7c57b-354">This leads to unpredictable behavior.</span></span>

  <span data-ttu-id="7c57b-355">Uživatel například ukládá do nákupního košíku v relaci.</span><span class="sxs-lookup"><span data-stu-id="7c57b-355">For example, a user stores a shopping cart in session.</span></span> <span data-ttu-id="7c57b-356">Uživatel přidá položky do košíku, ale potvrzení nezdaří.</span><span class="sxs-lookup"><span data-stu-id="7c57b-356">The user adds an item to the cart but the commit fails.</span></span> <span data-ttu-id="7c57b-357">Aplikace neví o selhání, aby hlásila uživateli, že byla položka přidána do jejich košíku, což neplatí.</span><span class="sxs-lookup"><span data-stu-id="7c57b-357">The app doesn't know about the failure so it reports to the user that the item was added to their cart, which isn't true.</span></span>

  <span data-ttu-id="7c57b-358">Doporučený postup ke kontrole chyb je volání `await feature.Session.CommitAsync();` od kódu aplikace, pokud aplikace provádí zápis do relace.</span><span class="sxs-lookup"><span data-stu-id="7c57b-358">The recommended approach to check for errors is to call `await feature.Session.CommitAsync();` from app code when the app is done writing to the session.</span></span> <span data-ttu-id="7c57b-359">`CommitAsync` vyvolá výjimku, pokud záložní úložiště není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="7c57b-359">`CommitAsync` throws an exception if the backing store is unavailable.</span></span> <span data-ttu-id="7c57b-360">Pokud `CommitAsync` nezdaří, aplikace může zpracovat výjimku.</span><span class="sxs-lookup"><span data-stu-id="7c57b-360">If `CommitAsync` fails, the app can process the exception.</span></span> <span data-ttu-id="7c57b-361">`LoadAsync` vyvolá za stejných podmínek, kde je úložiště dat není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="7c57b-361">`LoadAsync` throws under the same conditions where the data store is unavailable.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7c57b-362">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="7c57b-362">Additional resources</span></span>

<xref:host-and-deploy/web-farm>
