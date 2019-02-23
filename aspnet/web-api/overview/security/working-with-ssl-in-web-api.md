---
uid: web-api/overview/security/working-with-ssl-in-web-api
title: Práce s protokolem SSL ve webovém rozhraní API | Dokumentace Microsoftu
author: MikeWasson
description: Ukazuje, jak používat protokol SSL s ASP.NET Web API, včetně používání certifikátů SSL klienta.
ms.author: riande
ms.date: 02/22/2019
ms.assetid: 97f6164f-59cf-45c0-b820-e4aa29b45396
msc.legacyurl: /web-api/overview/security/working-with-ssl-in-web-api
msc.type: authoredcontent
ms.openlocfilehash: 69c0d217f605096d968435c062ee9931f8dff75f
ms.sourcegitcommit: b3894b65e313570e97a2ab78b8addd22f427cac8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2019
ms.locfileid: "56744102"
---
<a name="working-with-ssl-in-web-api"></a><span data-ttu-id="4f360-103">Práce s protokolem SSL ve webovém rozhraní API</span><span class="sxs-lookup"><span data-stu-id="4f360-103">Working with SSL in Web API</span></span>
====================
<span data-ttu-id="4f360-104">podle [Mike Wasson](https://github.com/MikeWasson)</span><span class="sxs-lookup"><span data-stu-id="4f360-104">by [Mike Wasson](https://github.com/MikeWasson)</span></span>

<span data-ttu-id="4f360-105">Několik společných schémat ověřování nejsou zabezpečené přes standardní protokol HTTP.</span><span class="sxs-lookup"><span data-stu-id="4f360-105">Several common authentication schemes are not secure over plain HTTP.</span></span> <span data-ttu-id="4f360-106">Konkrétně se základní ověřování a ověřování pomocí formulářů odesílat nezašifrované přihlašovací údaje.</span><span class="sxs-lookup"><span data-stu-id="4f360-106">In particular, Basic authentication and forms authentication send unencrypted credentials.</span></span> <span data-ttu-id="4f360-107">Zabezpečení, tato schémata ověřování *musí* používat protokol SSL.</span><span class="sxs-lookup"><span data-stu-id="4f360-107">To be secure, these authentication schemes *must* use SSL.</span></span> <span data-ttu-id="4f360-108">Kromě toho certifikáty SSL klienta slouží k ověřování klientů.</span><span class="sxs-lookup"><span data-stu-id="4f360-108">In addition, SSL client certificates can be used to authenticate clients.</span></span>

## <a name="enabling-ssl-on-the-server"></a><span data-ttu-id="4f360-109">Povolení protokolu SSL na serveru</span><span class="sxs-lookup"><span data-stu-id="4f360-109">Enabling SSL on the Server</span></span>

<span data-ttu-id="4f360-110">Nastavení protokolu SSL ve službě IIS 7 nebo novější:</span><span class="sxs-lookup"><span data-stu-id="4f360-110">To set up SSL in IIS 7 or later:</span></span>

- <span data-ttu-id="4f360-111">Vytvořit nebo získat certifikát.</span><span class="sxs-lookup"><span data-stu-id="4f360-111">Create or get a certificate.</span></span> <span data-ttu-id="4f360-112">Pro účely testování můžete vytvořit certifikát podepsaný svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="4f360-112">For testing, you can create a self-signed certificate.</span></span>
- <span data-ttu-id="4f360-113">Přidáte vazbu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="4f360-113">Add an HTTPS binding.</span></span>

<span data-ttu-id="4f360-114">Podrobnosti najdete v tématu [postupy nastavení protokolu SSL ve službě IIS 7](https://www.iis.net/learn/manage/configuring-security/how-to-set-up-ssl-on-iis).</span><span class="sxs-lookup"><span data-stu-id="4f360-114">For details, see [How to Set Up SSL on IIS 7](https://www.iis.net/learn/manage/configuring-security/how-to-set-up-ssl-on-iis).</span></span>

<span data-ttu-id="4f360-115">Pro místní testování, můžete povolit SSL ve službě IIS Express ze sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="4f360-115">For local testing, you can enable SSL in IIS Express from Visual Studio.</span></span> <span data-ttu-id="4f360-116">V okně Vlastnosti nastavte **povolený protokol SSL** k **True**.</span><span class="sxs-lookup"><span data-stu-id="4f360-116">In the Properties window, set **SSL Enabled** to **True**.</span></span> <span data-ttu-id="4f360-117">Poznamenejte si hodnotu **adresa URL protokolu SSL**; pomocí této adresy URL pro testování připojení prostřednictvím protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="4f360-117">Note the value of **SSL URL**; use this URL for testing HTTPS connections.</span></span>

![](working-with-ssl-in-web-api/_static/image1.png)

### <a name="enforcing-ssl-in-a-web-api-controller"></a><span data-ttu-id="4f360-118">Vynucování SSL v Kontroleru webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="4f360-118">Enforcing SSL in a Web API Controller</span></span>

<span data-ttu-id="4f360-119">Pokud máte vazbu HTTP i HTTPS, klienti můžou stále používat HTTP pro přístup k webu.</span><span class="sxs-lookup"><span data-stu-id="4f360-119">If you have both an HTTPS and an HTTP binding, clients can still use HTTP to access the site.</span></span> <span data-ttu-id="4f360-120">Může povolit některé prostředky je k dispozici prostřednictvím protokolu HTTP, zatímco jiné prostředky vyžadují protokol SSL.</span><span class="sxs-lookup"><span data-stu-id="4f360-120">You might allow some resources to be available through HTTP, while other resources require SSL.</span></span> <span data-ttu-id="4f360-121">V takovém případě použijte filtr akce Pokud chcete vyžadovat protokol SSL k chráněným prostředkům.</span><span class="sxs-lookup"><span data-stu-id="4f360-121">In that case, use an action filter to require SSL for the protected resources.</span></span> <span data-ttu-id="4f360-122">Následující kód ukazuje filtr ověřování webové rozhraní API, která kontroluje pro protokol SSL:</span><span class="sxs-lookup"><span data-stu-id="4f360-122">The following code shows a Web API authentication filter that checks for SSL:</span></span>

[!code-csharp[Main](working-with-ssl-in-web-api/samples/sample1.cs)]

<span data-ttu-id="4f360-123">Přidejte tento filtr pro všechny akce webového rozhraní API, které vyžadují protokol SSL:</span><span class="sxs-lookup"><span data-stu-id="4f360-123">Add this filter to any Web API actions that require SSL:</span></span>

[!code-csharp[Main](working-with-ssl-in-web-api/samples/sample2.cs)]

## <a name="ssl-client-certificates"></a><span data-ttu-id="4f360-124">Klientské certifikáty SSL</span><span class="sxs-lookup"><span data-stu-id="4f360-124">SSL Client Certificates</span></span>

<span data-ttu-id="4f360-125">Protokol SSL zajišťuje ověřování pomocí certifikátů infrastruktury veřejných klíčů.</span><span class="sxs-lookup"><span data-stu-id="4f360-125">SSL provides authentication by using Public Key Infrastructure certificates.</span></span> <span data-ttu-id="4f360-126">Na serveru, musíte zadat certifikát, který se ověřuje serveru do klienta.</span><span class="sxs-lookup"><span data-stu-id="4f360-126">The server must provide a certificate that authenticates the server to the client.</span></span> <span data-ttu-id="4f360-127">Je méně běžné pro klienta, jak poskytnout certifikát na server, ale to je jednou z možností pro ověřování klientů.</span><span class="sxs-lookup"><span data-stu-id="4f360-127">It is less common for the client to provide a certificate to the server, but this is one option for authenticating clients.</span></span> <span data-ttu-id="4f360-128">Použití klientských certifikátů pomocí protokolu SSL, potřebujete způsob, jak distribuovat certifikáty podepsané svým uživatelům.</span><span class="sxs-lookup"><span data-stu-id="4f360-128">To use client certificates with SSL, you need a way to distribute signed certificates to your users.</span></span> <span data-ttu-id="4f360-129">Pro mnoho typů aplikací neměl by to být vhodné uživatelské prostředí, ale v některých prostředích (například organizace) může být vhodná.</span><span class="sxs-lookup"><span data-stu-id="4f360-129">For many application types, this will not be a good user experience, but in some environments (for example, enterprise) it may be feasible.</span></span>

| <span data-ttu-id="4f360-130">Výhody</span><span class="sxs-lookup"><span data-stu-id="4f360-130">Advantages</span></span> | <span data-ttu-id="4f360-131">Nevýhody</span><span class="sxs-lookup"><span data-stu-id="4f360-131">Disadvantages</span></span> |
| --- | --- |
| <span data-ttu-id="4f360-132">-Certificate přihlašovací údaje jsou silnější než uživatelského jména a hesla.</span><span class="sxs-lookup"><span data-stu-id="4f360-132">- Certificate credentials are stronger than username/password.</span></span> <span data-ttu-id="4f360-133">-SSL poskytuje kompletní zabezpečený kanál s ověřováním, zprávu, integritu a šifrování zpráv.</span><span class="sxs-lookup"><span data-stu-id="4f360-133">- SSL provides a complete secure channel, with authentication, message integrity, and message encryption.</span></span> | <span data-ttu-id="4f360-134">-Je nutné získat a správu certifikátů infrastruktury veřejných KLÍČŮ.</span><span class="sxs-lookup"><span data-stu-id="4f360-134">- You must obtain and manage PKI certificates.</span></span> <span data-ttu-id="4f360-135">-Klientská platforma musí podporovat klientské certifikáty SSL.</span><span class="sxs-lookup"><span data-stu-id="4f360-135">- The client platform must support SSL client certificates.</span></span> |

<span data-ttu-id="4f360-136">Pokud chcete nakonfigurovat službu IIS tak, aby přijímal klientské certifikáty, otevřete Správce služby IIS a proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="4f360-136">To configure IIS to accept client certificates, open IIS Manager and perform the following steps:</span></span>

1. <span data-ttu-id="4f360-137">Klikněte na uzel serveru ve stromovém zobrazení.</span><span class="sxs-lookup"><span data-stu-id="4f360-137">Click the site node in the tree view.</span></span>
2. <span data-ttu-id="4f360-138">Dvakrát klikněte **nastavení SSL** funkce v prostředním podokně.</span><span class="sxs-lookup"><span data-stu-id="4f360-138">Double-click the **SSL Settings** feature in the middle pane.</span></span>
3. <span data-ttu-id="4f360-139">V části **klientské certifikáty**, vyberte jednu z těchto možností:</span><span class="sxs-lookup"><span data-stu-id="4f360-139">Under **Client Certificates**, select one of these options:</span></span> 

    - <span data-ttu-id="4f360-140">**Přijměte**: Služba IIS bude přijímat certifikát od klienta, ale nevyžaduje, aby jeden.</span><span class="sxs-lookup"><span data-stu-id="4f360-140">**Accept**: IIS will accept a certificate from the client, but does not require one.</span></span>
    - <span data-ttu-id="4f360-141">**Vyžadovat**: V nástroji vyžadují certifikát klienta.</span><span class="sxs-lookup"><span data-stu-id="4f360-141">**Require**: Require a client certificate.</span></span> <span data-ttu-id="4f360-142">(Chcete-li povolit tuto možnost, musíte také vybrat "Požadovat protokol SSL")</span><span class="sxs-lookup"><span data-stu-id="4f360-142">(To enable this option, you must also select "Require SSL")</span></span>

<span data-ttu-id="4f360-143">V souboru ApplicationHost.config můžete také nastavit tyto možnosti:</span><span class="sxs-lookup"><span data-stu-id="4f360-143">You can also set these options in the ApplicationHost.config file:</span></span>

[!code-xml[Main](working-with-ssl-in-web-api/samples/sample3.xml)]

<span data-ttu-id="4f360-144">**SslNegotiateCert** příznak znamená, že služba IIS bude přijímat certifikát od klienta, ale nevyžaduje, aby jeden (odpovídá možnosti "Přijmout" ve Správci služby IIS).</span><span class="sxs-lookup"><span data-stu-id="4f360-144">The **SslNegotiateCert** flag means IIS will accept a certificate from the client, but does not require one (equivalent to the "Accept" option in IIS Manager).</span></span> <span data-ttu-id="4f360-145">Chcete-li v nástroji vyžadují certifikát, nastavte **SslRequireCert** příznak.</span><span class="sxs-lookup"><span data-stu-id="4f360-145">To require a certificate, set the **SslRequireCert** flag.</span></span> <span data-ttu-id="4f360-146">Pro účely testování můžete také nastavit tyto možnosti v rámci služby IIS Express, v místní hostitele aplikace. Konfigurační soubor umístěný ve "Documents\IISExpress\config".</span><span class="sxs-lookup"><span data-stu-id="4f360-146">For testing, you can also set these options in IIS Express, in the local applicationhost.Config file, located in "Documents\IISExpress\config".</span></span>

### <a name="creating-a-client-certificate-for-testing"></a><span data-ttu-id="4f360-147">Vytváří se klientský certifikát pro účely testování</span><span class="sxs-lookup"><span data-stu-id="4f360-147">Creating a Client Certificate for Testing</span></span>

<span data-ttu-id="4f360-148">Pro účely testování můžete použít [MakeCert.exe](/windows/desktop/SecCrypto/makecert) k vytvoření klientského certifikátu.</span><span class="sxs-lookup"><span data-stu-id="4f360-148">For testing purposes, you can use [MakeCert.exe](/windows/desktop/SecCrypto/makecert) to create a client certificate.</span></span> <span data-ttu-id="4f360-149">Nejprve vytvořte testovací kořenové autority:</span><span class="sxs-lookup"><span data-stu-id="4f360-149">First, create a test root authority:</span></span>

[!code-console[Main](working-with-ssl-in-web-api/samples/sample4.cmd)]

<span data-ttu-id="4f360-150">Použití nástroje MakeCert vás vyzve k zadání hesla privátního klíče.</span><span class="sxs-lookup"><span data-stu-id="4f360-150">Makecert will prompt you to enter a password for the private key.</span></span>

<span data-ttu-id="4f360-151">V dalším kroku přidejte certifikát do testu, které ukládají serveru "důvěryhodné kořenové certifikační autority", následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="4f360-151">Next, add the certificate to the test server's "Trusted Root Certification Authorities" store, as follows:</span></span>

1. <span data-ttu-id="4f360-152">Otevřete konzolu MMC.</span><span class="sxs-lookup"><span data-stu-id="4f360-152">Open MMC.</span></span>
2. <span data-ttu-id="4f360-153">V části **souboru**vyberte **Přidat/odebrat modul Snap-In**.</span><span class="sxs-lookup"><span data-stu-id="4f360-153">Under **File**, select **Add/Remove Snap-In**.</span></span>
3. <span data-ttu-id="4f360-154">Vyberte **účet počítače**.</span><span class="sxs-lookup"><span data-stu-id="4f360-154">Select **Computer Account**.</span></span>
4. <span data-ttu-id="4f360-155">Vyberte **místního počítače** a dokončete průvodce.</span><span class="sxs-lookup"><span data-stu-id="4f360-155">Select **Local computer** and complete the wizard.</span></span>
5. <span data-ttu-id="4f360-156">V navigačním podokně rozbalte uzel "Důvěryhodné kořenové certifikační autority".</span><span class="sxs-lookup"><span data-stu-id="4f360-156">Under the navigation pane, expand the "Trusted Root Certification Authorities" node.</span></span>
6. <span data-ttu-id="4f360-157">Na **akce** nabídky, přejděte k **všechny úkoly**a potom klikněte na tlačítko **Import** spusťte Průvodce importem certifikátu.</span><span class="sxs-lookup"><span data-stu-id="4f360-157">On the **Action** menu, point to **All Tasks**, and then click **Import** to start the Certificate Import Wizard.</span></span>
7. <span data-ttu-id="4f360-158">Přejděte do souboru certifikátu, TempCA.cer.</span><span class="sxs-lookup"><span data-stu-id="4f360-158">Browse to the certificate file, TempCA.cer.</span></span>
8. <span data-ttu-id="4f360-159">Klikněte na tlačítko **otevřít**, pak klikněte na tlačítko **Další** a dokončete průvodce.</span><span class="sxs-lookup"><span data-stu-id="4f360-159">Click **Open**, then click **Next** and complete the wizard.</span></span> <span data-ttu-id="4f360-160">(Budete vyzváni znovu zadat heslo.)</span><span class="sxs-lookup"><span data-stu-id="4f360-160">(You will be prompted to re-enter the password.)</span></span>

<span data-ttu-id="4f360-161">Teď vytvořte klientský certifikát, který je podepsaný první certifikát:</span><span class="sxs-lookup"><span data-stu-id="4f360-161">Now create a client certificate that is signed by the first certificate:</span></span>

[!code-console[Main](working-with-ssl-in-web-api/samples/sample5.cmd)]

### <a name="using-client-certificates-in-web-api"></a><span data-ttu-id="4f360-162">Pomocí klientských certifikátů ve webovém rozhraní API</span><span class="sxs-lookup"><span data-stu-id="4f360-162">Using Client Certificates in Web API</span></span>

<span data-ttu-id="4f360-163">Na straně serveru, můžete získat certifikát klienta voláním [GetClientCertificate](https://msdn.microsoft.com/library/system.net.http.httprequestmessageextensions.getclientcertificate.aspx) na zprávy s požadavkem.</span><span class="sxs-lookup"><span data-stu-id="4f360-163">On the server side, you can get the client certificate by calling [GetClientCertificate](https://msdn.microsoft.com/library/system.net.http.httprequestmessageextensions.getclientcertificate.aspx) on the request message.</span></span> <span data-ttu-id="4f360-164">Metoda vrátí hodnotu null, pokud neexistuje žádný certifikát klienta.</span><span class="sxs-lookup"><span data-stu-id="4f360-164">The method returns null if there is no client certificate.</span></span> <span data-ttu-id="4f360-165">V opačném případě vrátí **X509Certificate2** instance.</span><span class="sxs-lookup"><span data-stu-id="4f360-165">Otherwise, it returns an **X509Certificate2** instance.</span></span> <span data-ttu-id="4f360-166">Pomocí tohoto objektu můžete získat informace z certifikátu, jako je vydavatel a předmět.</span><span class="sxs-lookup"><span data-stu-id="4f360-166">Use this object to get information from the certificate, such as the issuer and subject.</span></span> <span data-ttu-id="4f360-167">Tyto informace pak můžete použít pro ověřování a autorizace.</span><span class="sxs-lookup"><span data-stu-id="4f360-167">Then you can use this information for authentication and/or authorization.</span></span>

[!code-csharp[Main](working-with-ssl-in-web-api/samples/sample6.cs)]
