---
uid: web-pages/overview/testing-and-debugging/aspnet-web-pages-razor-troubleshooting-guide
title: ASP.NET – webové stránky příručky pro řešení potíží (Razor) | Dokumentace Microsoftu
author: tfitzmac
description: Tento článek popisuje problémy, které můžete mít při práci s webových stránek ASP.NET (Razor) a některé doporučené řešení. Verze softwaru Úvo ASP.NET Web...
ms.author: riande
ms.date: 02/10/2014
ms.assetid: 2a2c1833-0bfe-4e2e-9cc0-341b52c7b121
msc.legacyurl: /web-pages/overview/testing-and-debugging/aspnet-web-pages-razor-troubleshooting-guide
msc.type: authoredcontent
ms.openlocfilehash: c27139a720decd34a4ab89e6f93e71c97d123b45
ms.sourcegitcommit: 45ac74e400f9f2b7dbded66297730f6f14a4eb25
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2018
ms.locfileid: "41752607"
---
<a name="aspnet-web-pages-razor-troubleshooting-guide"></a><span data-ttu-id="7a94a-104">ASP.NET – webové stránky příručky pro řešení potíží (Razor)</span><span class="sxs-lookup"><span data-stu-id="7a94a-104">ASP.NET Web Pages (Razor) Troubleshooting Guide</span></span>
====================
<span data-ttu-id="7a94a-105">podle [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="7a94a-105">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

> <span data-ttu-id="7a94a-106">Tento článek popisuje problémy, které můžete mít při práci s webových stránek ASP.NET (Razor) a některé doporučené řešení.</span><span class="sxs-lookup"><span data-stu-id="7a94a-106">This article describes issues that you might have when working with ASP.NET Web Pages (Razor) and some suggested solutions.</span></span>
> 
> ## <a name="software-versions"></a><span data-ttu-id="7a94a-107">Verze softwaru</span><span class="sxs-lookup"><span data-stu-id="7a94a-107">Software versions</span></span>
> 
> 
> - <span data-ttu-id="7a94a-108">Webové stránky ASP.NET (Razor) 3</span><span class="sxs-lookup"><span data-stu-id="7a94a-108">ASP.NET Web Pages (Razor) 3</span></span>
>   
> 
> <span data-ttu-id="7a94a-109">V tomto kurzu se také pracuje s ASP.NET Web Pages 2 a ASP.NET Web Pages 1.0.</span><span class="sxs-lookup"><span data-stu-id="7a94a-109">This tutorial also works with ASP.NET Web Pages 2 and ASP.NET Web Pages 1.0.</span></span>


<span data-ttu-id="7a94a-110">Toto téma obsahuje následující oddíly:</span><span class="sxs-lookup"><span data-stu-id="7a94a-110">This topic contains the following sections:</span></span>

- [<span data-ttu-id="7a94a-111">Problémy se spouštěním stránky</span><span class="sxs-lookup"><span data-stu-id="7a94a-111">Issues with Running Pages</span></span>](#Issues_Running_.cshtml_Pages)
- [<span data-ttu-id="7a94a-112">Problémy s kódem Razor</span><span class="sxs-lookup"><span data-stu-id="7a94a-112">Issues with Razor Code</span></span>](#IssuesWithRazorCode)
- [<span data-ttu-id="7a94a-113">Problémy se zabezpečením a členství</span><span class="sxs-lookup"><span data-stu-id="7a94a-113">Issues with Security and Membership</span></span>](#membership)
- [<span data-ttu-id="7a94a-114">Problémy s odesláním e-mailu</span><span class="sxs-lookup"><span data-stu-id="7a94a-114">Issues with Sending Email</span></span>](#email)
- [<span data-ttu-id="7a94a-115">Další prostředky</span><span class="sxs-lookup"><span data-stu-id="7a94a-115">Additional Resources</span></span>](#AdditionalResources)

<span data-ttu-id="7a94a-116">Obecné dotazy najdete v tématu [webových stránek ASP.NET (Razor) – nejčastější dotazy](https://go.microsoft.com/fwlink/?LinkId=253000).</span><span class="sxs-lookup"><span data-stu-id="7a94a-116">For general questions, see [ASP.NET Web Pages (Razor) FAQ](https://go.microsoft.com/fwlink/?LinkId=253000).</span></span>

<a id="Issues_Running_.cshtml_Pages"></a>
## <a name="issues-with-running-pages"></a><span data-ttu-id="7a94a-117">Problémy se spouštěním stránky</span><span class="sxs-lookup"><span data-stu-id="7a94a-117">Issues with Running Pages</span></span>

<span data-ttu-id="7a94a-118">Celou řadou potíží může zabránit *.cshtml* a *.vbhtml* stránky ve správném spuštění.</span><span class="sxs-lookup"><span data-stu-id="7a94a-118">A variety of issues can prevent *.cshtml* and *.vbhtml* pages from running properly.</span></span> <span data-ttu-id="7a94a-119">Tato část uvádí běžné chybové zprávy a pravděpodobně způsobí, že.</span><span class="sxs-lookup"><span data-stu-id="7a94a-119">This section lists common error messages and likely causes.</span></span>

### <a name="http-error-403---forbidden-access-is-denied"></a><span data-ttu-id="7a94a-120">Chyba protokolu HTTP 403 – Zakázáno: Přístup byl odepřen.</span><span class="sxs-lookup"><span data-stu-id="7a94a-120">HTTP Error 403 - Forbidden: Access is denied</span></span>

<span data-ttu-id="7a94a-121">*Nemáte oprávnění k zobrazení tohoto adresáře nebo stránky pomocí zadaných přihlašovacích údajů.*</span><span class="sxs-lookup"><span data-stu-id="7a94a-121">*You do not have permission to view this directory or page using the credentials that you supplied.*</span></span>

<span data-ttu-id="7a94a-122">K této chybě může dojít, pokud server není spuštěn správnou verzi rozhraní .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="7a94a-122">This error can occur if the server is not running the correct version of the .NET Framework.</span></span> <span data-ttu-id="7a94a-123">Ujistěte se, že má počítač, na kterém běží server (místně nebo vzdáleně) alespoň nainstalované rozhraní .NET Framework 4.</span><span class="sxs-lookup"><span data-stu-id="7a94a-123">Make sure that the computer that's running the server (locally or remotely) has at least the .NET Framework 4 installed.</span></span> <span data-ttu-id="7a94a-124">Také se ujistěte, že samotná aplikace je nakonfigurován ke spuštění správnou verzi.</span><span class="sxs-lookup"><span data-stu-id="7a94a-124">Also make sure that the application itself is configured to run the right version.</span></span>

<span data-ttu-id="7a94a-125">Pokud tento problém se zobrazí místně při práci v nástroji WebMatrix, klikněte na tlačítko **lokality** prostoru a ve stromovém zobrazení klikněte na **nastavení**.</span><span class="sxs-lookup"><span data-stu-id="7a94a-125">If you see this problem locally while working in WebMatrix, click the **Site** workspace, and then in the treeview click **Settings**.</span></span> <span data-ttu-id="7a94a-126">V **vyberte verzi rozhraní .NET Framework** vyberte **.NET 4 (integrovaný režim)**.</span><span class="sxs-lookup"><span data-stu-id="7a94a-126">In the **Select .NET Framework Version** list, select **.NET 4 (Integrated)**.</span></span> <span data-ttu-id="7a94a-127">Pokud tato verze je již nastavena, zkuste spustit službu WebMatrix jako správce.</span><span class="sxs-lookup"><span data-stu-id="7a94a-127">If this version is already set, try running WebMatrix as an administrator.</span></span>

<span data-ttu-id="7a94a-128">Ujistěte se, že kořenový web má alespoň jednu *.cshtml* soubor v ní.</span><span class="sxs-lookup"><span data-stu-id="7a94a-128">Make sure that the root of your website has at least one *.cshtml* file in it.</span></span>

<span data-ttu-id="7a94a-129">Pokud tato chyba se zobrazí, když je webový server na vzdáleném serveru, obraťte se na správce serveru.</span><span class="sxs-lookup"><span data-stu-id="7a94a-129">If you see this error when the web server is on a remote server, contact the server administrator.</span></span> <span data-ttu-id="7a94a-130">Ověřte, že server má rozhraní .NET Framework 4 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="7a94a-130">Make sure that the server has the .NET Framework 4 or later installed.</span></span> <span data-ttu-id="7a94a-131">Také se ujistěte, že aplikace běží ve fondu aplikací, který je nakonfigurovaný na tuto verzi rozhraní.NET Framework.</span><span class="sxs-lookup"><span data-stu-id="7a94a-131">Also make sure that the application is running in an application pool that's configured to use that version of the.NET Framework.</span></span>

<span data-ttu-id="7a94a-132">Pokud budete mít kontrolu nad serveru, ujistěte se, že běží správnou verzi rozhraní .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="7a94a-132">If you have control over the server, make sure it's running the correct version of the .NET Framework.</span></span> <span data-ttu-id="7a94a-133">Můžete se taky může snažit opravíte spuštěním instalace `aspnet_regiis -iru` příkazu.</span><span class="sxs-lookup"><span data-stu-id="7a94a-133">You might also try repairing the installation by running the `aspnet_regiis -iru` command.</span></span> <span data-ttu-id="7a94a-134">(Například, pokud nainstalujete službu IIS po instalaci rozhraní .NET Framework, služby IIS nebudě správně nakonfigurovaná pro spuštění stránky technologie ASP.NET.) Další informace najdete v tématu [ASP.NET IIS Registrační nástroj (Aspnet\_regiis.exe)](https://msdn.microsoft.com/library/k6h9cz8h(v=vs.100).aspx).</span><span class="sxs-lookup"><span data-stu-id="7a94a-134">(For example, if you install IIS after you install the .NET Framework, IIS will not be correctly configured to run ASP.NET pages.) For more information, see [ASP.NET IIS Registration Tool (Aspnet\_regiis.exe)](https://msdn.microsoft.com/library/k6h9cz8h(v=vs.100).aspx).</span></span>

### <a name="http-error-40314---forbidden"></a><span data-ttu-id="7a94a-135">Chyba protokolu HTTP 403.14 – zakázáno</span><span class="sxs-lookup"><span data-stu-id="7a94a-135">HTTP Error 403.14 - Forbidden</span></span>

<span data-ttu-id="7a94a-136">*Webový server je nakonfigurován na obsah tohoto adresáře v seznamu.*</span><span class="sxs-lookup"><span data-stu-id="7a94a-136">*The Web server is configured to not list the contents of this directory.*</span></span>

<span data-ttu-id="7a94a-137">K této chybě může dojít, pokud si vyžádáte prostředek, který je chráněný (stejně jako *Web.config* soubor) nebo který je ve složce, která je chráněná (jako *aplikace\_Data* nebo *aplikace\_Kód*).</span><span class="sxs-lookup"><span data-stu-id="7a94a-137">This error can occur if you request a resource that's protected (like the *Web.config* file) or that's in a folder that's protected (like *App\_Data* or *App\_Code*).</span></span>

### <a name="http-error-40417---not-found"></a><span data-ttu-id="7a94a-138">Chyba protokolu HTTP 404.17 – Nenalezeno</span><span class="sxs-lookup"><span data-stu-id="7a94a-138">HTTP Error 404.17 - Not Found</span></span>

<span data-ttu-id="7a94a-139">*Požadovaný obsah se zdá být skript a nebude obsluhuje statický soubor obslužnou rutinou.*</span><span class="sxs-lookup"><span data-stu-id="7a94a-139">*The requested content appears to be script and will not be served by the static file handler.*</span></span>

<span data-ttu-id="7a94a-140">K této chybě může dojít, pokud server není správně nakonfigurovaný pro použití rozhraní .NET Framework 4 nebo novější a proto nemůže rozpoznat kód v `@{ }` bloky.</span><span class="sxs-lookup"><span data-stu-id="7a94a-140">This error can occur if the server is not configured correctly to use the .NET Framework 4 or later, and therefore does not recognize the code in `@{ }` blocks.</span></span> <span data-ttu-id="7a94a-141">Naleznete v popisu výše pro *HTTP Chyba 403 – Zakázáno: přístup byl odepřen*.</span><span class="sxs-lookup"><span data-stu-id="7a94a-141">See the description earlier for *HTTP Error 403 - Forbidden: Access is denied*.</span></span>

### <a name="http-error-4047---not-found"></a><span data-ttu-id="7a94a-142">Chyba protokolu HTTP 404.7 – nebyl nalezen</span><span class="sxs-lookup"><span data-stu-id="7a94a-142">HTTP Error 404.7 - Not Found</span></span>

<span data-ttu-id="7a94a-143">*Modul filtrování žádostí je konfigurován k odepření přípona souboru*</span><span class="sxs-lookup"><span data-stu-id="7a94a-143">*The request filtering module is configured to deny the file extension*</span></span>

<span data-ttu-id="7a94a-144">K této chybě může dojít, pokud *.cshtml* nebo *.vbhtml* rozšíření byl explicitně zakázán na serveru.</span><span class="sxs-lookup"><span data-stu-id="7a94a-144">This error can occur if *.cshtml* or *.vbhtml* extensions have been explicitly blocked on the server.</span></span> <span data-ttu-id="7a94a-145">Příznaky tohoto problému je, které pracují adresy URL, pokud neobsahují rozšíření, ale adresy URL, které zahrnují *.cshtml* nebo *.vbhtml* nefungují.</span><span class="sxs-lookup"><span data-stu-id="7a94a-145">A symptom of this problem is that URLs work when they do not include the extension, but URLs that include *.cshtml* or *.vbhtml* do not work.</span></span> <span data-ttu-id="7a94a-146">Opětovné povolení rozšíření na webu je možné řešení *Web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="7a94a-146">A possible solution is to re-enable the extensions in the site's *Web.config* file.</span></span> <span data-ttu-id="7a94a-147">Následující příklad ukazuje, jak povolit *.cshtml* rozšíření.</span><span class="sxs-lookup"><span data-stu-id="7a94a-147">The following example shows how to enable the *.cshtml* extension.</span></span>

[!code-xml[Main](aspnet-web-pages-razor-troubleshooting-guide/samples/sample1.xml?highlight=5-6)]

### <a name="http-error-4048---not-found"></a><span data-ttu-id="7a94a-148">Chyba protokolu HTTP 404.8 – nebyl nalezen</span><span class="sxs-lookup"><span data-stu-id="7a94a-148">HTTP Error 404.8 - Not Found</span></span>

<span data-ttu-id="7a94a-149">*Modul filtrování žádostí je konfigurován k odepření cestě v adrese URL, která obsahuje oddíl hiddenSegment.*</span><span class="sxs-lookup"><span data-stu-id="7a94a-149">*The request filtering module is configured to deny a path in the URL that contains a hiddenSegment section.*</span></span>

<span data-ttu-id="7a94a-150">K této chybě může dojít, pokud si vyžádáte prostředek, který je chráněný (stejně jako *Web.config* soubor) nebo který je ve složce, která je chráněná (jako *aplikace\_Data* nebo *aplikace\_Kód*).</span><span class="sxs-lookup"><span data-stu-id="7a94a-150">This error can occur if you request a resource that's protected (like the *Web.config* file) or that's in a folder that's protected (like *App\_Data* or *App\_Code*).</span></span>

### <a name="this-type-of-page-is-not-served-server-error-in--application"></a><span data-ttu-id="7a94a-151">Tento typ stránky není poskytováni (Chyba serveru v aplikaci "/")</span><span class="sxs-lookup"><span data-stu-id="7a94a-151">This type of page is not served (Server Error in '/' Application)</span></span>

<span data-ttu-id="7a94a-152">Naleznete v popisu výše pro HTTP 404.17 chyby.</span><span class="sxs-lookup"><span data-stu-id="7a94a-152">See the description earlier for HTTP Error 404.17.</span></span>

<a id="IssuesWithRazorCode"></a>
## <a name="issues-with-razor-code"></a><span data-ttu-id="7a94a-153">Problémy s kódem Razor</span><span class="sxs-lookup"><span data-stu-id="7a94a-153">Issues with Razor code</span></span>

### <a name="the-name-class-does-not-exist-in-the-current-context"></a><span data-ttu-id="7a94a-154">Název "*třídy*' neexistuje v aktuálním kontextu</span><span class="sxs-lookup"><span data-stu-id="7a94a-154">The name '*class*' does not exist in the current context</span></span>

<span data-ttu-id="7a94a-155">Často, důvod se zobrazí tato chyba je, že `class` odkazy pomocné rutiny, ale pomocné rutiny není nainstalována.</span><span class="sxs-lookup"><span data-stu-id="7a94a-155">Often, a reason you see this error is that `class` references a helper, but the helper is not installed.</span></span> <span data-ttu-id="7a94a-156">Například pokud se pokusíte použít pomocné rutiny, ale pokud jste nenainstalovali balíček z Nugetu, zobrazí se vám tato chyba.</span><span class="sxs-lookup"><span data-stu-id="7a94a-156">For example, if you try to use a helper, but if you haven't installed the package from NuGet, you'll see this error.</span></span> <span data-ttu-id="7a94a-157">Pomocí Galerie ve službě WebMatrix můžete najít a nainstalovat pomocné rutiny.</span><span class="sxs-lookup"><span data-stu-id="7a94a-157">Use the Gallery in WebMatrix to find and install the helper.</span></span>

<span data-ttu-id="7a94a-158">Pokud je nainstalovaná pomocné rutiny, ale stránce stále nerozpoznal ho, vyzkoušejte přidávání přidat `using` příkaz kódu.</span><span class="sxs-lookup"><span data-stu-id="7a94a-158">If the helper is installed, but the page still doesn't recognize it, try adding add a `using` statement to the code.</span></span> <span data-ttu-id="7a94a-159">V `using` prohlášení, odkaz na obor názvů, který obsahuje pomocné rutiny.</span><span class="sxs-lookup"><span data-stu-id="7a94a-159">In the `using` statement, reference the namespace that includes the helper.</span></span> <span data-ttu-id="7a94a-160">Například základní pomocné rutiny, které jsou součástí balíčku ASP.NET Web Helpers jsou v `System.Web.Helpers` oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="7a94a-160">For example, the basic helpers that are in the ASP.NET Web Helpers package are in the `System.Web.Helpers` namespace.</span></span> <span data-ttu-id="7a94a-161">V horní části stránky, kde chcete použít pomocné rutiny přidejte následující řádek:</span><span class="sxs-lookup"><span data-stu-id="7a94a-161">At the top of the page where you want to use the helper, add this line:</span></span>

`@using Microsoft.Web.Helpers;`

<a id="membership"></a>
## <a name="issues-with-security-and-membership"></a><span data-ttu-id="7a94a-162">Problémy se zabezpečením a členství</span><span class="sxs-lookup"><span data-stu-id="7a94a-162">Issues with Security and Membership</span></span>

<span data-ttu-id="7a94a-163">Pokud používáte systém integrované zabezpečení (členství) na webových stránkách ASP.NET (Razor), může dojít k následujícím potížím.</span><span class="sxs-lookup"><span data-stu-id="7a94a-163">If you are using the built-in security (membership) system in ASP.NET Web Pages (Razor), you might encounter the following issues.</span></span>

### <a name="to-call-this-method-the-membershipprovider-property-must-be-an-instance-of-extendedmembershipprovider"></a><span data-ttu-id="7a94a-164">Vlastnost "Membership.Provider" mohli volat tuto metodu, musí být instance "ExtendedMembershipProvider"</span><span class="sxs-lookup"><span data-stu-id="7a94a-164">To call this method, the "Membership.Provider" property must be an instance of "ExtendedMembershipProvider"</span></span>

<span data-ttu-id="7a94a-165">Tuto chybu můžete určit, že žádné `AspNetSqlMembershipProvider` třídy je nakonfigurované.</span><span class="sxs-lookup"><span data-stu-id="7a94a-165">This error can indicate that no `AspNetSqlMembershipProvider` class is configured.</span></span> <span data-ttu-id="7a94a-166">(Příznakem je, že lokality funguje místně, ale vyvolá tuto chybu, když ji publikujete do serveru poskytovatele hostingu.) Jednu opravu tohoto problému je explicitně povolit jednoduché členství přidáním následujícího kódu na web *Web.config* souboru:</span><span class="sxs-lookup"><span data-stu-id="7a94a-166">(A symptom is that the site works fine locally but throws this error when you publish it to a hosting provider's server.) One fix for this problem is to explicitly enable simple membership by adding the following to the site's *Web.config* file:</span></span>

[!code-xml[Main](aspnet-web-pages-razor-troubleshooting-guide/samples/sample2.xml?highlight=6)]

<a id="email"></a>
## <a name="issues-with-sending-email"></a><span data-ttu-id="7a94a-167">Problémy s odesláním e-mailu</span><span class="sxs-lookup"><span data-stu-id="7a94a-167">Issues with Sending Email</span></span>

<span data-ttu-id="7a94a-168">Chcete-li ladit může být náročné problémů s odesíláním e-mailu.</span><span class="sxs-lookup"><span data-stu-id="7a94a-168">Problems with sending email can be challenging to debug.</span></span> <span data-ttu-id="7a94a-169">Počáteční problém může být, že se nemůže připojit k serveru SMTP.</span><span class="sxs-lookup"><span data-stu-id="7a94a-169">An initial problem can be that you can't connect to the SMTP server.</span></span> <span data-ttu-id="7a94a-170">Pokud je připojení úspěšné, ASP.NET předá zprávy na server SMTP.</span><span class="sxs-lookup"><span data-stu-id="7a94a-170">If the connection is successful, ASP.NET hands the message off to the SMTP server.</span></span> <span data-ttu-id="7a94a-171">Může však být problémy s vlastní zprávě, který zabraňuje odeslání na server SMTP.</span><span class="sxs-lookup"><span data-stu-id="7a94a-171">However, there can be problems with the message itself that prevents the SMTP server from sending it.</span></span>

<span data-ttu-id="7a94a-172">Pokud vaše aplikace neodesílá úspěšně e-mailu, zkuste následující:</span><span class="sxs-lookup"><span data-stu-id="7a94a-172">If your application does not successfully send email, try the following:</span></span>

- <span data-ttu-id="7a94a-173">Název serveru SMTP je často něco jako `smtp.provider.com` nebo `smtp.provider.net`.</span><span class="sxs-lookup"><span data-stu-id="7a94a-173">The SMTP server name is often something like `smtp.provider.com` or `smtp.provider.net`.</span></span> <span data-ttu-id="7a94a-174">Nicméně pokud publikování webu k poskytovateli hostingu, název serveru SMTP v tomto okamžiku může být `localhost`.</span><span class="sxs-lookup"><span data-stu-id="7a94a-174">However, if you publish your site to a hosting provider, the SMTP server name at that point might be `localhost`.</span></span> <span data-ttu-id="7a94a-175">K této situaci dochází, protože po publikování a vaše lokalita běží na serveru zprostředkovatele, může být místní z pohledu aplikace serveru SMTP.</span><span class="sxs-lookup"><span data-stu-id="7a94a-175">This situation occurs because after you've published and your site is running on the provider's server, the SMTP server might be local from the perspective of your application.</span></span> <span data-ttu-id="7a94a-176">Tato změna názvů serverů může znamenat, že budete muset změnit název serveru SMTP jako součást procesu publikování.</span><span class="sxs-lookup"><span data-stu-id="7a94a-176">This change in server names might mean that you have to change the SMTP server name as part of your publishing process.</span></span>
- <span data-ttu-id="7a94a-177">Číslo portu je obvykle 25.</span><span class="sxs-lookup"><span data-stu-id="7a94a-177">The port number is usually 25.</span></span> <span data-ttu-id="7a94a-178">Ale někteří poskytovatelé vyžadovat použití port 587 nebo některé další porty.</span><span class="sxs-lookup"><span data-stu-id="7a94a-178">However, some providers require you to use port 587 or some other port.</span></span> <span data-ttu-id="7a94a-179">Obraťte se na vlastníka SMTP server, jaké číslo portu očekávané použití.</span><span class="sxs-lookup"><span data-stu-id="7a94a-179">Check with the owner of the SMTP server what port number they expect you to use.</span></span>
- <span data-ttu-id="7a94a-180">Ujistěte se, že používáte správné přihlašovací údaje.</span><span class="sxs-lookup"><span data-stu-id="7a94a-180">Make sure that you use the right credentials.</span></span> <span data-ttu-id="7a94a-181">Pokud k poskytovateli hostingu, které jste publikovali vašeho webu, použijte přihlašovací údaje, které zprostředkovatel má výslovně uvedeno, jsou určené pro e-mailu.</span><span class="sxs-lookup"><span data-stu-id="7a94a-181">If you've published your site to a hosting provider, use the credentials that the provider has specifically indicated are for email.</span></span> <span data-ttu-id="7a94a-182">Tyto přihlašovací údaje se může lišit od přihlašovací údaje, které můžete použít k publikování.</span><span class="sxs-lookup"><span data-stu-id="7a94a-182">These credentials might be different from the credentials you use to publish.</span></span>
- <span data-ttu-id="7a94a-183">Někdy není nutné přihlašovací údaje vůbec.</span><span class="sxs-lookup"><span data-stu-id="7a94a-183">Sometimes you don't need credentials at all.</span></span> <span data-ttu-id="7a94a-184">Pokud odesíláte e-mailu pomocí svého osobního poskytovatele internetových služeb, může být vašeho poskytovatele e-mailu už znáte svoje přihlašovací údaje.</span><span class="sxs-lookup"><span data-stu-id="7a94a-184">If you're sending email by using your personal ISP, your email provider might already know your credentials.</span></span> <span data-ttu-id="7a94a-185">Po publikování, možná budete muset použít jiná pověření než při testování v místním počítači.</span><span class="sxs-lookup"><span data-stu-id="7a94a-185">After you publish, you might need to use different credentials than when you test on your local computer.</span></span>
- <span data-ttu-id="7a94a-186">Pokud vašeho poskytovatele e-mailu používá šifrování, nastavit `WebMail.EnableSsl` k `true`.</span><span class="sxs-lookup"><span data-stu-id="7a94a-186">If your email provider uses encryption, set `WebMail.EnableSsl` to `true`.</span></span>

<span data-ttu-id="7a94a-187">Pokud dojde k chybě odesílání e-mailů, může se zobrazit standardní technologie ASP.NET chybovou zprávu, která vypadá takto:</span><span class="sxs-lookup"><span data-stu-id="7a94a-187">If there is an error sending email, you might see a standard ASP.NET error message, which looks like this:</span></span>

![ASP.NET chybové zprávy, pokud dojde k nějakému problému s e-mailem](aspnet-web-pages-razor-troubleshooting-guide/_static/image1.png)

<span data-ttu-id="7a94a-189">Můžete také ladit problémy s odesláním e-mailu pomocí `try-catch` bloku, jako v následujícím příkladu.</span><span class="sxs-lookup"><span data-stu-id="7a94a-189">You can also debug problems with sending email by using a `try-catch` block, as in the following example.</span></span> <span data-ttu-id="7a94a-190">Při použití `try-catch` blok ASP.NET nezobrazí její standardní chybové zprávy.</span><span class="sxs-lookup"><span data-stu-id="7a94a-190">When you use a `try-catch` block, ASP.NET does not display its standard error messages.</span></span> <span data-ttu-id="7a94a-191">Místo toho můžete zaznamenat chybu v `catch` části bloku.</span><span class="sxs-lookup"><span data-stu-id="7a94a-191">Instead, you can capture the error in the `catch` portion of the block.</span></span>

[!code-cshtml[Main](aspnet-web-pages-razor-troubleshooting-guide/samples/sample3.cshtml)]

<span data-ttu-id="7a94a-192">Nahraďte příslušnými hodnotami pro `your-SMTP-server-name`, a tak dále.</span><span class="sxs-lookup"><span data-stu-id="7a94a-192">Substitute the appropriate values for `your-SMTP-server-name`, and so on.</span></span> <span data-ttu-id="7a94a-193">Některé chybové zprávy, které se můžou objevovat tímto způsobem, patří:</span><span class="sxs-lookup"><span data-stu-id="7a94a-193">Some of the error messages you might see this way include the following:</span></span>

- <span data-ttu-id="7a94a-194">*Chyba při odeslání e-mailu.*</span><span class="sxs-lookup"><span data-stu-id="7a94a-194">*Failure sending mail.*</span></span>

    <span data-ttu-id="7a94a-195">-nebo-</span><span class="sxs-lookup"><span data-stu-id="7a94a-195">-or-</span></span>

    <span data-ttu-id="7a94a-196">*Pokus o připojení se nezdařila, protože připojená strana neodpověděla řádně po určitou dobu nebo navázané připojení se nezdařila, protože připojený hostitel se nepodařilo odpovědět*</span><span class="sxs-lookup"><span data-stu-id="7a94a-196">*A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond*</span></span>

    <span data-ttu-id="7a94a-197">Tato chyba obvykle znamená, že aplikace nelze připojit k serveru SMTP.</span><span class="sxs-lookup"><span data-stu-id="7a94a-197">This error usually means that the application could not connect to the SMTP server.</span></span> <span data-ttu-id="7a94a-198">Zkontrolujte název serveru a číslo portu.</span><span class="sxs-lookup"><span data-stu-id="7a94a-198">Check the server name and port number.</span></span>
- <span data-ttu-id="7a94a-199"><em>Poštovní schránka není k dispozici. Odpověď serveru: 5.1.0 &lt; someuser@invaliddomain &gt; odesílatele odmítnuta: Neplatný odesílatel domény</em></span><span class="sxs-lookup"><span data-stu-id="7a94a-199"><em>Mailbox unavailable. The server response was: 5.1.0 &lt;someuser@invaliddomain&gt; sender rejected : invalid sender domain</em></span></span>

    <span data-ttu-id="7a94a-200">Tuto zprávu můžete určit, že `From` adresa není správná nebo chybí.</span><span class="sxs-lookup"><span data-stu-id="7a94a-200">This message can indicate that the `From` address is not correct or is missing.</span></span>
- <span data-ttu-id="7a94a-201">*Zadaný řetězec není ve formátu vyžadovaném pro e-mailovou adresu.*</span><span class="sxs-lookup"><span data-stu-id="7a94a-201">*The specified string is not in the form required for an email address.*</span></span>

    <span data-ttu-id="7a94a-202">Tato chyba může znamenat, která hodnota `To` nebo `From` vlastnosti nerozeznal jako e-mailové adresy.</span><span class="sxs-lookup"><span data-stu-id="7a94a-202">This error might indicate that the value of the `To` or `From` properties are not recognized as email addresses.</span></span> <span data-ttu-id="7a94a-203">(Technologie ASP.NET nemůže zkontrolovat, že je platný, pouze že se je ve správném formátu, jako je třeba e-mailovou adresu *name@domain.com*.)</span><span class="sxs-lookup"><span data-stu-id="7a94a-203">(ASP.NET cannot check that the email address is valid, only that it's in the correct format, like *name@domain.com*.)</span></span>

> [!NOTE]
> <span data-ttu-id="7a94a-204">Odeberte kód, který se zobrazí chyba (`@errorMessage`) před publikováním na stránce živého webu.</span><span class="sxs-lookup"><span data-stu-id="7a94a-204">Remove the markup that displays the error (`@errorMessage`) before you publish the page to a live site.</span></span> <span data-ttu-id="7a94a-205">Není vhodné umožňuje uživatelům zobrazit chybové zprávy, které jste získali ze serveru.</span><span class="sxs-lookup"><span data-stu-id="7a94a-205">It's not a good idea to let users see error messages that you get from a server.</span></span>


<a id="AdditionalResources"></a>
## <a name="additional-resources"></a><span data-ttu-id="7a94a-206">Další prostředky</span><span class="sxs-lookup"><span data-stu-id="7a94a-206">Additional Resources</span></span>

[<span data-ttu-id="7a94a-207">Webové stránky ASP.NET (Razor) – časté otázky</span><span class="sxs-lookup"><span data-stu-id="7a94a-207">ASP.NET Web Pages (Razor) FAQ</span></span>](https://go.microsoft.com/fwlink/?LinkId=253000)

<span data-ttu-id="7a94a-208">[Služba WebMatrix a webové stránky ASP.NET](https://forums.asp.net/1224.aspx/1?WebMatrix) fórum na webu ASP.NET</span><span class="sxs-lookup"><span data-stu-id="7a94a-208">[WebMatrix and ASP.NET Web Pages](https://forums.asp.net/1224.aspx/1?WebMatrix) forum on the ASP.NET website</span></span>
