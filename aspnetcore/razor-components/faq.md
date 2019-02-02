---
title: Nejčastější dotazy (FAQ) součásti syntaxe Razor
author: guardrex
description: Najděte odpovědi na nejčastější dotazy o součástech Razor a Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2019
uid: razor-components/faq
ms.openlocfilehash: 8555a95ed0dea2b8bcca173c6ba5acfa1a221cfe
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55668114"
---
# <a name="frequently-asked-questions-faq-about-razor-components"></a>Nejčastější dotazy (FAQ) součásti syntaxe Razor

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

## <a name="what-are-razor-components-and-blazor"></a>Jaké jsou komponenty Razor a Blazor?

*ASP.NET Core Razor komponenty* jednostránkové webové rozhraní aplikace založená na rozhraní .NET, který se spustí na straně serveru v ASP.NET Core.

*Blazor* je rozšířením součástí syntaxe Razor pro spuštění aplikace na straně klienta, na kterém běží v prohlížeči s WebAssembly. Blazor poskytuje všechny výhody architekturu uživatelského rozhraní na straně klienta web pomocí rozhraní .NET na straně klienta.

## <a name="im-new-to-net-whats-net"></a>Já jsem ještě .NET. Co je .NET?

[.NET](https://www.microsoft.com/net) je zdarma, multiplatformní open source platforma pro vývojáře pro vytváření mnoho různých typů aplikací (plochy, mobilních, hry, web). .NET obsahuje spravovaného modulu runtime standardní sadu [knihovny](/dotnet/api), a podpora pro více moderní programovací jazyky: [C#](/dotnet/csharp/), [ F# ](/dotnet/fsharp/), a [VB](/dotnet/visual-basic/). Je možné [Začínáme s .NET během 10 minut](https://www.microsoft.com/net/learn/get-started/windows).

## <a name="why-would-i-use-net-for-web-development"></a>Proč používat .NET pro vývoj pro web?

V prohlížeči pomocí rozhraní .NET nabízí celou řadu výhod, které vám mohou pomoci vývoj pro web jednodušší a produktivnější:

* **Stabilní a konzistentní**: .NET nabízí standardní rozhraní API, nástroje a infrastruktura sestavení na všech platformách .NET, které jsou stabilní, funkce bohaté a snadno se používá.
* **Moderní jazyky inovativní**: jazycích .NET, jako jsou [ C# ](/dotnet/csharp/) a [ F# ](/dotnet/fsharp/) proveďte programování radosti a velkou a stále zlepšovaly inovativní novými funkcemi jazyka.
* **Špičkové nástroje**: [Sady Visual Studio](https://www.visualstudio.com/) produktové řady poskytuje skvělé vývojové prostředí .NET ve Windows, Linuxu a macOS.
* **Rychlá a škálovatelná**: .NET má dlouhou historii výkonu, spolehlivosti a zabezpečení na serveru. Pomocí kompletního řešení .NET usnadňuje škálování aplikací.

## <a name="how-can-you-run-net-in-a-web-browser"></a>Jak můžete ve webovém prohlížeči spouštět .NET?

Spuštění rozhraní .NET v prohlížeči je možné díky relativně nové technologie standardizované webové volá [WebAssembly](http://webassembly.org/). WebAssembly "portable, velikost a zatížení – čas – efektivní formát je vhodný pro kompilaci na webu." Kódu kompilovaném pro WebAssembly můžete spustit v libovolném prohlížeči nativní rychlostí. Ke spuštění .NET binární soubory ve webovém prohlížeči, používáme modulu runtime .NET (konkrétně [Mono](http://www.mono-project.com/news/2017/08/09/hello-webassembly/)), který byl zkompilován pro WebAssembly.

## <a name="does-blazor-compile-my-entire-net-based-app-to-webassembly"></a>Moje celý zkompiluje Blazor. Na základě NET aplikace WebAssembly?

Ne, Blazor aplikace se skládá z normální zkompilovaná sestavení .NET, které jsou stáhnout a spustit ve webovém prohlížeči pomocí na základě WebAssembly .NET runtime. Pouze .NET samotný modul runtime je zkompilováno do WebAssembly. Podporující uvedené, úplné [statické náskok před kompilace (AoT)](http://www.mono-project.com/news/2018/01/16/mono-static-webassembly-compilation/) aplikace WebAssembly může být něco přidáváme další dolů cestách.

## <a name="wouldnt-the-app-download-size-be-huge-if-it-also-includes-a-net-runtime"></a>Nebude velikost ke stažení aplikace obrovskou, pokud zahrnuje taky modul .NET runtime?

Ne nutně. Moduly .NET runtime se dělí na všechny obrazce v velikosti. Časná Blazor prototypy používá compact .NET modul runtime (včetně spuštění sestavení, uvolňování paměti, dělení na vlákna), který zkompiluje pouhé 60KB WebAssembly. Blazor nyní běží v Mono, která je aktuálně podstatně větší. Příležitosti k optimalizaci velikosti abound však včetně slučování a ořezávání binární soubory modulu runtime a aplikace. Jiná zmírnění rizik potenciální stahování velikost patří ukládání do mezipaměti a používání sítě CDN.

## <a name="what-features-will-razor-components-and-blazor-support"></a>Jaké funkce bude Razor komponenty a Blazor podporovat?

Komponenty Razor a Blazor podporuje všechny funkce architektury moderních jednostránkové aplikace:

* Model součásti pro vytváření složení uživatelského rozhraní
* Směrování
* Rozložení
* Formuláře a ověřování
* Injektáž závislostí
* Zprostředkovatel komunikace s objekty jazyka JavaScript
* Živé opětovné načtení v prohlížeči během vývoje.
* Vykreslování na straně serveru
* Plné ladění rozhraní .NET v prohlížeči i v integrovaném vývojovém prostředí
* Propracovaná funkce IntelliSense a nástroje
* Možnost spouštět ve starších prohlížečích (bez WebAssembly) pomocí ASP.NET Core Razor komponenty
* Publikování a oříznutí velikost aplikace

## <a name="can-i-use-blazor-without-running-net-on-the-server"></a>Můžete použít Blazor bez spuštění .NET na serveru?

Ano, můžete aplikaci Blazor nasadit jako sadu statických souborů bez nutnosti jakékoli podpora .NET na serveru.

## <a name="can-i-use-blazor-with-aspnet-core-on-the-server"></a>Můžu použít Blazor s ASP.NET Core na serveru?

Ano! Integruje Blazor [ASP.NET Core](/aspnet/core) na serveru jako *ASP.NET Core Razor komponenty* zajistit snadné a konzistentní předprodukčních webové vývojářské řešení.

## <a name="is-blazor-a-net-port-of-an-existing-javascript-framework"></a>Je Blazor .NET port existujícího rozhraní JavaScriptu?

Je Blazor *nový rámec* stávajících architektur moderních jednostránkovou aplikaci, například React, Angular a Vue se inspirovat.

## <a name="how-can-i-try-out-blazor"></a>Jak můžu vyzkoušet Blazor?

K vytvoření vaší první Blazor webové aplikace prohlédněte si naše [– Příručka Začínáme](xref:razor-components/get-started).

## <a name="why-is-blazor-an-experimental-project"></a>Proč je Blazor "experimentální" Projekt?

Blazor je experimentální projekt, protože stále existují spoustu otázek k zodpovězení o jeho životnost a odvolání. Účelem této počáteční experimentální fázi je:

* Seznámení se základními technických záležitostech.
* Měřidla zájmu a tak, aby naslouchala na zpětnou vazbu.

Máme optimistické o budoucnosti společnosti Blazor; ale v tuto chvíli Blazor není potvrzená produktu by se měly zvažovat předem alfa.

ASP.NET Core Razor komponenty se podporuje v ASP.NET Core 3.0 nebo novější.

## <a name="is-this-silverlight-all-over-again"></a>Je to Silverlight nejrůznějších znovu?

Ne, Blazor je webové rozhraní .NET framework založeného na HTML a CSS, která běží v prohlížeči pomocí otevřených webových standardů. Nevyžaduje modul plug-in, a funguje v mobilních zařízeních a starší prohlížeče.

## <a name="does-blazor-use-xaml"></a>Používá Blazor XAML?

Ne, Blazor je webové rozhraní založeného na HTML, CSS a dalších standardních webových technologií.

## <a name="is-webassembly-supported-in-all-browsers"></a>Je WebAssembly podporovány ve všech prohlížečích?

Ano, WebAssembly získala caiq prohlížečů a [všechny moderní prohlížeče teď podporují WebAssembly](https://caniuse.com/#search=webassembly).

## <a name="does-blazor-work-on-mobile-browsers"></a>Funguje v mobilních prohlížečích Blazor?

Ano, [moderní mobilní prohlížeče podporují také WebAssembly](https://caniuse.com/#search=webassembly).

## <a name="what-about-older-browsers-that-dont-support-webassembly-for-example-does-blazor-work-in-ie"></a>A co starší prohlížeče, které nepodporují WebAssembly? Například Blazor fungovat v IE?

Pro starší prohlížeče, které nepodporují WebAssembly, použijte komponenty technologie ASP.NET Core Razor [model hostingu na straně serveru](xref:razor-components/hosting-models#server-side-hosting-model). Aplikace na straně serveru součásti syntaxe Razor poskytují vynikající kompatibilitu a výkon s starší prohlížeče.

## <a name="can-i-use-net-standard-libraries"></a>Můžete použít knihovny .NET Standard?

Ano, podporuje .NET runtime používá pro Blazor .NET Standard 2.0. Rozhraní API, která nejsou podporované v prohlížeči s vyvolání výjimky na straně klienta Blazor *nepodporuje* výjimky.

## <a name="dont-you-need-features-like-garbage-collection-and-threading-added-to-webassembly-to-make-this-work"></a>Nepotřebujete funkce, jako jsou kolekce paměti a dělení na vlákna přidán do WebAssembly provést tuto práci?

Ne, stačí WebAssembly v jejím aktuálním stavu. Modul .NET runtime zpracovává vlastní uvolňování paměti a dělení na vlákna se týká.

## <a name="can-i-use-existing-javascript-libraries"></a>Můžete používat existující knihovny JavaScript?

Ano, aplikace může volat do jazyka JavaScript prostřednictvím vzájemné spolupráce rozhraní API jazyka JavaScript.

## <a name="can-i-access-the-dom-from-an-app"></a>Můžete přistupovat v modelu DOM z aplikace?

Přistupovat k modelu DOM pomocí zprostředkovatele komunikace s objekty jazyka JavaScript z kódu .NET. Však rozhraní založených na komponentách minimalizuje nutnost přímý přístup k modelu DOM.

## <a name="why-mono-why-not-net-core-or-corert"></a>Důvod, proč Mono? Proč ne .NET Core nebo CoreRT?

[Mono](http://www.mono-project.com/) sponzorovaný společností Microsoft opensourcových implementaci rozhraní .NET Framework. Používá mono [Xamarin](https://www.xamarin.com/) vytvářet nativní klientské aplikace pro Android, iOS a macOS. Mono se používá také podle [Unity](https://unity3d.com/) pro vývoj her. Tým Microsoftu pro Xamarin [jsme oznámili plány](http://www.mono-project.com/news/2017/08/09/hello-webassembly/) přidání podpory pro WebAssembly pro Mono a jste byla provádění konstantní průběh ([Mono a WebAssembly – aktualizace na statické kompilace 1/16/2018](http://www.mono-project.com/news/2018/01/16/mono-static-webassembly-compilation/)). Vzhledem k tomu, že Blazor je zaměřený na WebAssembly architekturu uživatelského rozhraní na straně klienta webové, Mono je přirozeně vhodná.

Naproti tomu [.NET Core](https://www.microsoft.com/net/learn/get-started/windows) slouží především pro serverové aplikace a pro různé platformy konzolové aplikace. .NET core je použít pro vytvoření back-endu ASP.NET Core pro aplikaci Blazor, ale nikoli pro sestavování klientské aplikace. [CoreRT](https://github.com/dotnet/corert) je .NET Core runtime optimalizovaný pro kompilaci AoT; a, pokud má WebAssembly cíli, projekt bude stále nedokončenou prací a ne produkt.

## <a name="where-did-the-name-blazor-come-from"></a>Pokud název "Blazor" pocházejí z?

Blazor výrazně využívá [Razor](/aspnet/core/mvc/views/razor?view=aspnetcore-2.1), syntaxe značek jazyka HTML a C#. **Prohlížeč + Razor = Blazor!** Když výraznější, je také název swanky sak používány hipsters, které mají vynikající chuť v podobě, styl a programovacích jazyků.
