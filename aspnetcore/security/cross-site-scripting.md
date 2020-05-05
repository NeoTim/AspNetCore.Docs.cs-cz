---
title: Zabránit skriptování mezi weby (XSS) v ASP.NET Core
author: rick-anderson
description: Přečtěte si o skriptování mezi weby (XSS) a techniky pro řešení této chyby zabezpečení v aplikaci ASP.NET Core.
ms.author: riande
ms.date: 10/02/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/cross-site-scripting
ms.openlocfilehash: 5a14042db6250d5f7a47acaf4083b44272c606ab
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777485"
---
# <a name="prevent-cross-site-scripting-xss-in-aspnet-core"></a>Zabránit skriptování mezi weby (XSS) v ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Skriptování mezi weby (XSS) je ohrožení zabezpečení, které umožňuje útočníkovi umístit skripty na straně klienta (obvykle JavaScript) do webových stránek. Když ostatní uživatelé načtou ovlivněné stránky, spustí se skripty útočníka, což útočníkovi umožní ukrást soubory cookie a tokeny relace, změnit obsah webové stránky prostřednictvím manipulace s modelem DOM nebo přesměrovat prohlížeč na jinou stránku. K ohrožení zabezpečení XSS obvykle dochází, když aplikace přebírá uživatelem vstup a výstupuje na stránku bez ověřování, kódování nebo uvozovacího uvozovacího prvku.

## <a name="protecting-your-application-against-xss"></a>Ochrana aplikace proti XSS

Na základní úrovni technologie XSS funguje tak, že se podíváme na `<script>` vaši aplikaci na vložení značky na vykreslenou stránku nebo `On*` vložením události do prvku. Vývojáři by měli pomocí následujících kroků prevence zabránit zavlečení SKRIPTOVÁNÍ do své aplikace.

1. Nikdy neumísťujte nedůvěryhodná data do vstupu ve formátu HTML, pokud nepoužijete zbývající část následujících kroků. Nedůvěryhodná data jsou všechna data, která může být ovládána útočníkem, vstupy formulářů HTML, řetězce dotazů, hlavičky protokolu HTTP, dokonce i data, která jsou zdrojem tohoto útočníka, může být, že by mohlo dojít k porušení zabezpečení vaší databáze, i když nemůžou porušit vaši aplikaci.

2. Před vložením nedůvěryhodných dat do elementu HTML zajistěte, aby byl kódovaný HTML. Kódování HTML přebírá znaky jako &lt; a mění je v bezpečné podobě, jako &amp;je lt;

3. Před vložením nedůvěryhodných dat do atributu HTML zajistěte, aby byl kódovaný HTML. Kódování atributů HTML je nadmnožinou kódování HTML a kóduje další znaky, jako například "a".

4. Před vložením nedůvěryhodných dat do JavaScriptu umístěte data v elementu HTML, jehož obsah načítáte za běhu. Pokud to není možné, zajistěte, aby byla data kódovaná pomocí JavaScriptu. Kódování JavaScriptu přebírá nebezpečné znaky pro JavaScript a nahrazuje je šestnáctkově, například &lt; by bylo zakódováno `\u003C`jako.

5. Před vložením nedůvěryhodných dat do řetězce dotazu URL zajistěte, aby byla zakódovaná adresa URL.

## <a name="html-encoding-using-razor"></a>Kódování HTML pomocíRazor

Razor Modul používaný v MVC automaticky zakóduje všechny výstupy zdroje z proměnných, pokud nepracujete opravdu k tomu, aby se tak zabránilo. Používá pravidla kódování atributů HTML vždy, když použijete *@* směrnici. Kódování atributu HTML je nadmnožinou kódování HTML to znamená, že se nemusíte zabývat sami, ať už používáte kódování HTML nebo kódování atributů HTML. Je nutné, abyste měli jistotu, že budete používat pouze @ v kontextu jazyka HTML, nikoli při pokusu o vložení nedůvěryhodného vstupu přímo do JavaScriptu. Pomocník značek také zakóduje vstup, který použijete v parametrech značek.

Proveďte následující Razor zobrazení:

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

