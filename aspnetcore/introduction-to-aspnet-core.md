---
title: Úvod do ASP.NET Core
author: rick-anderson
description: Seznamte se s ASP.NET Core, vysoce výkonnou a open-source architekturou pro vytváření moderních cloudových aplikací připojených k Internetu.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: index
ms.openlocfilehash: 7f46051193681ecac59428b77ca1e36830c7bb63
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776333"
---
# <a name="introduction-to-aspnet-core"></a>Úvod do ASP.NET Core

Autoři: [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT) a [Shaun Luttin](https://twitter.com/dicshaunary)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core je vysoce výkonná [Open-Source](https://github.com/dotnet/aspnetcore) platforma pro různé platformy, která umožňuje vytvářet moderní cloudové aplikace připojené k Internetu. S ASP.NET Core můžete:

* Sestavujte webové aplikace a služby, aplikace [Internet věcí (IoT)](https://www.microsoft.com/internet-of-things/) a mobilní back-endy.
* Používat oblíbené vývojářské nástroje v systémech Windows, Mac OS a Linux.
* Nasazovat v cloudu nebo místně
* Spusťte v [.NET Core](/dotnet/core/introduction).

## <a name="why-choose-aspnet-core"></a>Proč zvolit ASP.NET Core?

Miliony vývojářů používají nebo používali [ASP.NET 4. x](/aspnet/overview) k vytváření webových aplikací. ASP.NET Core je přepracování ASP.NET 4. x, včetně změn v architektuře, jejichž výsledkem je štíhlá a větší modulární architektura.

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a>Vytváření webových rozhraní API a webového uživatelského rozhraní v ASP.NET Core MVC

Architektura ASP.NET Core MVC nabízí funkce umožňující vytvářet [webová rozhraní API](xref:tutorials/first-web-api) a [webové aplikace](xref:tutorials/razor-pages/index):

* [Vzor Model-View-Controller (MVC)](xref:mvc/overview) přispívá k tomu, aby vaše webová rozhraní API a webové aplikace byly testovatelné.
* [Razor Pages](xref:razor-pages/index) představuje stránkovaný programový model, se kterým je vytváření webového uživatelského rozhraní jednodušší a produktivnější.
* [Kód Razor](xref:mvc/views/razor) poskytuje produktivní syntaxi jak pro [stránky Razor](xref:razor-pages/index), tak pro [zobrazení MVC](xref:mvc/views/overview).
* [Pomocné rutiny značky](xref:mvc/views/tag-helpers/intro) umožňují, aby se kód na straně serveru v souborech Razor podílel na vytváření a vykreslování prvků HTML.
* Díky integrované podpoře [různých datových formátů a vyjednávání obsahu](xref:web-api/advanced/formatting) mohou vaše webová rozhraní API používat různí klienti, včetně prohlížečů a mobilních zařízení.
* [Vazby modelu](xref:mvc/models/model-binding) automaticky mapují data požadavků HTTP na parametry metod akce.
* [Ověření modelu](xref:mvc/models/validation) automaticky provede ověření na straně klienta i serveru.

## <a name="client-side-development"></a>Vývoj klientské strany

ASP.NET Core se hladce integruje s oblíbenými architekturami a knihovnami na straně klienta, včetně [Blazor](xref:blazor/index), [úhlů](xref:spa/angular), [reakce](xref:spa/react)a [zavedení](https://getbootstrap.com/). Další informace najdete <xref:blazor/index> v tématech a souvisejících tématech v části *vývoj na straně klienta*.

<a name="target-framework"></a>

## <a name="aspnet-core-target-frameworks"></a>ASP.NET Core cílové architektury

ASP.NET Core 3. x a novější může cílit jenom na .NET Core. Obecně se ASP.NET Core skládá z knihoven [.NET Standard](/dotnet/standard/net-standard) . Knihovny napsané pomocí .NET Standard 2,0 běží na libovolné [platformě .NET, která implementuje .NET Standard 2,0](/dotnet/standard/net-standard#net-implementation-support).

Cílení na .NET Core má několik výhod, které přibývají s každou vydanou verzí. Mezi výhody .NET Core oproti .NET Framework patří:

* Mutliplatformní: Běží na Windows, macOS a Linux.
* Vyšší výkon
* [Souběžná správa verzí](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* Nová rozhraní API
* Open source

## <a name="recommended-learning-path"></a>Doporučený studijní program

Pro Úvod k vývoji ASP.NET Corech aplikací doporučujeme následující posloupnost kurzů:

1. Postupujte podle kurzu pro typ aplikace, který chcete vyvíjet nebo udržovat.

   |Typ aplikace  |Scénář  |Tutoriál  |
   |----------|----------|----------|
   |Webová aplikace                   | Nový vývoj webového uživatelského rozhraní na straně serveru |[Začínáme se stránkami Razor](xref:tutorials/razor-pages/razor-pages-start) |
   |Webová aplikace                   | Údržba aplikace MVC |[Začínáme s MVC](xref:tutorials/first-mvc-app/start-mvc)|
   |Webová aplikace                   | Vývoj webového uživatelského rozhraní na straně klienta |[Začínáme s Blazor](xref:tutorials/first-blazor-app) |
   |Webové rozhraní API                   | Služby HTTP RESTful |[Vytvoření webového rozhraní API](xref:tutorials/first-web-api)&dagger; |
   |Aplikace vzdáleného volání procedur | Služby pro první kontrakt využívající vyrovnávací paměti protokolu |[Začínáme se službou gRPC](xref:tutorials/grpc/grpc-start) |
   |Aplikace v reálném čase             | Obousměrná komunikace mezi servery a připojenými klienty |[Začínáme s funkcí SignalR](xref:tutorials/signalr) |

1. Postupujte podle kurzu, který ukazuje, jak získat základní přístup k datům.

   |Scénář  |Tutoriál  |
   |----------|----------|
   |Nový vývoj        |[Razor Pages pomocí Entity Framework Core](xref:data/ef-rp/intro) |
   |Údržba aplikace MVC |[MVC pomocí Entity Framework Core](xref:data/ef-mvc/intro) |

1. Přečtěte si přehled ASP.NET Core [základních](xref:fundamentals/index) součástí, které se vztahují na všechny typy aplikací.

1. Projděte si obsah a vyhledejte další témata, která vás zajímají.

&dagger;K dispozici je také [Interaktivní kurz webového rozhraní API](/learn/modules/build-web-api-net-core). Není nutná žádná místní instalace vývojářských nástrojů. Kód se spouští v [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) v prohlížeči a k testování se používá [kudrlinkou](https://curl.haxx.se/) .

## <a name="migrate-from-net-framework"></a>Migrace z .NET Framework

Referenční příručka pro migraci aplikací ASP.NET 4. x na ASP.NET Core najdete v tématu <xref:migration/proper-to-2x/index>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core je vysoce výkonná [Open-Source](https://github.com/dotnet/aspnetcore) platforma pro různé platformy, která umožňuje vytvářet moderní cloudové aplikace připojené k Internetu. S ASP.NET Core můžete:

* Sestavujte webové aplikace a služby, aplikace [Internet věcí (IoT)](https://www.microsoft.com/internet-of-things/) a mobilní back-endy.
* Používat oblíbené vývojářské nástroje v systémech Windows, Mac OS a Linux.
* Nasazovat v cloudu nebo místně
* Používat ke spuštění [.NET Core nebo .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).

## <a name="why-choose-aspnet-core"></a>Proč zvolit ASP.NET Core?

Miliony vývojářů používají nebo používali [ASP.NET 4. x](/aspnet/overview) k vytváření webových aplikací. ASP.NET Core je přepracovanou verzí ASP.NET 4.x se změněnou architekturou. Výsledkem je odlehčené a modulárnější prostředí.

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a>Vytváření webových rozhraní API a webového uživatelského rozhraní v ASP.NET Core MVC

Architektura ASP.NET Core MVC nabízí funkce umožňující vytvářet [webová rozhraní API](xref:tutorials/first-web-api) a [webové aplikace](xref:tutorials/razor-pages/index):

* [Vzor Model-View-Controller (MVC)](xref:mvc/overview) přispívá k tomu, aby vaše webová rozhraní API a webové aplikace byly testovatelné.
* [Razor Pages](xref:razor-pages/index) představuje stránkovaný programový model, se kterým je vytváření webového uživatelského rozhraní jednodušší a produktivnější.
* [Kód Razor](xref:mvc/views/razor) poskytuje produktivní syntaxi jak pro [stránky Razor](xref:razor-pages/index), tak pro [zobrazení MVC](xref:mvc/views/overview).
* [Pomocné rutiny značky](xref:mvc/views/tag-helpers/intro) umožňují, aby se kód na straně serveru v souborech Razor podílel na vytváření a vykreslování prvků HTML.
* Díky integrované podpoře [různých datových formátů a vyjednávání obsahu](xref:web-api/advanced/formatting) mohou vaše webová rozhraní API používat různí klienti, včetně prohlížečů a mobilních zařízení.
* [Vazby modelu](xref:mvc/models/model-binding) automaticky mapují data požadavků HTTP na parametry metod akce.
* [Ověření modelu](xref:mvc/models/validation) automaticky provede ověření na straně klienta i serveru.

## <a name="client-side-development"></a>Vývoj klientské strany

ASP.NET Core se hladce integruje s oblíbenými architekturami a knihovnami na straně klienta, včetně [Blazor](xref:blazor/index), [úhlů](xref:spa/angular), [reakce](xref:spa/react)a [zavedení](https://getbootstrap.com/). Další informace najdete <xref:blazor/index> v tématech a souvisejících tématech v části *vývoj na straně klienta*.

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a>Cílení ASP.NET Core na .NET Framework

Cílem ASP.NET Core 2.x může být .NET Core nebo .NET Framework. Aplikace ASP.NET Core, jejichž cílem je .NET Framework, nejsou multiplatformní, ale běží jen ve Windows. ASP.NET Core 2.x se obecně skládá z knihoven [.NET Standard](/dotnet/standard/net-standard). Knihovny napsané pomocí .NET Standard 2,0 běží na libovolné [platformě .NET, která implementuje .NET Standard 2,0](/dotnet/standard/net-standard#net-implementation-support).

ASP.NET Core 2. x se podporuje v .NET Framework verzích, které implementují .NET Standard 2,0:

* Doporučuje se .NET Framework nejnovější verze.
* .NET Framework 4.6.1 a novější.

ASP.NET Core 3.0 a novější poběží jenom na platformě .NET Core. Další podrobnosti týkající se této změny najdete v tématu [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/) (První pohled na změny, které přináší ASP.NET Core 3.0).

Cílení na .NET Core má několik výhod, které přibývají s každou vydanou verzí. Mezi výhody .NET Core oproti .NET Framework patří:

* Mutliplatformní: Běží v systémech macOS, Linux a Windows.
* Vyšší výkon
* [Souběžná správa verzí](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* Nová rozhraní API
* Open source

Aby bylo možné zavřít mezeru rozhraní API z .NET Framework do .NET Core, [Sada Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) vystavila tisíce rozhraní API jenom pro Windows, která jsou k dispozici v .NET Core. Tato rozhraní API nebyla v .NET Core 1.x dostupná.

## <a name="recommended-learning-path"></a>Doporučený studijní program

Jako úvod do vývoje aplikací ASP.NET Core doporučujeme následující posloupnost kurzů a článků:

1. Postupujte podle kurzu pro typ aplikace, kterou chcete vyvíjet nebo udržovat.

   |Typ aplikace  |Scénář  |Tutoriál  |
   |----------|----------|----------|
   |Webová aplikace                   | Vývoj nové aplikace        |[Začínáme se stránkami Razor](xref:tutorials/razor-pages/razor-pages-start) |
   |Webová aplikace                   | Údržba aplikace MVC |[Začínáme s MVC](xref:tutorials/first-mvc-app/start-mvc)|
   |Webové rozhraní API                   |                            |[Vytvoření webového rozhraní API](xref:tutorials/first-web-api)&dagger; |
   |Aplikace v reálném čase             |                            |[Začínáme s funkcí SignalR](xref:tutorials/signalr) |

1. Postupujte podle kurzu, který ukazuje, jak získat základní přístup k datům.

   |Scénář  |Tutoriál  |
   |----------|----------|
   | Vývoj nové aplikace        |[RazorStránky s Entity Framework Core](xref:data/ef-rp/intro) |
   | Údržba aplikace MVC |[MVC pomocí Entity Framework Core](xref:data/ef-mvc/intro) |

1. Přečtěte si přehled ASP.NET Core [základních](xref:fundamentals/index) součástí, které se vztahují na všechny typy aplikací.

1. Projděte obsah a najděte další témata, která vás zajímají.

&dagger;K dispozici je také [kurz webového rozhraní API, který se řídí zcela v prohlížeči](/learn/modules/build-web-api-net-core), nevyžaduje se žádná místní instalace IDE. Kód běží v prostředí [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) a k testování se používá [curl](https://curl.haxx.se/).

## <a name="migrate-from-net-framework"></a>Migrace z .NET Framework

Referenční příručka pro migraci aplikací ASP.NET na ASP.NET Core najdete v tématu <xref:migration/proper-to-2x/index>.

::: moniker-end

## <a name="how-to-download-a-sample"></a>Jak si stáhnout ukázku

Řada článků a kurzů obsahuje odkazy na vzorový kód.

1. [Stáhněte si soubor zip úložiště ASP.NET](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).
1. Rozbalte soubor *Docs-master.zip*.
1. Adresa URL v ukázkovém odkazu vám pomůže s přechodem do ukázkového adresáře.

### <a name="preprocessor-directives-in-sample-code"></a>Direktivy preprocesoru ve vzorovém kódu

Aby bylo možné předvést více scénářů, `#define` ukázkové `#if-#else/#elif-#endif` aplikace používají direktivy preprocesoru a k selektivní kompilaci a spouštění různých sekcí ukázkového kódu. Pro tyto ukázky, které využívají tento přístup, nastavte `#define` direktivu v horní části souborů C# k definování symbolu přidruženého ke scénáři, který chcete spustit. Některé ukázky vyžadují definování symbolu v horní části více souborů, aby bylo možné spustit scénář.

Například následující seznam symbolů `#define` udává, že jsou dostupné čtyři scénáře (jeden scénář na symbol). Při aktuální konfiguraci ukázky se spustí scénář `TemplateCode`:

```csharp
#define TemplateCode // or LogFromMain or ExpandDefault or FilterInCode
```

Pokud chcete ukázku změnit tak, aby se spustil scénář `ExpandDefault`, definujte symbol `ExpandDefault` a zbývající symboly nechejte zakomentované:

```csharp
#define ExpandDefault // TemplateCode or LogFromMain or FilterInCode
```

Další informace o používání [direktiv preprocesoru C#](/dotnet/csharp/language-reference/preprocessor-directives/) k selektivní kompilaci částí kódu najdete v článku [#define (referenční dokumentace jazyka C# )](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) a [#if (referenční dokumentace jazyka C#)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).

### <a name="regions-in-sample-code"></a>Oblasti ve vzorovém kódu

Některé ukázkové aplikace obsahují oddíly kódu, které jsou obklopeny [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) a [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) direktivami jazyka C#. Systém pro sestavování dokumentace vkládá tyto oblasti do zobrazených témat dokumentace.  

Názvy oblastí obvykle obsahují slovo „snippet“. Následující příklad ukazuje oblast s názvem `snippet_WebHostDefaults`:

```csharp
#region snippet_WebHostDefaults
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
    });
#endregion
```

Na předchozí fragment kódu C# odkazuje soubor Markdown tohoto tématu pomocí následujícího řádku:

```md
[!code-csharp[](sample/SampleApp/Program.cs?name=snippet_WebHostDefaults)]
```

Můžete bezpečně ignorovat (nebo odebrat) direktivy `#region` a `#endregion` , které obklopují kód. Pokud plánujete spustit ukázkové scénáře popsané v tématu, neměňte kód v těchto direktivách. Při experimentování s jinými scénáři můžete tento kód klidně pozměnit.

Další informace najdete v článku [Příspěvky k dokumentaci rozhraní ASP.NET: fragmenty kódu](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících materiálech:

* <xref:getting-started>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [Základy ASP.NET Core](xref:fundamentals/index)
* [Týdenní přehled novinek v komunitě ASP.NET](https://live.asp.net/) se zabývá pokrokem v týmových projektech a týmovými plány. Nabízí nové blogové příspěvky a software třetích stran.
