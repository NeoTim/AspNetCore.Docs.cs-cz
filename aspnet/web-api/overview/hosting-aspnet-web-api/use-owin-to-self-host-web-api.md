---
uid: web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api
title: Použití rozhraní OWIN k samoobslužnému hostování webového rozhraní API ASP.NET | Dokumentace Microsoftu
author: rick-anderson
description: Tento kurz ukazuje, jak hostovat v konzolové aplikaci, používá OWIN k samoobslužnému hostování webového rozhraní API rozhraní ASP.NET Web API. Otevřít Web Interface pro .NET (OWIN) d...
ms.author: riande
ms.date: 07/09/2013
ms.assetid: a90a04ce-9d07-43ad-8250-8a92fb2bd3d5
msc.legacyurl: /web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api
msc.type: authoredcontent
ms.openlocfilehash: 59ce24aa47ca590fbe9b617dbbe8bc6b3711849e
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667385"
---
<a name="use-owin-to-self-host-aspnet-web-api"></a>Použití rozhraní OWIN k samoobslužnému hostování webového rozhraní API ASP.NET 
====================

> Tento kurz ukazuje, jak hostovat v konzolové aplikaci, používá OWIN k samoobslužnému hostování webového rozhraní API rozhraní ASP.NET Web API.
>
> [Otevřete Web Interface pro .NET](http://owin.org) (OWIN) definuje abstrakce mezi .NET webové servery a webové aplikace. OWIN odpojí webové aplikace ze serveru, který je ideální pro webové aplikace ve vašem vlastním procesu mimo službu IIS s vlastním hostováním OWIN.
>
> ## <a name="software-versions-used-in-the-tutorial"></a>V tomto kurzu použili verze softwaru
>
>
> - [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) 
> - Web API 5.2.7


> [!NOTE]
> Úplný zdrojový kód můžete najít v tomto kurzu na [aspnet.codeplex.com](https://aspnet.codeplex.com/SourceControl/latest#Samples/WebApi/OwinSelfhostSample/ReadMe.txt).


## <a name="create-a-console-application"></a>Vytvoření konzolové aplikace

Na **souboru** nabídce **nový**a pak vyberte **projektu**. Z **nainstalováno**v části **Visual C#** vyberte **Windows Desktop** a pak vyberte **Konzolová aplikace (.Net Framework)**. Zadejte název projektu "OwinSelfhostSample" a vyberte **OK**.

[![](use-owin-to-self-host-web-api/_static/image7.png)](use-owin-to-self-host-web-api/_static/image7.png)

## <a name="add-the-web-api-and-owin-packages"></a>Přidejte balíčky webového rozhraní API a OWIN

Z **nástroje** příkaz **Správce balíčků NuGet**, vyberte **konzoly Správce balíčků**. V okně konzoly Správce balíčků zadejte následující příkaz:

`Install-Package Microsoft.AspNet.WebApi.OwinSelfHost`

Tím se nainstaluje balíček selfhost WebAPI OWIN a všechny požadované balíčky OWIN.

[![](use-owin-to-self-host-web-api/_static/image4.png)](use-owin-to-self-host-web-api/_static/image3.png)

## <a name="configure-web-api-for-self-host"></a>Konfigurace webového rozhraní API pro hostování na vlastním serveru

V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt a vyberte **přidat** / **třídy** přidání nové třídy. Název třídy `Startup`.

![](use-owin-to-self-host-web-api/_static/image5.png)

Nahraďte všechny často používaný kód v tomto souboru následujícím kódem:

[!code-csharp[Main](use-owin-to-self-host-web-api/samples/sample1.cs)]

## <a name="add-a-web-api-controller"></a>Přidat kontroler Web API

Dále přidejte třídu kontroleru webového rozhraní API. V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt a vyberte **přidat** / **třídy** přidání nové třídy. Název třídy `ValuesController`.

Nahraďte všechny často používaný kód v tomto souboru následujícím kódem:

[!code-csharp[Main](use-owin-to-self-host-web-api/samples/sample2.cs)]

## <a name="start-the-owin-host-and-make-a-request-with-httpclient"></a>Spuštění hostitele OWIN a poslat žádost s HttpClient

Nahraďte všechny často používaný kód v souboru Program.cs následujícími způsoby:

[!code-csharp[Main](use-owin-to-self-host-web-api/samples/sample3.cs)]

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci stisknutím klávesy F5 v sadě Visual Studio. Výstup by měl vypadat takto:

[!code-console[Main](use-owin-to-self-host-web-api/samples/sample4.cmd)]

![](use-owin-to-self-host-web-api/_static/image6.png)

## <a name="additional-resources"></a>Další zdroje

[Přehled projektu Katana](../../../aspnet/overview/owin-and-katana/an-overview-of-project-katana.md)

[Hostování webového rozhraní API ASP.NET v roli pracovního procesu Azure](host-aspnet-web-api-in-an-azure-worker-role.md)