Toto zobrazení vypíše obsah proměnné *untrustedInput* . Tato proměnná zahrnuje některé znaky, které jsou používány v útocích XSS, &lt;konkrétně a &gt;. Prozkoumání zdroje zobrazuje Vykreslený výstup kódovaný jako:

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> ASP.NET Core MVC poskytuje `HtmlString` třídu, která není automaticky kódována při výstupu. Tato akce by se nikdy neměla používat v kombinaci s nedůvěryhodným vstupem, protože to vystavuje chybu zabezpečení XSS.

## <a name="javascript-encoding-using-razor"></a>Kódování JavaScriptu pomocíRazor

Může nastat situace, kdy budete chtít vložit hodnotu do JavaScriptu pro zpracování v zobrazení. Můžete to provést dvěma způsoby. Nejbezpečnější způsob, jak vkládat hodnoty, je umístit hodnotu do atributu data tagu a načíst ho v JavaScriptu. Příklad:

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   <div
       id="injectedData"
       data-untrustedinput="@untrustedInput" />

   <script>
     var injectedData = document.getElementById("injectedData");

     // All clients
     var clientSideUntrustedInputOldStyle =
         injectedData.getAttribute("data-untrustedinput");

     // HTML 5 clients only
     var clientSideUntrustedInputHtml5 =
         injectedData.dataset.untrustedinput;

     document.write(clientSideUntrustedInputOldStyle);
     document.write("<br />")
     document.write(clientSideUntrustedInputHtml5);
   </script>
   ```

Tím se vytvoří následující kód HTML

```html
<div
     id="injectedData"
     data-untrustedinput="&lt;&quot;123&quot;&gt;" />

   <script>
     var injectedData = document.getElementById("injectedData");

     var clientSideUntrustedInputOldStyle =
         injectedData.getAttribute("data-untrustedinput");

     var clientSideUntrustedInputHtml5 =
         injectedData.dataset.untrustedinput;

     document.write(clientSideUntrustedInputOldStyle);
     document.write("<br />")
     document.write(clientSideUntrustedInputHtml5);
   </script>
   ```

V takovém případě se při spuštění vykreslí následující:

```
<"123">
   <"123">
```

JavaScriptový kodér můžete také volat přímo:

```cshtml
@using System.Text.Encodings.Web;
   @inject JavaScriptEncoder encoder;

   @{
       var untrustedInput = "<\"123\">";
   }

   <script>
       document.write("@encoder.Encode(untrustedInput)");
   </script>
```

Vykreslí se v prohlížeči následujícím způsobem:

```html
<script>
    document.write("\u003C\u0022123\u0022\u003E");
