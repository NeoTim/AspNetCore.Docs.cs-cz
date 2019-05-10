---
title: Zabránit webů skriptování mezi weby (XSS) v ASP.NET Core
author: rick-anderson
description: Další informace o skriptování mezi weby (XSS) a techniky pro řešení tohoto ohrožení zabezpečení v aplikaci ASP.NET Core.
ms.author: riande
ms.date: 10/02/2018
uid: security/cross-site-scripting
ms.openlocfilehash: 50f0211a2c64708d9b788dd10ce9064e66014d55
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64901956"
---
# <a name="prevent-cross-site-scripting-xss-in-aspnet-core"></a>Zabránit webů skriptování mezi weby (XSS) v ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

Skriptování mezi weby (XSS) je ohrožení zabezpečení, která umožňuje útočníkovi umístí skripty na straně klienta (obvykle JavaScriptu) do webových stránek. Když ostatní uživatelé načíst ovlivněné stránek, které budou spuštěny skripty útočníka, umožňuje útočníkovi krádež souborů cookie a relace tokeny, změňte obsah webové stránky pomocí manipulace s modelem DOM nebo přesměrovat prohlížeč na jinou stránku. Ohrožení zabezpečení XSS obecně dojít, když aplikace přijímá vstup uživatele a uloží jej na stránku bez ověřování, kódování nebo ho uvozovací znaky.

## <a name="protecting-your-application-against-xss"></a>Ochrana aplikace proti skriptování mezi servery

Na základní úrovni XSS funguje tak, přičemž aplikace do vkládání `<script>` značky do vykreslované stránky nebo vložením `On*` událostí do elementu. Vývojáři by měl následujícím postupem ochrany před únikem informací pro Vyhýbejte XSS do své aplikace.

1. Nikdy nepoužili nedůvěryhodná data váš vstup ve formátu HTML, pokud postupujte podle zbývajících pokynů. Nedůvěryhodná data jsou všechna data, která mohou být řízena útočník, vstupy formuláře HTML, řetězce dotazů, hlavičky protokolu HTTP, dokonce i data source z databáze, protože útočník může být schopni porušení zabezpečení databáze, i v případě, že se nemůže pronikají do vaší aplikace.

2. Před přepnutím nedůvěryhodná data uvnitř elementu HTML Ujistěte se, že je kódováno jazykem HTML. Kódování HTML, jako má znaků &lt; a změny do bezpečného formuláře jako &amp;lt;

3. Před uvedením nedůvěryhodná data do atributu HTML Ujistěte se, že je kódováno jazykem HTML. Kódování atributu HTML je nadstavbou jazyka kódování HTML a další znaky zakóduje jako "a".

4. Před přepnutím nedůvěryhodná data do jazyka JavaScript umístíte data v elementu HTML, jehož obsah načíst za běhu. Pokud to není možné, pak Ujistěte se, že data jsou kódované jazyka JavaScript. Kódování JavaScript trvá nebezpečné znaky pro JavaScript a nahradí je jejich hex, například &lt; by být zakódován jako `\u003C`.

5. Před přepnutím nedůvěryhodná data do řetězce dotazu adresy URL Ujistěte se, že je kódování URL.

## <a name="html-encoding-using-razor"></a>Kódování HTML pomocí syntaxe Razor

Modul Razor použité v MVC automaticky kóduje všechny výstupní zdrojem proměnné, pokud pracujete ve skutečnosti intenzivně zabránit, aby ji uděláte. Použije pravidla kódování atributu HTML při každém použití *@* směrnice. Ve formátu HTML kódování atributu je nadstavbou jazyka kódování HTML, to znamená, že nemáte problém sami se určuje, zda by měl používat kódování HTML nebo kódování atributu HTML. Musíte zajistit, že používáte pouze v kontextu HTML, ne už při pokusu o vložení nedůvěryhodný vstup přímo do jazyka JavaScript. Pomocné rutiny značek se také kódování vstup, který použijete v parametrů tag.

Proveďte následující zobrazení Razor:

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

Toto zobrazení vypíše obsah *untrustedInput* proměnné. Tato proměnná obsahuje některé znaky, které se používají v útoky XSS, konkrétně &lt;, "a &gt;. Zkoumání zdroj ukazuje vykresleného výstupu zakódován jako:

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> ASP.NET Core MVC nabízí `HtmlString` třídy, který není kódován automaticky při výstupu. To byste nikdy neměli používat v kombinaci s nedůvěryhodnému vstupu jako to bude vystavovat chybu XSS.

## <a name="javascript-encoding-using-razor"></a>JavaScript kódování pomocí syntaxe Razor

Může nastat situace, které chcete vložit do jazyka JavaScript ke zpracování v zobrazení hodnotu. Existují dva způsoby, jak to provést. Nejbezpečnější způsob, jak vložit hodnoty je hodnota atributu data značky a načíst v JavaScript. Příklad:

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

To vytvoří následující kód HTML

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

Který při spuštění, zobrazí se pak následující:

```none
<"123">
   <"123">
   ```

Kodér JavaScript můžete také volat přímo:

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

To bude vykreslení v prohlížeči následujícím způsobem:

```html
<script>
       document.write("\u003C\u0022123\u0022\u003E");
   </script>
   ```

