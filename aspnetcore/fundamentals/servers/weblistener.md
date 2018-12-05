---
title: Implementace serveru WebListener web v ASP.NET Core
author: guardrex
description: Další informace o WebListener, webový server pro ASP.NET Core ve Windows, který lze použít pro přímé připojení k Internetu bez služby IIS.
monikerRange: < aspnetcore-2.0
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: fundamentals/servers/weblistener
ms.openlocfilehash: 92a2e567e968cce59ba7b6f374ebd4bc189b81ee
ms.sourcegitcommit: 9bb58d7c8dad4bbd03419bcc183d027667fefa20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52862119"
---
# <a name="weblistener-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="24510-103">Implementace serveru WebListener web v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="24510-103">WebListener web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="24510-104">Podle [Petr Dykstra](https://github.com/tdykstra) a [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="24510-104">By [Tom Dykstra](https://github.com/tdykstra) and [Chris Ross](https://github.com/Tratcher)</span></span>

> [!NOTE]
> <span data-ttu-id="24510-105">Toto téma platí pouze pro ASP.NET Core 1.x.</span><span class="sxs-lookup"><span data-stu-id="24510-105">This topic applies only to ASP.NET Core 1.x.</span></span> <span data-ttu-id="24510-106">V ASP.NET Core 2.0, je název WebListener [HTTP.sys](httpsys.md).</span><span class="sxs-lookup"><span data-stu-id="24510-106">In ASP.NET Core 2.0, WebListener is named [HTTP.sys](httpsys.md).</span></span>

<span data-ttu-id="24510-107">Je WebListener [webového serveru pro ASP.NET Core](index.md) , který poběží pouze na Windows.</span><span class="sxs-lookup"><span data-stu-id="24510-107">WebListener is a [web server for ASP.NET Core](index.md) that runs only on Windows.</span></span> <span data-ttu-id="24510-108">Orchard je založen na [ovladač režimu jádra Http.Sys](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx).</span><span class="sxs-lookup"><span data-stu-id="24510-108">It's built on the [Http.Sys kernel mode driver](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx).</span></span> <span data-ttu-id="24510-109">WebListener představuje alternativu k [Kestrel](kestrel.md) , který lze použít pro přímé připojení k Internetu bez nutnosti spoléhat se ve službě IIS jako reverzní proxy server.</span><span class="sxs-lookup"><span data-stu-id="24510-109">WebListener is an alternative to [Kestrel](kestrel.md) that can be used for direct connection to the Internet without relying on IIS as a reverse proxy server.</span></span> <span data-ttu-id="24510-110">Ve skutečnosti **WebListener nejde používat s IIS nebo IIS Express, není kompatibilní se systémem [modul ASP.NET Core](aspnet-core-module.md).**</span><span class="sxs-lookup"><span data-stu-id="24510-110">In fact, **WebListener can't be used with IIS or IIS Express, as it isn't compatible with the [ASP.NET Core Module](aspnet-core-module.md).**</span></span>

<span data-ttu-id="24510-111">I když WebListener vyvinutá pro ASP.NET Core, můžete použít přímo v jakékoli aplikaci .NET Core nebo .NET Framework přes [Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/) balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="24510-111">Although WebListener was developed for ASP.NET Core, it can be used directly in any .NET Core or .NET Framework application via the [Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/) NuGet package.</span></span>

<span data-ttu-id="24510-112">WebListener podporuje následující funkce:</span><span class="sxs-lookup"><span data-stu-id="24510-112">WebListener supports the following features:</span></span>

