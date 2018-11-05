---
uid: web-pages/overview/releases/running-v1-and-v2-sites-side-by-side
title: Spuštění různých verzí webových stránek ASP.NET (Razor) vedle sebe | Dokumentace Microsoftu
author: Rick-Anderson
description: Tento článek vysvětluje, jak spustit websites webových stránek ASP.NET (Razor) na stejném počítači nebo serveru, když weby jsou nakonfigurovány pro použití různých verzí...
ms.author: riande
ms.date: 02/10/2014
ms.assetid: a861409b-4ae6-4868-9e09-87edfac3535f
msc.legacyurl: /web-pages/overview/releases/running-v1-and-v2-sites-side-by-side
msc.type: authoredcontent
ms.openlocfilehash: e587398b430795c12a1dcee394852b4e2b8a0e44
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021170"
---
<a name="running-different-versions-of-aspnet-web-pages-razor-side-by-side"></a>Souběžné spuštění různých verzí webových stránek ASP.NET (Razor)
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> Tento článek vysvětluje, jak spustit websites webových stránek ASP.NET (Razor) na stejném počítači nebo serveru, když weby jsou nakonfigurovány pro použití různých verzí z rozhraní ASP.NET Web Pages.
> 
> Co se dozvíte:
> 
> - Výchozí chování Novinky v technologii ASP.NET případě, že máte webů vytvořených s webovými stránkami ASP.NET.
> - Jak konfigurovat novou lokalitu ke spuštění ve starší verzi z rozhraní ASP.NET Web Pages.
>   
> 
> Toto je funkce technologie ASP.NET zavedené v následujícím článku:
> 
> - `webPages:Version` Nastavení konfigurace.
>   
> 
> ## <a name="software-versions"></a>Verze softwaru
> 
> 
> - Webové stránky ASP.NET (Razor) 3
>   
> 
> V tomto kurzu se také pracuje s ASP.NET Web Pages 2 a ASP.NET Web Pages 1.0.


ASP.NET Web Pages podporuje možnost spouštění websites vedle sebe. Tímto způsobem můžete i nadále spouštět starší aplikace ASP.NET Web Pages, vytvářet nové aplikace ASP.NET Web Pages a spustit všechny z nich na stejném počítači.

Zde jsou některé kroky, nezapomeňte při instalaci webových stránek pomocí služby WebMatrix:

- Ve výchozím nastavení bude existující aplikace Web Pages spustit jako na nejnovější verzi ve vašem počítači. (Sestavení jsou nainstalované v globální mezipaměti sestavení (GAC) a automaticky se používají.)
- Pokud budete chtít spuštění webu v jiné verzi z webových stránek ASP.NET, weby k tomu můžete konfigurovat. Pokud váš web už nemá *web.config* souboru v kořenovém adresáři serveru, vytvořte novou a zkopírujte následující kód XML do něj, přepisování stávajícího obsahu. Pokud web již obsahuje *web.config* přidejte `<appSettings>` prvky jako následující ten, který má `<configuration>` oddílu.

    [!code-xml[Main](running-v1-and-v2-sites-side-by-side/samples/sample1.xml)]
  . – Pokud nezadáte verzi v *web.config* soubor lokality je nasazená jako na nejnovější verzi. (Sestavení se zkopírují do *bin* složky v nasazené lokality.)
- Nové aplikace, které vytvoříte pomocí šablony webu v Web Matrix zahrnují budou sestavení verze webové stránky na webu *bin* složky.

Obecně platí, můžete vždy určit, kterou verzi webových stránek pomocí vašeho webu pomocí NuGet nainstalovat odpovídající sestavení na web *bin* složky. Balíčky, najdete v tématu [NuGet.org](http://NuGet.org).

## <a name="additional-resources"></a>Další prostředky

[Hlavní funkce webových stránek v ASP.NET 2](top-features-in-web-pages-2.md)