>[!WARNING]
> Nedůvěryhodný vstup v jazyce JavaScript, k vytváření prvků modelu DOM není zřetězit. Měli byste použít `createElement()` a odpovídajícím způsobem, jako přiřadit hodnoty vlastností `node.TextContent=`, nebo použijte `element.SetAttribute()` / `element[attribute]=` jinak zpřístupníte sami na základě modelu DOM XSS.

## <a name="accessing-encoders-in-code"></a>Přístup k kodérů v kódu

Jsou k dispozici dvě možnosti, jak váš kód kodérů HTML, JavaScript a adresu URL, můžete vložit pomocí [injektáž závislostí](xref:fundamentals/dependency-injection) nebo můžete použít výchozí kodérů součástí `System.Text.Encodings.Web` oboru názvů. Pokud používáte výchozí kodérů a veškeré použité k rozsahů znaků považovány za bezpečné projeví – výchozí kodérů použijte nejbezpečnější pravidla kódování, je to možné.

Použití konfigurovatelné kodérů prostřednictvím DI zabere vaše konstruktory *HtmlEncoder*, *JavaScriptEncoder* a *UrlEncoder* parametr podle potřeby. Například;

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

## <a name="encoding-url-parameters"></a>Kódování adresy URL parametry

Pokud chcete sestavit řetězec dotazu adresy URL s nedůvěryhodný vstup jako hodnotu použít `UrlEncoder` ke kódování hodnotu. Například

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

Po kódování encodedValue bude obsahovat proměnnou `%22Quoted%20Value%20with%20spaces%20and%20%26%22`. Mezery, nabídek, interpunkce a dalších problematické znaky budou procenta kódovány za účelem jejich šestnáctkové hodnoty, například znak mezery se stanou % 20.

>[!WARNING]
> Nepoužívejte nedůvěryhodnému vstupu jako část cesty adresy URL. Vždycky předáte nedůvěryhodný vstup jako hodnotu řetězce dotazu.

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a>Přizpůsobení u kodérů

Ve výchozím nastavení kodérů pomocí seznamu bezpečných omezeno na rozsahu základní latinky Unicode a kódování všechny znaky mimo tento rozsah jako jejich ekvivalenty kód znaku. Toto chování Taghelperu Razor a HtmlHelper vykreslování ovlivní také, jak se bude používat u kodérů pro výstupní vaše řetězce.

Zdůvodnění to je pro ochranu před chybami neznámý nebo budoucí prohlížeče (předchozí chyby prohlížeče mít zasekne analýzy založené na zpracování jiných než anglických znaků). Pokud vaše webová stránka značně používá jiné znaky než latinku, jako je například čínština, cyrilice, nebo jinými toto není pravděpodobně chování, které chcete.

Můžete přizpůsobit seznamy bezpečných kodér zahrnout rozsahy vhodnými pro vaši aplikaci při spuštění v kódování Unicode `ConfigureServices()`.

Například pomocí výchozí konfigurace můžete použít syntaxi Razor HtmlHelper takto;

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

Po zobrazení zdrojového kódu webové stránky uvidíte, že má se vykreslí následujícím způsobem čínštině kódování;

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

Chcete-li rozšířit znaky považované za bezpečné kodér by vložíte následující řádek do `ConfigureServices()` metoda `startup.cs`;

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

Tento příklad rozšiřuje seznamu bezpečných zahrnout CjkUnifiedIdeographs rozsah Unicode. Teď už vykresleného výstupu

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

Seznamu bezpečných rozsahy jsou zadané jako grafy kódu Unicode, ne jazyky. [Unicode standard](http://unicode.org/) má seznam [kódu grafy](http://www.unicode.org/charts/index.html) můžete použít k vyhledání graf obsahující znaky. Každý kodér, Html, JavaScript a adresu Url, musí být nakonfigurované samostatně.

> [!NOTE]
> Přizpůsobení seznamu bezpečných ovlivňuje pouze Source prostřednictvím DI kodérů. Pokud přímý přístup k kodéru prostřednictvím `System.Text.Encodings.Web.*Encoder.Default` pak výchozí základní latinky se použije pouze safelist.

## <a name="where-should-encoding-take-place"></a>Pokud byste umístit kódování vzít?

Obecné přijme, postup je, že kódování probíhá místě výstup a kódovaného hodnoty by nikdy neměly být uloženy v databázi. Kódování místě výstup vám umožní změnit používání dat, například z HTML na hodnotu řetězce dotazu. Také umožňuje snadno prohledávat svá data bez nutnosti kódování hodnoty před vyhledáváním a umožňuje vám umožní využívat jakékoli změny a opravy chyb. kodérů.

## <a name="validation-as-an-xss-prevention-technique"></a>Ověření jako XSS techniku ochrany před únikem informací

Ověření může být užitečný nástroj v omezení útoky XSS. Například číselných řetězců obsahující pouze znaky 0-9, nebude spustí XSS útoku. Ověření bude složitější, při přijetí HTML vstup uživatele. Analýza elementu input kódu HTML je obtížné, pokud není možné. Markdown, společně se analyzátor, který odstraní vložený HTML, je bezpečnější možnost pro příjem formátovaný vstup. Nikdy spoléhat na ověřovací samostatně. Vždy kódování nedůvěryhodný vstup před výstupu, bez ohledu na to, jaké ověřování nebo čištění pro zadávání byla provedena.