</script>
```

>[!WARNING]
> Nezřetězení nedůvěryhodného vstupu v JavaScriptu pro vytváření elementů DOM. Měli byste použít `createElement()` a přiřadit hodnoty vlastností odpovídajícím způsobem `node.TextContent=`, jako je `element.SetAttribute()` / `element[attribute]=` , nebo jinak můžete vystavit sami sobě pomocí skriptování na základě modelu DOM.

## <a name="accessing-encoders-in-code"></a>Přístup k kodérům v kódu

Kodéry HTML, JavaScript a URL jsou pro váš kód k dispozici dvěma způsoby, můžete je vložit prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection) nebo můžete použít výchozí kodéry obsažené v `System.Text.Encodings.Web` oboru názvů. Pokud použijete výchozí kodéry, pak všechny použité pro rozsahy znaků, které se mají považovat za bezpečné, se neprojeví – Výchozí kodéry použijí nejbezpečnější pravidla kódování.

Chcete-li použít konfigurovatelné kodéry přes DI, vaše konstruktory by měly podle potřeby přebírat parametr *HtmlEncode*, *JavaScriptEncoder* a *UrlEncoder* . Například;

```csharp
public class HomeController : Controller
   {
       HtmlEncoder _htmlEncoder;
       JavaScriptEncoder _javaScriptEncoder;
       UrlEncoder _urlEncoder;

       public HomeController(HtmlEncoder htmlEncoder,
                             JavaScriptEncoder javascriptEncoder,
                             UrlEncoder urlEncoder)
       {
           _htmlEncoder = htmlEncoder;
           _javaScriptEncoder = javascriptEncoder;
           _urlEncoder = urlEncoder;
       }
   }
   ```

## <a name="encoding-url-parameters"></a>Kódování parametrů adresy URL

Pokud chcete vytvořit řetězec dotazu URL s nedůvěryhodným vstupem jako hodnotu, použijte `UrlEncoder` ke kódování hodnoty. Například:

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

Po kódování bude proměnná encodedValue obsahovat `%22Quoted%20Value%20with%20spaces%20and%20%26%22`. Mezery, uvozovky, interpunkční znaménka a další nebezpečné znaky budou v procentech zakódovány na jejich hexadecimální hodnotu, například znak mezery se stane %20.

>[!WARNING]
> Nepoužívejte nedůvěryhodný vstup jako součást cesty URL. Vždy předejte nedůvěryhodný vstup jako hodnotu řetězce dotazu.

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a>Přizpůsobení kodérů

Ve výchozím nastavení používají kodéry zabezpečený seznam omezený na základní rozsah Latinské sady Latin Unicode a zakódovat všechny znaky mimo tento rozsah jako jejich ekvivalenty kódu znaku. Toto chování má vliv Razor také na Taghelperu a HtmlHelper vykreslování, protože bude používat kodéry k výstupu řetězců.

Důvodem je ochrana proti neznámým nebo budoucím chybám prohlížeče (předchozí chyby prohlížeče mají Trip analýzu na základě zpracování neanglických znaků). Pokud váš web používá velké množství znaků mimo Latin, jako je čínština, cyrilice nebo jiné, pravděpodobně to není chování, které požadujete.

Seznam bezpečných seznamů pro kodér můžete přizpůsobit tak, aby zahrnoval rozsahy Unicode, které jsou vhodné `ConfigureServices()`pro vaši aplikaci během spouštění, v.

Například pomocí výchozí konfigurace můžete použít Razor HtmlHelper jako tak,

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

Po zobrazení zdroje webové stránky uvidíte, že byla vykreslena takto, s kódovaným čínským textem;

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

Chcete-li rozšířit znaky zpracovávané jako bezpečné kodérem, vložte následující řádek do `ConfigureServices()` metody v. `startup.cs`

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

Tento příklad rozšiřuje seznam bezpečných dat tak, aby zahrnoval CjkUnifiedIdeographs rozsah znaků Unicode. Vykreslený výstup by se teď stal

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

Bezpečné rozsahy seznamů jsou zadány jako grafy kódu Unicode, nikoli jazyky. [Standard Unicode](https://unicode.org/) obsahuje seznam [kódových grafů](https://www.unicode.org/charts/index.html) , pomocí kterých můžete najít graf obsahující vaše znaky. Každý kodér, HTML, JavaScript a adresu URL je nutné nakonfigurovat samostatně.

> [!NOTE]
> Přizpůsobení seznamu bezpečný má vliv pouze na kodéry, které jsou zdrojem přes DI. Pokud přímo přistupujete k kodéru přes `System.Text.Encodings.Web.*Encoder.Default` výchozí nastavení, použije se jenom základní Safelist s latinkou.

## <a name="where-should-encoding-take-place"></a>Kde má být kódování provedeno?

Obecně přijatý postup spočívá v tom, že kódování probíhá na místě výstupu a kódované hodnoty by nikdy neměly být uloženy v databázi. Kódování v bodě výstupu umožňuje změnit použití dat, například z formátu HTML na hodnotu řetězce dotazu. Umožňuje také snadné vyhledávání dat bez nutnosti kódování hodnot před vyhledáváním a umožňuje využít všechny změny nebo opravy chyb provedené v kodérech.

## <a name="validation-as-an-xss-prevention-technique"></a>Ověřování jako technika ochrany XSS

Ověřování může být užitečným nástrojem v omezení útoků XSS. Například číselný řetězec obsahující pouze znaky 0-9 nespustí Útok XSS. Při přijímání HTML při vstupu uživatele se ověřování bude složitější. Analýza vstupu HTML je obtížná, pokud není možná. Markdownu společně s analyzátorem, který odděluje vložený kód HTML, je bezpečnější možností pro přijetí formátovaného vstupu. Nikdy nespoléhat jenom na ověřování. Vždy kódování nedůvěryhodného vstupu před výstupem bez ohledu na to, jaké ověření nebo úpravu bylo provedeno.
