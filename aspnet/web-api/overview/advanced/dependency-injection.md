---
uid: web-api/overview/advanced/dependency-injection
title: Injektáž závislostí v rozhraní ASP.NET Web API 2 | Dokumentace Microsoftu
author: MikeWasson
description: Tento kurz ukazuje postupy při vložení závislosti do vašeho kontroleru webového rozhraní API ASP.NET. Verze softwaru používaných kurz webové rozhraní API 2 Unity Application Block...
ms.author: riande
ms.date: 01/20/2014
ms.assetid: e3d3e7ba-87f0-4032-bdd3-31f3c1aa9d9c
msc.legacyurl: /web-api/overview/advanced/dependency-injection
msc.type: authoredcontent
ms.openlocfilehash: 318a2f1c587feb360212a390bb5de7bdc127513d
ms.sourcegitcommit: 42a8164b8aba21f322ffefacb92301bdfb4d3c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54341823"
---
<a name="dependency-injection-in-aspnet-web-api-2"></a>Injektáž závislostí v rozhraní ASP.NET Web API 2
====================
podle [Mike Wasson](https://github.com/MikeWasson)

[Stáhnout dokončený projekt](http://code.msdn.microsoft.com/ASP-NET-Web-API-Tutorial-468ee148)

> Tento kurz ukazuje postupy při vložení závislosti do vašeho kontroleru webového rozhraní API ASP.NET.
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>V tomto kurzu použili verze softwaru
> 
> 
> - Webové rozhraní API 2
> - [Blok aplikací Unity](https://www.nuget.org/packages/Unity/)
> - Entity Framework 6 (verze 5 taky funguje)


## <a name="what-is-dependency-injection"></a>Co je injektáž závislostí?

*Závislost* je libovolný objekt, který je vyžadován jiným objektem. Například je společné pro definování [úložiště](http://martinfowler.com/eaaCatalog/repository.html) přístup k datům, která zpracovává. Pojďme ukazuje příklad. Nejprve budeme definovat model domény:

[!code-csharp[Main](dependency-injection/samples/sample1.cs)]

Tady je jednoduché úložiště třídu, která ukládá položky v databázi pomocí Entity Frameworku.

[!code-csharp[Main](dependency-injection/samples/sample2.cs)]

Teď nadefinujeme kontroler Web API, který podporuje požadavky GET pro `Product` entity. (I jsem vynecháním POST a další metody pro zjednodušení.) Zde je první pokus o:

[!code-csharp[Main](dependency-injection/samples/sample3.cs)]

Všimněte si, že třída kontroleru závisí na `ProductRepository`, a vytvoření kontroleru, dáváme `ProductRepository` instance. Je však vhodné pevný kód závislosti tímto způsobem z několika důvodů.

- Pokud budete chtít nahradit `ProductRepository` s jinou implementaci, budete také muset upravit třídy kontroleru.
- Pokud `ProductRepository` má závislosti, je nutné nakonfigurovat tyto uvnitř kontroleru. Pro projekt velkých s více řadiči změní váš kód konfigurace rozmístěny na váš projekt.
- Je těžké test jednotky, protože kontroler je pevně zakódovaný k dotazování databáze. Pro testování částí měli byste použít úložiště model nebo zástupné procedury, které není možné s návrhem currect.

Jsme řešení těchto problémů podle *vkládá* úložiště do kontroleru. Nejprve Refaktorovat `ProductRepository` třídy do rozhraní:

[!code-csharp[Main](dependency-injection/samples/sample4.cs)]

Zadejte `IProductRepository` jako parametr konstruktoru:

[!code-csharp[Main](dependency-injection/samples/sample5.cs)]

Tento příklad používá [konstruktor vkládání](http://www.martinfowler.com/articles/injection.html#FormsOfDependencyInjection). Můžete také použít *setter vkládání*, kde nastavujete závislost prostřednictvím metody setter nebo vlastnosti.

Ale nyní je nějaký problém, protože aplikace přímo nevytváří kontroleru. Webové rozhraní API vytvoří kontroler při směruje žádosti a webového rozhraní API neví nic o `IProductRepository`. To přichází překladač závislostí webového rozhraní API.

## <a name="the-web-api-dependency-resolver"></a>Překladač závislostí webového rozhraní API

Definuje webového rozhraní API **IDependencyResolver** rozhraní pro vyřešení závislostí. Tady je definice rozhraní:

[!code-csharp[Main](dependency-injection/samples/sample6.cs)]

**IDependencyScope** rozhraní má dvě metody:

- **GetService** vytvoří jednu instanci typu.
- **GetServices** vytvoří kolekci objektů zadaného typu.

**IDependencyResolver** dědí metodu **IDependencyScope** a přidá **BeginScope** metody. O oborech můžu zabývat později v tomto kurzu.

Pokud webové rozhraní API vytvoří instanci kontroleru, nejprve volá **IDependencyResolver.GetService**, předejte typ kontroleru. Toto rozšíření zavěšení můžete použít k vytvoření kontroleru, řešení případných závislostí. Pokud **GetService** , vrátí hodnotu null, vyhledá webového rozhraní API pro konstruktor třídy kontroleru.

## <a name="dependency-resolution-with-the-unity-container"></a>Řešení závislostí s kontejnerem Unity

I když můžete napsat kompletní **IDependencyResolver** implementace úplně od začátku, rozhraní je ve skutečnosti navrženy tak, aby fungoval jako most mezi webovým rozhraním API a existující kontejnery IoC.

Kontejner IoC je softwarová součást, která zodpovídá za správu závislostí. Registrace typů s kontejnerem a následné použití k vytvoření objektů kontejneru. Kontejner automaticky zjistí vztahy závislostí. Spousta kontejnerů IoC také umožňují řídit věci, jako je životní cyklus objektů a obor.

> [!NOTE]
> "IoC" znamená "inverzi ovládacího prvku", což je obecný vzor, pokud rámec volání do kódu aplikace. Kontejner IoC vytvoří objekty, které "Invertuje" obvyklý tok řízení.


V tomto kurzu použijeme [Unity](https://msdn.microsoft.com/library/ff647202.aspx) z Microsoft Patterns &amp; postupy. (Zahrnout další oblíbené knihovny [Castle Windsor](http://www.castleproject.org/), [Spring.Net](http://www.springframework.net/), [Autofac](https://code.google.com/p/autofac/), [Ninject](http://www.ninject.org/), a [StructureMap ](http://structuremap.github.io/documentation/).) Správce balíčků NuGet můžete nainstalovat Unity. Z **nástroje** v aplikaci Visual Studio, vyberte v nabídce **Správce balíčků NuGet**, vyberte **konzoly Správce balíčků**. V okně konzoly Správce balíčků zadejte následující příkaz:

[!code-console[Main](dependency-injection/samples/sample7.cmd)]

Tady je implementace **IDependencyResolver** tím končí naše kontejner Unity.

[!code-csharp[Main](dependency-injection/samples/sample8.cs)]

> [!NOTE]
> Pokud **GetService** metodu nelze přeložit typ, měla by vrátit **null**. Pokud **GetServices** metodu nelze přeložit typ, měla by vrátit objekt prázdnou kolekci. Není vyvolávat výjimky pro neznámé typy.


## <a name="configuring-the-dependency-resolver"></a>Konfigurace překladač závislostí

Nastavit překladače závislostí **překladače závislostí** vlastnost globálního **HttpConfiguration** objektu.

Následující kód registrů `IProductRepository` rozhraní pomocí Unity a pak vytvoří `UnityResolver`.

[!code-csharp[Main](dependency-injection/samples/sample9.cs)]

## <a name="dependency-scope-and-controller-lifetime"></a>Závislost oboru a životnosti Kontroleru

Kontrolery se vytvoří každý požadavek. Ke správě životnosti objektu **IDependencyResolver** používá koncept *oboru*.

Překladač závislostí, který je připojen k **HttpConfiguration** objektu má globální obor. Webové rozhraní API vytvoří kontroler, volá **BeginScope**. Tato metoda vrátí **IDependencyScope** , která představuje podřízeném oboru.

Potom volá webové rozhraní API **GetService** v podřízeném oboru pro vytvoření kontroleru. Po dokončení žádosti se volá webové rozhraní API **Dispose** v podřízeném oboru. Použití **Dispose** metodu dispose závislostí kontroleru.

Jak implementovat **BeginScope** závisí na kontejner IoC. Pro Unity odpovídá rozsahu podřízený kontejner:

[!code-csharp[Main](dependency-injection/samples/sample10.cs)]

Většina IoC kontejnery mají podobné ekvivalenty.
