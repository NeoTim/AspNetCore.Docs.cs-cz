---
title: Úvod do ASP.NET Core
author: rick-anderson
description: Nechte si představit ASP.NET Core, což je platformově univerzální, vysoce výkonná architektura typu open-source, která slouží k vytváření moderních cloudových aplikací připojených k internetu.
ms.author: riande
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- SignalR
uid: index
ms.openlocfilehash: fd7fa9dd70502f51222e457dd887ef668d377278
ms.sourcegitcommit: 4b166b49ec557a03f99f872dd069ca5e56faa524
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366657"
---
# <a name="introduction-to-aspnet-core"></a>Úvod do ASP.NET Core

Autoři: [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT) a [Shaun Luttin](https://twitter.com/dicshaunary)

ASP.NET Core je platformově univerzální, vysoce výkonná architektura typu [open-source](https://github.com/aspnet/home), která slouží k vytváření moderních cloudových aplikací připojených k internetu. S ASP.NET Core můžete:

* Vytvářet webové aplikace a služby, aplikace [IoT](https://www.microsoft.com/internet-of-things/) a mobilní back-endy.
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

ASP.NET Core se hladce integruje s oblíbenými architekturami a knihovnami na straně klienta, včetně [Blazor](xref:blazor/index), [úhlů](xref:spa/angular), [reakce](xref:spa/react)a [zavedení](https://getbootstrap.com/). Další informace najdete v tématu <xref:blazor/index> a související témata týkající se *vývoje na straně klienta*.

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a>Cílení ASP.NET Core na .NET Framework

Cílem ASP.NET Core 2.x může být .NET Core nebo .NET Framework. Aplikace ASP.NET Core, jejichž cílem je .NET Framework, nejsou multiplatformní, ale běží jen ve Windows. ASP.NET Core 2.x se obecně skládá z knihoven [.NET Standard](/dotnet/standard/net-standard). Knihovny napsané pomocí .NET Standard 2,0 běží na libovolné [platformě .NET, která implementuje .NET Standard 2,0](/dotnet/standard/net-standard#net-implementation-support).

ASP.NET Core 2. x se podporuje v .NET Framework verzích, které implementují .NET Standard 2,0:

* Důrazně doporučujeme .NET Framework nejnovější verzi.
* .NET Framework 4.6.1 a novější.

ASP.NET Core 3.0 a novější poběží jenom na platformě .NET Core. Další podrobnosti týkající se této změny najdete v tématu [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/) (První pohled na změny, které přináší ASP.NET Core 3.0).

Cílení na .NET Core má několik výhod, které přibývají s každou vydanou verzí. Mezi výhody .NET Core oproti .NET Framework patří:

* Mutliplatformní: Běží v systémech macOS, Linux a Windows.
* Vyšší výkon
* [Souběžná Správa verzí](/dotnet/standard/choosing-core-framework-server#a-need-for-side-by-side-of-net-versions-per-application-level)
* Nová rozhraní API
* Open source

Ze všech sil se snažíme doplnit rozhraní API z .NET Framework do .NET Core. Sada [Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) zpřístupnila v .NET Core tisíce rozhraní API, která byla určena jen pro Windows. Tato rozhraní API nebyla v .NET Core 1.x dostupná.

## <a name="recommended-learning-path"></a>Doporučený studijní program

Jako úvod do vývoje aplikací ASP.NET Core doporučujeme následující posloupnost kurzů a článků:

1. Postupujte podle kurzu pro typ aplikace, kterou chcete vyvíjet nebo udržovat:

   |Typ aplikace  |Scénář  |Kurz  |
   |----------|----------|----------|
   |Webová aplikace                   | Vývoj nové aplikace        |[Začínáme se stránkami Razor](xref:tutorials/razor-pages/razor-pages-start) |
   |Webová aplikace                   | Údržba aplikace MVC |[Začínáme s MVC](xref:tutorials/first-mvc-app/start-mvc)|
   |Webové rozhraní API                   |                            |[Vytvoření webového rozhraní API](xref:tutorials/first-web-api)\*  |
   |Aplikace v reálném čase             |                            |[Začínáme s funkcí SignalR](xref:tutorials/signalr) |
   |Aplikace Blazor                |                            |[Začínáme s Blazor](xref:blazor/get-started) |
   |Aplikace vzdáleného volání procedur |                            |[Začínáme se službou gRPC](xref:tutorials/grpc/grpc-start) |

1. Postupujte podle kurzu, který popisuje základní přístup k datům:

   |Scénář  |Kurz  |
   |----------|----------|
   | Vývoj nové aplikace        |[Razor Pages pomocí Entity Framework Core](xref:data/ef-rp/intro) |
   | Údržba aplikace MVC |[MVC pomocí Entity Framework Core](xref:data/ef-mvc/intro)

1. Podívejte se na přehled funkcí ASP.NET Core, které se vztahují na všechny typy aplikací:

   * [Základy](xref:fundamentals/index)

1. Projděte obsah a najděte další témata, která vás zajímají.

\* K dispozici je nový [kurz webového rozhraní API, který můžete kompletně absolvovat v prohlížeči](https://docs.microsoft.com/learn/modules/build-web-api-net-core) bez nutnosti instalace integrovaného vývojového prostředí.  Kód běží v prostředí [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) a k testování se používá [curl](https://curl.haxx.se/).

## <a name="migration-from-the-net-framework"></a>Migrace z .NET Framework

Referenční příručka pro migraci aplikací ASP.NET na ASP.NET Core najdete v tématu <xref:migration/proper-to-2x/index>.

## <a name="how-to-download-a-sample"></a>Jak si stáhnout ukázku

Řada článků a kurzů obsahuje odkazy na vzorový kód.

1. [Stáhněte si soubor zip úložiště ASP.NET](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).
1. Rozbalte soubor *Docs-master.zip*.
1. Adresa URL v ukázkovém odkazu vám pomůže s přechodem do ukázkového adresáře.

### <a name="preprocessor-directives-in-sample-code"></a>Direktivy preprocesoru ve vzorovém kódu

Aby bylo možné předvést více scénářů, ukázkové aplikace používají direktivy preprocesoru `#define` a `#if-#else/#elif-#endif` k selektivní kompilaci a spouštění různých sekcí vzorového kódu. U vzorků, které využívají tento přístup, nastavte direktivu `#define` v horní části C# souborů k definování symbolu přidruženého ke scénáři, který chcete spustit. Některé ukázky vyžadují definování symbolu v horní části více souborů, aby bylo možné spustit scénář.

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

Některé ukázkové aplikace obsahují oddíly kódu, které jsou obklopené direktivami [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) a [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) C# . Systém pro sestavování dokumentace vkládá tyto oblasti do zobrazených témat dokumentace.  

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

Můžete bezpečně ignorovat (nebo odebrat) direktivy `#region` a `#endregion`, které obklopují kód. Pokud plánujete spustit ukázkové scénáře popsané v tématu, neměňte kód v těchto direktivách. Při experimentování s jinými scénáři můžete tento kód klidně pozměnit.

Další informace najdete v článku [Příspěvky k dokumentaci rozhraní ASP.NET: fragmenty kódu](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).

## <a name="next-steps"></a>Další kroky

Další informace naleznete v následujících zdrojích:

* <xref:getting-started>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [Základy ASP.NET Core](xref:fundamentals/index)
* [Týdenní přehled novinek v komunitě ASP.NET](https://live.asp.net/) se zabývá pokrokem v týmových projektech a týmovými plány. Nabízí nové blogové příspěvky a software třetích stran.
