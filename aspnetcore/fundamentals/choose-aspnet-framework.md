---
title: Vyberte ASP.NET 4. x a ASP.NET Core
author: rick-anderson
description: Vysvětluje ASP.NET Core vs. ASP.NET 4. x a jejich výběr.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/choose-between-aspnet-and-aspnetcore
ms.openlocfilehash: 1fb81d5a54cf332ca473af8fbe1841813a127be7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775879"
---
# <a name="choose-between-aspnet-4x-and-aspnet-core"></a>Vyberte ASP.NET 4. x a ASP.NET Core

ASP.NET Core je přepracování ASP.NET 4. x. V tomto článku jsou uvedeny rozdíly mezi nimi.

## <a name="aspnet-core"></a>Jádro ASP.NET

ASP.NET Core je open source architektura pro různé platformy pro vytváření moderních cloudových webových aplikací ve Windows, macOS nebo Linux.

[!INCLUDE[](~/includes/benefits.md)]

## <a name="aspnet-4x"></a>ASP.NET 4.x

ASP.NET 4. x je vyspělý rámec, který poskytuje služby potřebné k sestavení webových aplikací založených na podnikové úrovni v systému Windows.

## <a name="framework-selection"></a>Výběr architektury

Následující tabulka porovnává ASP.NET Core ASP.NET 4. x.

| Jádro ASP.NET | ASP.NET 4.x |
|---|---|
|Sestavení pro Windows, macOS nebo Linux|Sestavení pro Windows|
|Stránky jsou doporučeným přístupem k vytvoření webového uživatelského rozhraní ASP.NET Core 2. x. [ Razor ](xref:razor-pages/index) Viz také [MVC](xref:mvc/overview), [webové rozhraní API](xref:tutorials/first-web-api)a [SignalR](xref:signalr/introduction).|Použití [webových formulářů](/aspnet/web-forms), [SignalR](/aspnet/signalr), [MVC](/aspnet/mvc), [webového rozhraní API](/aspnet/web-api/), [webhooků](/aspnet/webhooks/)nebo [webových stránek](/aspnet/web-pages)|
|Více verzí na počítač|Jedna verze na počítač|
|Vývoj pomocí sady [Visual Studio](https://visualstudio.microsoft.com/vs/), [Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/)nebo [Visual Studio Code](https://code.visualstudio.com/) pomocí C# nebo F #|Vývoj pomocí sady [Visual Studio](https://visualstudio.microsoft.com/vs/) pomocí jazyků C#, vb nebo F #|
|Vyšší výkon než ASP.NET 4. x|Dobrý výkon|
|[Použití .NET Core Runtime](/dotnet/standard/choosing-core-framework-server)|Použití modulu runtime .NET Framework|

Informace o podpoře ASP.NET Core 2. x na .NET Framework najdete v tématu [ASP.NET Core cílení na .NET Framework](xref:index#target-framework) .

## <a name="aspnet-core-scenarios"></a>ASP.NET Core scénáře

* [Websites](xref:tutorials/first-mvc-app/index)
* [Rozhraní API](xref:tutorials/first-web-api)
* [Reálný čas](xref:signalr/introduction)
* [Nasazení aplikace ASP.NET Core do Azure](/azure/app-service/app-service-web-get-started-dotnet)

## <a name="aspnet-4x-scenarios"></a>ASP.NET 4. x – scénáře

* [Websites](/aspnet/mvc)
* [Rozhraní API](/aspnet/web-api)
* [Reálný čas](/aspnet/signalr)
* [Vytvoření webové aplikace ASP.NET 4. x v Azure](/azure/app-service/app-service-web-get-started-dotnet-framework)

## <a name="additional-resources"></a>Další zdroje

* [Úvod do ASP.NET](/aspnet/overview)
* [Úvod do ASP.NET Core](xref:index)
* <xref:host-and-deploy/azure-apps/index>
