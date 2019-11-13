---
title: Vyberte ASP.NET 4. x a ASP.NET Core
author: rick-anderson
description: Vysvětluje ASP.NET Core vs. ASP.NET 4. x a jejich výběr.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 11/12/2019
no-loc:
- SignalR
uid: fundamentals/choose-between-aspnet-and-aspnetcore
ms.openlocfilehash: 8b1681476f96e8613f9461c507fbb7696f888cbc
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963629"
---
# <a name="choose-between-aspnet-4x-and-aspnet-core"></a>Vyberte ASP.NET 4. x a ASP.NET Core

ASP.NET Core je přepracování ASP.NET 4. x. V tomto článku jsou uvedeny rozdíly mezi nimi.

## <a name="aspnet-core"></a>ASP.NET Core

ASP.NET Core je open source architektura pro různé platformy pro vytváření moderních cloudových webových aplikací ve Windows, macOS nebo Linux.

[!INCLUDE[](~/includes/benefits.md)]

## <a name="aspnet-4x"></a>ASP.NET 4. x

ASP.NET 4. x je vyspělý rámec, který poskytuje služby potřebné k sestavení webových aplikací založených na podnikové úrovni v systému Windows.

## <a name="framework-selection"></a>Výběr architektury

Následující tabulka porovnává ASP.NET Core ASP.NET 4. x.

| ASP.NET Core | ASP.NET 4. x |
|---|---|
|Sestavení pro Windows, macOS nebo Linux|Sestavení pro Windows|
|[Razor Pages](xref:razor-pages/index) je doporučený postup pro vytvoření webového uživatelského rozhraní pro ASP.NET Core 2. x. Viz také [MVC](xref:mvc/overview), [webové rozhraní API](xref:tutorials/first-web-api)a [SignalR](xref:signalr/introduction).|Použití [webových formulářů](/aspnet/web-forms), [SignalR](/aspnet/signalr), [MVC](/aspnet/mvc), [webového rozhraní API](/aspnet/web-api/), [webhooků](/aspnet/webhooks/)nebo [webových stránek](/aspnet/web-pages)|
|Více verzí na počítač|Jedna verze na počítač|
|Vývoj pomocí sady [Visual Studio](https://visualstudio.microsoft.com/vs/), [Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/)nebo [Visual Studio Code](https://code.visualstudio.com/) pomocí C# neboF#|Vývoj pomocí sady [Visual Studio](https://visualstudio.microsoft.com/vs/) pomocí C#, vb neboF#|
|Vyšší výkon než ASP.NET 4. x|Dobrý výkon|
|[Použití .NET Core Runtime](/dotnet/standard/choosing-core-framework-server)|Použití modulu runtime .NET Framework|

Informace o podpoře ASP.NET Core 2. x na .NET Framework najdete v tématu [ASP.NET Core cílení na .NET Framework](xref:index#target-framework) .

## <a name="aspnet-core-scenarios"></a>ASP.NET Core scénáře

* [Websites](xref:tutorials/first-mvc-app/index)
* [Třídy](xref:tutorials/first-web-api)
* [V reálném čase](xref:signalr/index)
* [Nasazení aplikace ASP.NET Core do Azure](/azure/app-service/app-service-web-get-started-dotnet)

## <a name="aspnet-4x-scenarios"></a>ASP.NET 4. x – scénáře

* [Websites](/aspnet/mvc)
* [Třídy](/aspnet/web-api)
* [V reálném čase](/aspnet/signalr)
* [Vytvoření webové aplikace ASP.NET 4. x v Azure](/azure/app-service/app-service-web-get-started-dotnet-framework)

## <a name="additional-resources"></a>Další zdroje

* [Úvod do ASP.NET](/aspnet/overview)
* [Úvod do ASP.NET Core](xref:index)
* <xref:host-and-deploy/azure-apps/index>
