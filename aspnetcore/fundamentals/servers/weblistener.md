---
title: Implementace serveru WebListener web v ASP.NET Core
author: guardrex
description: Další informace o WebListener, webový server pro ASP.NET Core ve Windows, který lze použít pro přímé připojení k Internetu bez služby IIS.
monikerRange: < aspnetcore-2.0
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: fundamentals/servers/weblistener
ms.openlocfilehash: eaf76a44bc7750aef94319042e61aa294c6cba35
ms.sourcegitcommit: 09affee3d234cb27ea6fe33bc113b79e68900d22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2018
ms.locfileid: "51191266"
---
# <a name="weblistener-web-server-implementation-in-aspnet-core"></a>Implementace serveru WebListener web v ASP.NET Core

Podle [Petr Dykstra](https://github.com/tdykstra) a [Chris Ross](https://github.com/Tratcher)

> [!NOTE]
> Toto téma platí pouze pro ASP.NET Core 1.x. V ASP.NET Core 2.0, je název WebListener [HTTP.sys](httpsys.md).

Je WebListener [webového serveru pro ASP.NET Core](index.md) , který poběží pouze na Windows. Orchard je založen na [ovladač režimu jádra Http.Sys](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx). WebListener představuje alternativu k [Kestrel](kestrel.md) , který lze použít pro přímé připojení k Internetu bez nutnosti spoléhat se ve službě IIS jako reverzní proxy server. Ve skutečnosti **WebListener nejde používat s IIS nebo IIS Express, není kompatibilní se systémem [modul ASP.NET Core](aspnet-core-module.md).**

I když WebListener vyvinutá pro ASP.NET Core, můžete použít přímo v jakékoli aplikaci .NET Core nebo .NET Framework přes [Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/) balíček NuGet.

WebListener podporuje následující funkce:

- [Ověřování Windows](xref:security/authentication/windowsauth)
- Sdílení portů
- Protokol HTTPS se SNI
- HTTP/2 přes protokol TLS (Windows 10)
- Přenos souborů s přímým přístupem
- Ukládání odpovědí do mezipaměti
- Protokoly Websocket (Windows 8)

Podporované verze Windows:

- Windows 7 a Windows Server 2008 R2 a novější

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/weblistener/sample) ([stažení](xref:index#how-to-download-a-sample))

## <a name="when-to-use-weblistener"></a>Kdy použít WebListener

WebListener je užitečné pro nasazení, kde je nutné vystavit server přímo k Internetu bez použití služby IIS.

![Weblistener komunikuje přímo s Internetem](weblistener/_static/weblistener-to-internet.png)

Protože je založený na souboru Http.Sys, nevyžaduje WebListener reverzní proxy server pro ochranu před útoky. Ovladač Http.Sys je Vyspělá technologie, která chrání proti mnoha typů útoků a poskytuje odolnost, zabezpečení a škálovatelnost plně funkční webového serveru. Služba IIS pracuje jako naslouchací proces protokolu HTTP na základě ovladače Http.Sys.

WebListener je také vhodný pro interní nasazení v případě potřeby funkcí, které nabízí, nelze získat pomocí Kestrel.

![Weblistener komunikuje přímo s vaší interní sítě](weblistener/_static/weblistener-to-internal.png)

## <a name="kernel-mode-authentication-with-kerberos"></a>Ověřování pomocí protokolu Kerberos v režimu jádra

WebListener delegáty pro ověřování v režimu jádra ověřování protokolem Kerberos. Režim ověřování uživatele nepodporuje protokolů Kerberos a WebListener. Účet počítače musí být použité k dešifrování token/lístek služby Kerberos, která se získá z Active Directory a předá klienta na serveru k ověření uživatele. Zaregistrujte hlavní název služby (SPN) příslušného hostitele není uživatel aplikace.

## <a name="how-to-use-weblistener"></a>Jak používat WebListener

Tady je přehled úloh její úvodního nastavení pro hostitelský operační systém a aplikace ASP.NET Core.

### <a name="configure-windows-server"></a>Konfigurace Windows serveru

* Nainstalujte verzi rozhraní .NET, které aplikace potřebuje, jako například [.NET Core](https://download.microsoft.com/download/0/A/3/0A372822-205D-4A86-BFA7-084D2CBE9EDF/DotNetCore.1.0.1-SDK.1.0.0.Preview2-003133-x64.exe) nebo rozhraní .NET Framework 4.5.1.

* Preregister předpony adres URL k vytvoření vazby k WebListener a nastavení certifikátů SSL

   Pokud není preregister předpony adres URL ve Windows, musíte spustit aplikaci s oprávněním správce. Jedinou výjimkou je, pokud lze vázat na místního hostitele pomocí více než 1 024; číslo portu HTTP (nikoli HTTPS) v takovém případě nejsou vyžaduje oprávnění správce.

   Podrobnosti najdete v tématu [preregister předpony a konfigurace protokolu SSL](#preregister-url-prefixes-and-configure-ssl) dále v tomto článku.

* Otevřete porty brány firewall pro povolení provozu k dosažení WebListener.

   Můžete použít netsh.exe nebo [rutin prostředí PowerShell](https://technet.microsoft.com/library/jj554906).

Existují také [nastavení registru Http.Sys](https://support.microsoft.com/kb/820129).

### <a name="configure-your-aspnet-core-application"></a>Konfigurace aplikace ASP.NET Core

* Nainstalujte balíček NuGet [Microsoft.AspNetCore.Server.WebListener](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.WebListener/). Tím se nainstaluje taky [Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/) jako závislost.

* Volání `UseWebListener` rozšiřující metody na [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) ve vašich `Main` metoda zadání jakékoli WebListener [možnosti](https://github.com/aspnet/HttpSysServer/blob/rel/1.1.2/src/Microsoft.AspNetCore.Server.WebListener/WebListenerOptions.cs) a [nastavení](https://github.com/aspnet/HttpSysServer/blob/rel/1.1.2/src/Microsoft.Net.Http.Server/WebListenerSettings.cs) , které potřebujete , jak je znázorněno v následujícím příkladu:

  [!code-csharp[](weblistener/sample/Program.cs?name=snippet_Main&highlight=13-17)]

* Konfigurovat adresy URL a porty pro naslouchání 

  Ve výchozím nastavení, ASP.NET Core váže k `http://localhost:5000`. Konfigurace předpony adres URL a portů, můžete použít `UseURLs` metody rozšíření `urls` argument příkazového řádku nebo systém konfigurace ASP.NET Core. Další informace najdete v tématu [hostitele v Core(xref:fundamentals/host/index) technologie ASP.NET.

  Web používá naslouchací proces [formáty řetězců předponu Http.Sys](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx). Nejsou žádné požadavky formátu řetězec předpony, které jsou specifické pro WebListener.

  > [!WARNING]
  > Vazby nejvyšší úrovně zástupný znak (`http://*:80/` a `http://+:80`) by měl **není** použít. Vazby nejvyšší úrovně zástupný znak můžete otevřít aplikaci tak, aby slabá místa zabezpečení. To platí pro silné a slabé zástupné znaky. Používejte explicitní hostitele názvy místo zástupných znaků. Vazby zástupný znak subdoménu (například `*.mysub.com`) nemá toto bezpečnostní riziko, pokud celé nadřazené domény (nikoli `*.com`, což je ohrožené). Zobrazit [rfc7230 části-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) Další informace.

  > [!NOTE]
  > Je nutné zadat stejnou předponu řetězce v `UseUrls` , který preregister na serveru. 

* Zajistěte, aby že vaše aplikace není nakonfigurovaná pro spuštění služby IIS nebo IIS Express.

  V sadě Visual Studio je výchozí profil spuštění pro službu IIS Express.  Spustit projekt jako konzolovou aplikaci budete muset ručně změnit vybraný profil, jak je znázorněno na následujícím snímku obrazovky.

  ![Vyberte profil aplikace konzoly](weblistener/_static/vs-choose-profile.png)

## <a name="how-to-use-weblistener-outside-of-aspnet-core"></a>Jak používat WebListener mimo ASP.NET Core

* Nainstalujte [Microsoft.Net.Http.Server](https://www.nuget.org/packages/Microsoft.Net.Http.Server/) balíček NuGet.

* [Předpony adres URL k vytvoření vazby k WebListener a nastavení certifikátů SSL preregister](#preregister-url-prefixes-and-configure-ssl) stejně jako pro použití v ASP.NET Core.

Existují také [nastavení registru Http.Sys](https://support.microsoft.com/kb/820129).


Tady je ukázka kódu, který ukazuje použití WebListener mimo ASP.NET Core:

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

## <a name="preregister-url-prefixes-and-configure-ssl"></a>Preregister předpony adres URL a konfigurovat jím protokol SSL

Služba IIS a WebListener Spolehněte se na základní ovladač režimu jádra Http.Sys naslouchat požadavkům a počáteční zpracování. Ve službě IIS uživatelské rozhraní pro správu poskytuje relativně jednoduché je způsob, jak nakonfigurovat vše, co. Ale pokud používáte WebListener musíte nakonfigurovat ovladač Http.Sys sami. Integrované nástroje pro to, který je netsh.exe. 

Budete muset použít netsh.exe pro nejběžnější úkoly jsou rezervace předpony adres URL a přiřazováním certifikátů SSL.

NetSh.exe není jednoduché nástroje pro použití pro začátečníky. Následující příklad ukazuje holých minimální počet potřebný pro rezervaci předpony adres URL pro porty 80 a 443:

```console
netsh http add urlacl url=http://+:80/ user=Users
netsh http add urlacl url=https://+:443/ user=Users
```

Následující příklad ukazuje, jak přiřadit certifikát SSL:

```console
netsh http add sslcert ipport=0.0.0.0:443 certhash=MyCertHash_Here appid="{00000000-0000-0000-0000-000000000000}".
```

Toto je oficiální dokumentaci:

* [Příkazy Netsh pro Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)
* [UrlPrefix řetězce](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

Následující zdroje poskytují podrobné pokyny pro několik scénářů. Články, které odkazují na `HttpListener` platí jak pro `WebListener`, jako jsou také založené na souboru Http.Sys.

* [Postupy: Konfigurace portu s certifikátem SSL](/dotnet/framework/wcf/feature-details/how-to-configure-a-port-with-an-ssl-certificate)
* [Hosting a certifikát klienta na základě komunikaci přes protokol HTTPS - HttpListener](http://sunshaking.blogspot.com/2012/11/https-communication-httplistener-based.html) to je blogu třetích stran a je poměrně starý, ale stále obsahuje užitečné informace.
* [Postupy: Použití HttpListener návodu nebo Http Server nespravovaný kód (C++) jako jednoduchý serveru SSL](https://blogs.msdn.microsoft.com/jpsanders/2009/09/29/how-to-walkthrough-using-httplistener-or-http-server-unmanaged-code-c-as-an-ssl-simple-server/) toto chování je starší blog s užitečnými informacemi.
* [Jak můžu nastavit WebListener .NET Core s protokolem SSL?](https://blogs.msdn.microsoft.com/timomta/2016/11/04/how-do-i-set-up-a-net-core-weblistener-with-ssl/)

Tady jsou některé nástroje třetích stran, které může být jednodušší než netsh.exe příkazového řádku. Tyto nejsou poskytované nebo schválené pro společnost Microsoft. Nástroje spustit jako správce ve výchozím nastavení, protože netsh.exe samotné vyžaduje oprávnění správce.

* [ovladač HTTP.sys správce](http://httpsysmanager.codeplex.com/) poskytuje uživatelské rozhraní pro výpis a konfigurace certifikátů SSL a možnosti, předpona rezervace a seznamy důvěryhodných certifikátů. 
* [HttpConfig](http://www.stevestechspot.com/ABetterHttpcfg.aspx) umožňuje zobrazit nebo konfigurovat certifikáty SSL a předpony adres URL. Uživatelské rozhraní je přesnější než http.sys správce a zveřejňuje několik dalších možností konfigurace, ale jinak poskytuje podobné funkce. Nelze vytvořit nový seznam důvěryhodných certifikátů (CTL), ale můžete přiřaďte existující značky.

Pro generování certifikátů SSL podepsaných svým držitelem, společnost Microsoft poskytuje nástroje příkazového řádku: [MakeCert.exe](https://msdn.microsoft.com/library/windows/desktop/aa386968) a rutiny prostředí PowerShell [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pki/new-selfsignedcertificate). Existují také uživatelské rozhraní nástroje třetích stran, které bylo snazší pro vygenerování certifikátů SSL podepsaných svým držitelem:

* [Nástroje SelfCert](https://www.pluralsight.com/blog/software-development/selfcert-create-a-self-signed-certificate-interactively-gui-or-programmatically-in-net)
* [Použití nástroje MakeCert uživatelského rozhraní](http://makecertui.codeplex.com/)

## <a name="next-steps"></a>Další kroky

Další informace naleznete v následujících materiálech:

* [Ukázková aplikace pro účely tohoto článku](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/weblistener/sample)
* [WebListener zdrojového kódu](https://github.com/aspnet/HttpSysServer/)
* [Hostování](xref:fundamentals/host/index)
