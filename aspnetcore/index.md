---
title: Úvod do ASP.NET Core
author: rick-anderson
description: Nechte si představit ASP.NET Core, což je platformově univerzální, vysoce výkonná architektura typu open-source, která slouží k vytváření moderních cloudových aplikací připojených k internetu.
ms.author: riande
ms.custom: mvc
ms.date: 02/13/2019
uid: index
ms.openlocfilehash: c3f07814bfab19a0f070e0b48b0d2ef6cfc1594e
ms.sourcegitcommit: 6ba5fb1fd0b7f9a6a79085b0ef56206e462094b7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56248157"
---
# <a name="introduction-to-aspnet-core"></a>Úvod do ASP.NET Core

Autoři: [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT) a [Shaun Luttin](https://twitter.com/dicshaunary)

ASP.NET Core je platformově univerzální, vysoce výkonná architektura typu [open-source](https://github.com/aspnet/home), která slouží k vytváření moderních cloudových aplikací připojených k internetu. S ASP.NET Core můžete:

* Vytvářet webové aplikace a služby, aplikace [IoT](https://www.microsoft.com/internet-of-things/) a mobilní back-endy.
* Používat oblíbené vývojářské nástroje v systémech Windows, Mac OS a Linux.
* Nasazovat v cloudu nebo místně
* Používat ke spuštění [.NET Core nebo .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).

## <a name="why-use-aspnet-core"></a>Proč používat ASP.NET Core?

Miliony vývojářů už dříve k vytváření webových aplikací používali [ASP.NET 4.x](/aspnet/overview) (což bude platit i nadále). ASP.NET Core je přepracovanou verzí ASP.NET 4.x se změněnou architekturou. Výsledkem je odlehčené a modulárnější prostředí.

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

Architektura ASP.NET Core se hladce integruje do oblíbených klientských architektur a knihoven, včetně těchto: [komponenty Razor](xref:razor-components/index), [Angular](xref:spa/angular), [React](xref:spa/react) a [Bootstrap](https://getbootstrap.com/). Další informace najdete v tématu věnovaném [komponentám Razor](xref:razor-components/index) a v souvisejících tématech v části *Vývoj na straně klienta*.

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a>Cílení ASP.NET Core na .NET Framework

Cílem ASP.NET Core 2.x může být .NET Core nebo .NET Framework. Aplikace ASP.NET Core, jejichž cílem je .NET Framework, nejsou multiplatformní, ale běží jen ve Windows. ASP.NET Core 2.x se obecně skládá z knihoven [.NET Standard](/dotnet/standard/net-standard). Aplikace vytvořené pomocí .NET Standard 2.0 běží všude, kde se podporuje .NET Standard 2.0.

ASP.NET Core 2.x se podporuje ve verzích .NET Framework kompatibilních s .NET Standard 2.0:

* Důrazně se doporučuje .NET Framework 4.7.1 a novější.
* .NET Framework 4.6.1 a novější.

ASP.NET Core 3.0 a novější poběží jenom na platformě .NET Core. Další podrobnosti týkající se této změny najdete v tématu [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/) (První pohled na změny, které přináší ASP.NET Core 3.0).

Cílení na .NET Core má několik výhod, které přibývají s každou vydanou verzí. Mezi výhody .NET Core oproti .NET Framework patří:

* Mutliplatformní: Běží v systémech macOS, Linux a Windows.
* Vyšší výkon
* Souběžná správa verzí
* Nová rozhraní API
* Open source

Ze všech sil se snažíme doplnit rozhraní API z .NET Framework do .NET Core. Sada [Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) zpřístupnila v .NET Core tisíce rozhraní API, která byla určena jen pro Windows. Tato rozhraní API nebyla v .NET Core 1.x dostupná.

## <a name="how-to-download-a-sample"></a>Jak si stáhnout ukázku

Řada článků a kurzů obsahuje odkazy na vzorový kód.

1. [Stáhněte si soubor zip úložiště ASP.NET](https://codeload.github.com/aspnet/Docs/zip/master).
1. Rozbalte soubor *Docs-master.zip*.
1. Adresa URL v ukázkovém odkazu vám pomůže s přechodem do ukázkového adresáře.

### <a name="preprocessor-directives-in-sample-code"></a>Direktivy preprocesoru ve vzorovém kódu

Pro účely demonstrace různých scénářů využívají ukázkové aplikace příkazy `#define` a `#if-#else/#elif-#endif` jazyka C# k selektivní kompilaci a spouštění různých částí vzorového kódu. U ukázek, které používají tento přístup, nastavte příkaz `#define` na začátku souborů C# na symbol přidružený ke scénáři, který chcete spustit. U některých ukázek musíte kvůli spuštění scénáře nastavit tento symbol na začátku několika souborů.

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

Některé ukázkové aplikace obsahují úseky kódu, které jsou uzavřené mezi příkazy jazyka C# [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) a [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion). Systém pro sestavování dokumentace vkládá tyto oblasti do zobrazených témat dokumentace.  

Názvy oblastí obvykle obsahují slovo „snippet“. Následující příklad ukazuje oblast s názvem `snippet_FilterInCode`:

```csharp
#region snippet_FilterInCode
WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
        logging.AddFilter("System", LogLevel.Debug)
            .AddFilter<DebugLoggerProvider>("Microsoft", LogLevel.Trace))
            .Build();
#endregion
```

Na předchozí fragment kódu C# odkazuje soubor Markdown tohoto tématu pomocí následujícího řádku:

```
[!code-csharp[](sample/SampleApp/Program.cs?name=snippet_FilterInCode)]
```

Příkazy `#region` a `#endregion`, do kterých je kód uzavřený, můžete bezpečně ignorovat. Pokud budete chtít spustit vzorové scénáře popsané v tomto tématu, neměňte kód uvnitř těchto příkazů. Při experimentování s jinými scénáři můžete tento kód klidně pozměnit.

Další informace najdete v článku [Příspěvky k dokumentaci rozhraní ASP.NET: fragmenty kódu](https://github.com/aspnet/Docs/blob/master/CONTRIBUTING.md#code-snippets).

## <a name="next-steps"></a>Další kroky

Další informace naleznete v následujících materiálech:

* [Začínáme se stránkami Razor](xref:tutorials/razor-pages/razor-pages-start)
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [Základy ASP.NET Core](xref:fundamentals/index)
* [Týdenní přehled novinek v komunitě ASP.NET](https://live.asp.net/) se zabývá pokrokem v týmových projektech a týmovými plány. Nabízí nové blogové příspěvky a software třetích stran.