- [<span data-ttu-id="24510-113">Ověřování Windows</span><span class="sxs-lookup"><span data-stu-id="24510-113">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
- <span data-ttu-id="24510-114">Sdílení portů</span><span class="sxs-lookup"><span data-stu-id="24510-114">Port sharing</span></span>
- <span data-ttu-id="24510-115">Protokol HTTPS se SNI</span><span class="sxs-lookup"><span data-stu-id="24510-115">HTTPS with SNI</span></span>
- <span data-ttu-id="24510-116">HTTP/2 přes protokol TLS (Windows 10)</span><span class="sxs-lookup"><span data-stu-id="24510-116">HTTP/2 over TLS (Windows 10)</span></span>
- <span data-ttu-id="24510-117">Přenos souborů s přímým přístupem</span><span class="sxs-lookup"><span data-stu-id="24510-117">Direct file transmission</span></span>
- <span data-ttu-id="24510-118">Ukládání odpovědí do mezipaměti</span><span class="sxs-lookup"><span data-stu-id="24510-118">Response caching</span></span>
- <span data-ttu-id="24510-119">Protokoly Websocket (Windows 8)</span><span class="sxs-lookup"><span data-stu-id="24510-119">WebSockets (Windows 8)</span></span>

<span data-ttu-id="24510-120">Podporované verze Windows:</span><span class="sxs-lookup"><span data-stu-id="24510-120">Supported Windows versions:</span></span>

- <span data-ttu-id="24510-121">Windows 7 a Windows Server 2008 R2 a novější</span><span class="sxs-lookup"><span data-stu-id="24510-121">Windows 7 and Windows Server 2008 R2 and later</span></span>

<span data-ttu-id="24510-122">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/weblistener/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="24510-122">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/weblistener/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-weblistener"></a><span data-ttu-id="24510-123">Kdy použít WebListener</span><span class="sxs-lookup"><span data-stu-id="24510-123">When to use WebListener</span></span>

<span data-ttu-id="24510-124">WebListener je užitečné pro nasazení, kde je nutné vystavit server přímo k Internetu bez použití služby IIS.</span><span class="sxs-lookup"><span data-stu-id="24510-124">WebListener is useful for deployments where you need to expose the server directly to the Internet without using IIS.</span></span>

![Weblistener komunikuje přímo s Internetem](weblistener/_static/weblistener-to-internet.png)

<span data-ttu-id="24510-126">Protože je založený na souboru Http.Sys, nevyžaduje WebListener reverzní proxy server pro ochranu před útoky.</span><span class="sxs-lookup"><span data-stu-id="24510-126">Because it's built on Http.Sys, WebListener doesn't require a reverse proxy server for protection against attacks.</span></span> <span data-ttu-id="24510-127">Ovladač Http.Sys je Vyspělá technologie, která chrání proti mnoha typů útoků a poskytuje odolnost, zabezpečení a škálovatelnost plně funkční webového serveru.</span><span class="sxs-lookup"><span data-stu-id="24510-127">Http.Sys is mature technology that protects against many kinds of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="24510-128">Služba IIS pracuje jako naslouchací proces protokolu HTTP na základě ovladače Http.Sys.</span><span class="sxs-lookup"><span data-stu-id="24510-128">IIS itself runs as an HTTP listener on top of Http.Sys.</span></span>

<span data-ttu-id="24510-129">WebListener je také vhodný pro interní nasazení v případě potřeby funkcí, které nabízí, nelze získat pomocí Kestrel.</span><span class="sxs-lookup"><span data-stu-id="24510-129">WebListener is also a good choice for internal deployments when you need one of the features it offers that you can't get by using Kestrel.</span></span>

![Weblistener komunikuje přímo s vaší interní sítě](weblistener/_static/weblistener-to-internal.png)

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="24510-131">Ověřování pomocí protokolu Kerberos v režimu jádra</span><span class="sxs-lookup"><span data-stu-id="24510-131">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="24510-132">WebListener delegáty pro ověřování v režimu jádra ověřování protokolem Kerberos.</span><span class="sxs-lookup"><span data-stu-id="24510-132">WebListener delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="24510-133">Režim ověřování uživatele nepodporuje protokolů Kerberos a WebListener.</span><span class="sxs-lookup"><span data-stu-id="24510-133">User mode authentication isn't supported with Kerberos and WebListener.</span></span> <span data-ttu-id="24510-134">Účet počítače musí být použité k dešifrování token/lístek služby Kerberos, která se získá z Active Directory a předá klienta na serveru k ověření uživatele.</span><span class="sxs-lookup"><span data-stu-id="24510-134">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="24510-135">Zaregistrujte hlavní název služby (SPN) příslušného hostitele není uživatel aplikace.</span><span class="sxs-lookup"><span data-stu-id="24510-135">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-weblistener"></a><span data-ttu-id="24510-136">Jak používat WebListener</span><span class="sxs-lookup"><span data-stu-id="24510-136">How to use WebListener</span></span>

<span data-ttu-id="24510-137">Tady je přehled úloh její úvodního nastavení pro hostitelský operační systém a aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="24510-137">Here's an overview of setup tasks for the host OS and your ASP.NET Core application.</span></span>

### <a name="configure-windows-server"></a><span data-ttu-id="24510-138">Konfigurace Windows serveru</span><span class="sxs-lookup"><span data-stu-id="24510-138">Configure Windows Server</span></span>

* <span data-ttu-id="24510-139">Nainstalujte verzi rozhraní .NET, které aplikace potřebuje, jako například [.NET Core](https://download.microsoft.com/download/0/A/3/0A372822-205D-4A86-BFA7-084D2CBE9EDF/DotNetCore.1.0.1-SDK.1.0.0.Preview2-003133-x64.exe) nebo rozhraní .NET Framework 4.5.1.</span><span class="sxs-lookup"><span data-stu-id="24510-139">Install the version of .NET that your application requires, such as [.NET Core](https://download.microsoft.com/download/0/A/3/0A372822-205D-4A86-BFA7-084D2CBE9EDF/DotNetCore.1.0.1-SDK.1.0.0.Preview2-003133-x64.exe) or .NET Framework 4.5.1.</span></span>

* <span data-ttu-id="24510-140">Preregister předpony adres URL k vytvoření vazby k WebListener a nastavení certifikátů SSL</span><span class="sxs-lookup"><span data-stu-id="24510-140">Preregister URL prefixes to bind to WebListener, and set up SSL certificates</span></span>

   <span data-ttu-id="24510-141">Pokud není preregister předpony adres URL ve Windows, musíte spustit aplikaci s oprávněním správce.</span><span class="sxs-lookup"><span data-stu-id="24510-141">If you don't preregister URL prefixes in Windows, you have to run your application with administrator privileges.</span></span> <span data-ttu-id="24510-142">Jedinou výjimkou je, pokud lze vázat na místního hostitele pomocí více než 1 024; číslo portu HTTP (nikoli HTTPS) v takovém případě nejsou vyžaduje oprávnění správce.</span><span class="sxs-lookup"><span data-stu-id="24510-142">The only exception is if you bind to localhost using HTTP (not HTTPS) with a port number greater than 1024; in that case administrator privileges aren't required.</span></span>

   <span data-ttu-id="24510-143">Podrobnosti najdete v tématu [preregister předpony a konfigurace protokolu SSL](#preregister-url-prefixes-and-configure-ssl) dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="24510-143">For details, see [How to preregister prefixes and configure SSL](#preregister-url-prefixes-and-configure-ssl) later in this article.</span></span>

* <span data-ttu-id="24510-144">Otevřete porty brány firewall pro povolení provozu k dosažení WebListener.</span><span class="sxs-lookup"><span data-stu-id="24510-144">Open firewall ports to allow traffic to reach WebListener.</span></span>

   <span data-ttu-id="24510-145">Můžete použít netsh.exe nebo [rutin prostředí PowerShell](https://technet.microsoft.com/library/jj554906).</span><span class="sxs-lookup"><span data-stu-id="24510-145">You can use netsh.exe or [PowerShell cmdlets](https://technet.microsoft.com/library/jj554906).</span></span>

<span data-ttu-id="24510-146">Existují také [nastavení registru Http.Sys](https://support.microsoft.com/kb/820129).</span><span class="sxs-lookup"><span data-stu-id="24510-146">There are also [Http.Sys registry settings](https://support.microsoft.com/kb/820129).</span></span>

### <a name="configure-your-aspnet-core-application"></a><span data-ttu-id="24510-147">Konfigurace aplikace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="24510-147">Configure your ASP.NET Core application</span></span>

* <span data-ttu-id="24510-148">Nainstalujte balíček NuGet [Microsoft.AspNetCore.Server.WebListener](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.WebListener/).</span><span class="sxs-lookup"><span data-stu-id="24510-148">Install the NuGet package [Microsoft.AspNetCore.Server.WebListener](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.WebListener/).</span></span> <span data-ttu-id="24510-149">Tím se nainstaluje taky [Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/) jako závislost.</span><span class="sxs-lookup"><span data-stu-id="24510-149">This also installs [Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/) as a dependency.</span></span>

* <span data-ttu-id="24510-150">Volání `UseWebListener` rozšiřující metody na [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) ve vašich `Main` metoda zadání jakékoli WebListener [možnosti](https://github.com/aspnet/HttpSysServer/blob/rel/1.1.2/src/Microsoft.AspNetCore.Server.WebListener/WebListenerOptions.cs) a [nastavení](https://github.com/aspnet/HttpSysServer/blob/rel/1.1.2/src/Microsoft.Net.Http.Server/WebListenerSettings.cs) , které potřebujete , jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="24510-150">Call the `UseWebListener` extension method on [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) in your `Main` method, specifying any WebListener [options](https://github.com/aspnet/HttpSysServer/blob/rel/1.1.2/src/Microsoft.AspNetCore.Server.WebListener/WebListenerOptions.cs) and [settings](https://github.com/aspnet/HttpSysServer/blob/rel/1.1.2/src/Microsoft.Net.Http.Server/WebListenerSettings.cs) that you need, as shown in the following example:</span></span>

  [!code-csharp[](weblistener/sample/Program.cs?name=snippet_Main&highlight=13-17)]

* <span data-ttu-id="24510-151">Konfigurovat adresy URL a porty pro naslouchání</span><span class="sxs-lookup"><span data-stu-id="24510-151">Configure URLs and ports to listen on</span></span> 

  <span data-ttu-id="24510-152">Ve výchozím nastavení, ASP.NET Core váže k `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="24510-152">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="24510-153">Konfigurace předpony adres URL a portů, můžete použít `UseURLs` metody rozšíření `urls` argument příkazového řádku nebo systém konfigurace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="24510-153">To configure URL prefixes and ports, you can use the `UseURLs` extension method, the `urls` command-line argument or the ASP.NET Core configuration system.</span></span> <span data-ttu-id="24510-154">Další informace najdete v tématu [hostitele v ASP.NET Core](xref:fundamentals/host/index).</span><span class="sxs-lookup"><span data-stu-id="24510-154">For more information, see [Host in ASP.NET Core](xref:fundamentals/host/index).</span></span>

  <span data-ttu-id="24510-155">Web používá naslouchací proces [formáty řetězců předponu Http.Sys](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).</span><span class="sxs-lookup"><span data-stu-id="24510-155">Web Listener uses the [Http.Sys prefix string formats](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).</span></span> <span data-ttu-id="24510-156">Nejsou žádné požadavky formátu řetězec předpony, které jsou specifické pro WebListener.</span><span class="sxs-lookup"><span data-stu-id="24510-156">There are no prefix string format requirements that are specific to WebListener.</span></span>

  > [!WARNING]
  > <span data-ttu-id="24510-157">Vazby nejvyšší úrovně zástupný znak (`http://*:80/` a `http://+:80`) by měl **není** použít.</span><span class="sxs-lookup"><span data-stu-id="24510-157">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="24510-158">Vazby nejvyšší úrovně zástupný znak můžete otevřít aplikaci tak, aby slabá místa zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="24510-158">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="24510-159">To platí pro silné a slabé zástupné znaky.</span><span class="sxs-lookup"><span data-stu-id="24510-159">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="24510-160">Používejte explicitní hostitele názvy místo zástupných znaků.</span><span class="sxs-lookup"><span data-stu-id="24510-160">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="24510-161">Vazby zástupný znak subdoménu (například `*.mysub.com`) nemá toto bezpečnostní riziko, pokud celé nadřazené domény (nikoli `*.com`, což je ohrožené).</span><span class="sxs-lookup"><span data-stu-id="24510-161">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="24510-162">Zobrazit [rfc7230 části-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) Další informace.</span><span class="sxs-lookup"><span data-stu-id="24510-162">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

  > [!NOTE]
  > <span data-ttu-id="24510-163">Je nutné zadat stejnou předponu řetězce v `UseUrls` , který preregister na serveru.</span><span class="sxs-lookup"><span data-stu-id="24510-163">Make sure that you specify the same prefix strings in `UseUrls` that you preregister on the server.</span></span> 

* <span data-ttu-id="24510-164">Zajistěte, aby že vaše aplikace není nakonfigurovaná pro spuštění služby IIS nebo IIS Express.</span><span class="sxs-lookup"><span data-stu-id="24510-164">Make sure your application isn't configured to run IIS or IIS Express.</span></span>

  <span data-ttu-id="24510-165">V sadě Visual Studio je výchozí profil spuštění pro službu IIS Express.</span><span class="sxs-lookup"><span data-stu-id="24510-165">In Visual Studio, the default launch profile is for IIS Express.</span></span>  <span data-ttu-id="24510-166">Spustit projekt jako konzolovou aplikaci budete muset ručně změnit vybraný profil, jak je znázorněno na následujícím snímku obrazovky.</span><span class="sxs-lookup"><span data-stu-id="24510-166">To run the project as a console application you have to manually change the selected profile, as shown in the following screen shot.</span></span>

  ![Vyberte profil aplikace konzoly](weblistener/_static/vs-choose-profile.png)

## <a name="how-to-use-weblistener-outside-of-aspnet-core"></a><span data-ttu-id="24510-168">Jak používat WebListener mimo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="24510-168">How to use WebListener outside of ASP.NET Core</span></span>

* <span data-ttu-id="24510-169">Nainstalujte [Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/) balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="24510-169">Install the [Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/) NuGet package.</span></span>

* <span data-ttu-id="24510-170">[Předpony adres URL k vytvoření vazby k WebListener a nastavení certifikátů SSL preregister](#preregister-url-prefixes-and-configure-ssl) stejně jako pro použití v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="24510-170">[Preregister URL prefixes to bind to WebListener, and set up SSL certificates](#preregister-url-prefixes-and-configure-ssl) as you would for use in ASP.NET Core.</span></span>

<span data-ttu-id="24510-171">Existují také [nastavení registru Http.Sys](https://support.microsoft.com/kb/820129).</span><span class="sxs-lookup"><span data-stu-id="24510-171">There are also [Http.Sys registry settings](https://support.microsoft.com/kb/820129).</span></span>


<span data-ttu-id="24510-172">Tady je ukázka kódu, který ukazuje použití WebListener mimo ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="24510-172">Here's a code sample that demonstrates WebListener use outside of ASP.NET Core:</span></span>

```csharp
var settings = new WebListenerSettings();
settings.UrlPrefixes.Add("http://localhost:8080");

using (WebListener listener = new WebListener(settings))
{
    listener.Start();

    while (true)
    {
        var context = await listener.AcceptAsync();
        byte[] bytes = Encoding.ASCII.GetBytes("Hello World: " + DateTime.Now);
        context.Response.ContentLength = bytes.Length;
        context.Response.ContentType = "text/plain";

        await context.Response.Body.WriteAsync(bytes, 0, bytes.Length);
        context.Dispose();
    }
}
```

## <a name="preregister-url-prefixes-and-configure-ssl"></a><span data-ttu-id="24510-173">Preregister předpony adres URL a konfigurovat jím protokol SSL</span><span class="sxs-lookup"><span data-stu-id="24510-173">Preregister URL prefixes and configure SSL</span></span>

<span data-ttu-id="24510-174">Služba IIS a WebListener Spolehněte se na základní ovladač režimu jádra Http.Sys naslouchat požadavkům a počáteční zpracování.</span><span class="sxs-lookup"><span data-stu-id="24510-174">Both IIS and WebListener rely on the underlying Http.Sys kernel mode driver to listen for requests and do initial processing.</span></span> <span data-ttu-id="24510-175">Ve službě IIS uživatelské rozhraní pro správu poskytuje relativně jednoduché je způsob, jak nakonfigurovat vše, co.</span><span class="sxs-lookup"><span data-stu-id="24510-175">In IIS, the management UI gives you a relatively easy way to configure everything.</span></span> <span data-ttu-id="24510-176">Ale pokud používáte WebListener musíte nakonfigurovat ovladač Http.Sys sami.</span><span class="sxs-lookup"><span data-stu-id="24510-176">However, if you're using WebListener you need to configure Http.Sys yourself.</span></span> <span data-ttu-id="24510-177">Integrované nástroje pro to, který je netsh.exe.</span><span class="sxs-lookup"><span data-stu-id="24510-177">The built-in tool for doing that's netsh.exe.</span></span> 

<span data-ttu-id="24510-178">Budete muset použít netsh.exe pro nejběžnější úkoly jsou rezervace předpony adres URL a přiřazováním certifikátů SSL.</span><span class="sxs-lookup"><span data-stu-id="24510-178">The most common tasks you need to use netsh.exe for are reserving URL prefixes and assigning SSL certificates.</span></span>

<span data-ttu-id="24510-179">NetSh.exe není jednoduché nástroje pro použití pro začátečníky.</span><span class="sxs-lookup"><span data-stu-id="24510-179">NetSh.exe isn't an easy tool to use for beginners.</span></span> <span data-ttu-id="24510-180">Následující příklad ukazuje holých minimální počet potřebný pro rezervaci předpony adres URL pro porty 80 a 443:</span><span class="sxs-lookup"><span data-stu-id="24510-180">The following example shows the bare minimum needed to reserve URL prefixes for ports 80 and 443:</span></span>

```console
netsh http add urlacl url=http://+:80/ user=Users
netsh http add urlacl url=https://+:443/ user=Users
```

<span data-ttu-id="24510-181">Následující příklad ukazuje, jak přiřadit certifikát SSL:</span><span class="sxs-lookup"><span data-stu-id="24510-181">The following example shows how to assign an SSL certificate:</span></span>

```console
netsh http add sslcert ipport=0.0.0.0:443 certhash=MyCertHash_Here appid="{00000000-0000-0000-0000-000000000000}".
```

<span data-ttu-id="24510-182">Toto je oficiální dokumentaci:</span><span class="sxs-lookup"><span data-stu-id="24510-182">Here is the official reference documentation:</span></span>

* [<span data-ttu-id="24510-183">Příkazy Netsh pro Hypertext Transfer Protocol (HTTP)</span><span class="sxs-lookup"><span data-stu-id="24510-183">Netsh Commands for Hypertext Transfer Protocol (HTTP)</span></span>](https://technet.microsoft.com/library/cc725882.aspx)
* [<span data-ttu-id="24510-184">UrlPrefix řetězce</span><span class="sxs-lookup"><span data-stu-id="24510-184">UrlPrefix Strings</span></span>](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

<span data-ttu-id="24510-185">Následující zdroje poskytují podrobné pokyny pro několik scénářů.</span><span class="sxs-lookup"><span data-stu-id="24510-185">The following resources provide detailed instructions for several scenarios.</span></span> <span data-ttu-id="24510-186">Články, které odkazují na `HttpListener` platí jak pro `WebListener`, jako jsou také založené na souboru Http.Sys.</span><span class="sxs-lookup"><span data-stu-id="24510-186">Articles that refer to `HttpListener` apply equally to `WebListener`, as both are based on Http.Sys.</span></span>

* [<span data-ttu-id="24510-187">Postupy: Konfigurace portu s certifikátem SSL</span><span class="sxs-lookup"><span data-stu-id="24510-187">How to: Configure a Port with an SSL Certificate</span></span>](/dotnet/framework/wcf/feature-details/how-to-configure-a-port-with-an-ssl-certificate)
* <span data-ttu-id="24510-188">[Hosting a certifikát klienta na základě komunikaci přes protokol HTTPS - HttpListener](http://sunshaking.blogspot.com/2012/11/https-communication-httplistener-based.html) to je blogu třetích stran a je poměrně starý, ale stále obsahuje užitečné informace.</span><span class="sxs-lookup"><span data-stu-id="24510-188">[HTTPS Communication - HttpListener based Hosting and Client Certification](http://sunshaking.blogspot.com/2012/11/https-communication-httplistener-based.html) This is a third-party blog and is fairly old but still has useful information.</span></span>
* <span data-ttu-id="24510-189">[Postupy: Použití HttpListener návodu nebo Http Server nespravovaný kód (C++) jako jednoduchý serveru SSL](https://blogs.msdn.microsoft.com/jpsanders/2009/09/29/how-to-walkthrough-using-httplistener-or-http-server-unmanaged-code-c-as-an-ssl-simple-server/) toto chování je starší blog s užitečnými informacemi.</span><span class="sxs-lookup"><span data-stu-id="24510-189">[How To: Walkthrough Using HttpListener or Http Server unmanaged code (C++) as an SSL Simple Server](https://blogs.msdn.microsoft.com/jpsanders/2009/09/29/how-to-walkthrough-using-httplistener-or-http-server-unmanaged-code-c-as-an-ssl-simple-server/) This too is an older blog with useful information.</span></span>
* [<span data-ttu-id="24510-190">Jak můžu nastavit WebListener .NET Core s protokolem SSL?</span><span class="sxs-lookup"><span data-stu-id="24510-190">How Do I Set Up A .NET Core WebListener With SSL?</span></span>](https://blogs.msdn.microsoft.com/timomta/2016/11/04/how-do-i-set-up-a-net-core-weblistener-with-ssl/)

<span data-ttu-id="24510-191">Tady jsou některé nástroje třetích stran, které může být jednodušší než netsh.exe příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="24510-191">Here are some third-party tools that can be easier to use than the netsh.exe command line.</span></span> <span data-ttu-id="24510-192">Tyto nejsou poskytované nebo schválené pro společnost Microsoft.</span><span class="sxs-lookup"><span data-stu-id="24510-192">These are not provided by or endorsed by Microsoft.</span></span> <span data-ttu-id="24510-193">Nástroje spustit jako správce ve výchozím nastavení, protože netsh.exe samotné vyžaduje oprávnění správce.</span><span class="sxs-lookup"><span data-stu-id="24510-193">The tools run as administrator by default, since netsh.exe itself requires administrator privileges.</span></span>

* <span data-ttu-id="24510-194">[ovladač HTTP.sys správce](http://httpsysmanager.codeplex.com/) poskytuje uživatelské rozhraní pro výpis a konfigurace certifikátů SSL a možnosti, předpona rezervace a seznamy důvěryhodných certifikátů.</span><span class="sxs-lookup"><span data-stu-id="24510-194">[http.sys Manager](http://httpsysmanager.codeplex.com/) provides UI for listing and configuring SSL certificates and options, prefix reservations, and certificate trust lists.</span></span> 
* <span data-ttu-id="24510-195">[HttpConfig](http://www.stevestechspot.com/ABetterHttpcfg.aspx) umožňuje zobrazit nebo konfigurovat certifikáty SSL a předpony adres URL.</span><span class="sxs-lookup"><span data-stu-id="24510-195">[HttpConfig](http://www.stevestechspot.com/ABetterHttpcfg.aspx) lets you list or configure SSL certificates and URL prefixes.</span></span> <span data-ttu-id="24510-196">Uživatelské rozhraní je přesnější než http.sys správce a zveřejňuje několik dalších možností konfigurace, ale jinak poskytuje podobné funkce.</span><span class="sxs-lookup"><span data-stu-id="24510-196">The UI is more refined than http.sys Manager and exposes a few more configuration options, but otherwise it provides similar functionality.</span></span> <span data-ttu-id="24510-197">Nelze vytvořit nový seznam důvěryhodných certifikátů (CTL), ale můžete přiřaďte existující značky.</span><span class="sxs-lookup"><span data-stu-id="24510-197">It cannot create a new certificate trust list (CTL), but can assign existing ones.</span></span>

<span data-ttu-id="24510-198">Pro generování certifikátů SSL podepsaných svým držitelem, společnost Microsoft poskytuje nástroje příkazového řádku: [MakeCert.exe](https://msdn.microsoft.com/library/windows/desktop/aa386968) a rutiny prostředí PowerShell [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pki/new-selfsignedcertificate).</span><span class="sxs-lookup"><span data-stu-id="24510-198">For generating self-signed SSL certificates, Microsoft provides command-line tools: [MakeCert.exe](https://msdn.microsoft.com/library/windows/desktop/aa386968) and the PowerShell cmdlet [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pki/new-selfsignedcertificate).</span></span> <span data-ttu-id="24510-199">Existují také uživatelské rozhraní nástroje třetích stran, které bylo snazší pro vygenerování certifikátů SSL podepsaných svým držitelem:</span><span class="sxs-lookup"><span data-stu-id="24510-199">There are also third-party UI tools that make it easier for you to generate self-signed SSL certificates:</span></span>

* [<span data-ttu-id="24510-200">Nástroje SelfCert</span><span class="sxs-lookup"><span data-stu-id="24510-200">SelfCert</span></span>](https://www.pluralsight.com/blog/software-development/selfcert-create-a-self-signed-certificate-interactively-gui-or-programmatically-in-net)
* [<span data-ttu-id="24510-201">Použití nástroje MakeCert uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="24510-201">Makecert UI</span></span>](http://makecertui.codeplex.com/)

## <a name="next-steps"></a><span data-ttu-id="24510-202">Další kroky</span><span class="sxs-lookup"><span data-stu-id="24510-202">Next steps</span></span>

<span data-ttu-id="24510-203">Další informace naleznete v následujících materiálech:</span><span class="sxs-lookup"><span data-stu-id="24510-203">For more information, see the following resources:</span></span>

* [<span data-ttu-id="24510-204">Ukázková aplikace pro účely tohoto článku</span><span class="sxs-lookup"><span data-stu-id="24510-204">Sample app for this article</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/weblistener/sample)
* [<span data-ttu-id="24510-205">WebListener zdrojového kódu</span><span class="sxs-lookup"><span data-stu-id="24510-205">WebListener source code</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="24510-206">Hostování</span><span class="sxs-lookup"><span data-stu-id="24510-206">Hosting</span></span>](xref:fundamentals/host/index)
