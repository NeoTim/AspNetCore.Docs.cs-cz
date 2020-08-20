---
title: Testovací komponenty v ASP.NET Core Blazor
author: guardrex
description: Naučte se testovat componments v Blazor aplikacích.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/10/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/test
ms.openlocfilehash: 8a6fa8f25c8209584488fb2578c70e884877d666
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625866"
---
# <a name="test-components-in-aspnet-core-no-locblazor"></a>Testovací komponenty v ASP.NET Core Blazor

[Egil Hansen](https://egilhansen.com/)

Testování je důležitým aspektem budování stabilního a udržovatelného softwaru.

Chcete-li otestovat Blazor součást, je zkoušená *součást* (vyjmout):

* Vykresleno s relevantním vstupem pro test.
* V závislosti na typu provedeného testu může být ovlivněna interakce nebo modifikace. Například lze aktivovat obslužné rutiny událostí, jako například `onclick` událost pro tlačítko.
* Zkontrolováno očekávaných hodnot.

## <a name="test-approaches"></a>Test přístupů

Dva běžné přístupy k testování Blazor komponent jsou komplexní testování (e2e) a testování částí:

* **Testování částí**: [testy jednotek](/dotnet/core/testing/) jsou zapisovány pomocí knihovny testování částí, která poskytuje:
  * Vykreslování součásti.
  * Kontrola výstupu součásti a stavu.
  * Aktivace obslužných rutin událostí a metod životního cyklu.
  * Kontrolní výrazy, které chování komponenty jsou správné.

  [bUnit](https://github.com/egil/bUnit) je příklad knihovny, která umožňuje testování částí Razor komponent.

* **Testování e2e**: spouštěč testů spustí Blazor aplikaci obsahující vyjmutí a automatizuje instanci prohlížeče. Testovací nástroj kontroluje a komunikuje s OŘÍZNUTím prostřednictvím prohlížeče. [Selen](https://github.com/SeleniumHQ/selenium) je příkladem testovacího rozhraní e2e, které lze použít s Blazor aplikacemi.

V testování částí Blazor Razor je zapojena pouze komponenta (/c #). Externí závislosti, například interoperabilita služeb a JS, musí být napsaný. V testování E2E jsou součástí Blazor testu součást a všechna pomocná infrastruktura, včetně šablon stylů CSS, js a DOM a rozhraní API prohlížeče.

*Rozsah testu* popisuje, jak velký je počet testů. Rozsah testu má obvykle vliv na rychlost testů. Testy jednotek se spouštějí v podmnožině subsystémů aplikace a obvykle se spouštějí v milisekundách. Testy E2E, které testují širokou skupinu subsystémů aplikace, můžou dokončit několik sekund. 

Testování částí také poskytuje přístup k instanci řezu, umožňující kontrole a ověření vnitřního stavu komponenty. To obvykle není možné při testování E2E.

V souvislosti s prostředím komponenty musí testy E2E před zahájením ověřování zajistěte, aby byl dosažen očekávaný stav prostředí. V opačném případě se výsledek nepředpokládá. Při testování částí jsou vykreslování funkce vyjmout a životní cyklus testu více integrovány, což zlepšuje stabilitu testu.

E2E testování zahrnuje spouštění více procesů, vstupně-výstupních operací v síti a disku a další činnosti subsystému, které často vedou k nízké spolehlivosti testů. Testy jednotek jsou obvykle izolované z těchto druhů problémů.

Následující tabulka shrnuje rozdíl mezi dvěma testovacími přístupy.

| Schopnost                       | Testování částí                     | E2E testování                             |
| -------------------------------- | -------------------------------- | --------------------------------------- |
| Rozsah testu                       | Blazor jenom součást ( Razor /c #) | Blazor součást ( Razor /c #) s CSS/JS |
| Čas spuštění testu              | Milisekund                     | Sekundy                                 |
| Přístup k instanci komponenty | Ano                              | No                                      |
| Citlivá na prostředí     | No                               | Yes                                     |
| Spolehlivost                      | Spolehlivější                    | Méně spolehlivé                           |

## <a name="choose-the-most-appropriate-test-approach"></a>Volba nejvhodnějšího testovacího přístupu

Při výběru typu testování, který se má provést, vezměte v úvahu scénář. Některé okolnosti jsou popsány v následující tabulce.

| Scénář | Navrhovaný přístup | Poznámky |
| -------- | ------------------ | ------- |
| Součást bez logiky spolupráce JS | Testování částí | Pokud neexistuje žádná závislost na zprostředkovateli komunikace s JS v Blazor komponentě, může být komponenta testována bez přístupu k JS nebo rozhraní API modelu DOM. V tomto scénáři nehrozí žádné nevýhody pro výběr testování částí. |
| Komponenta s jednoduchou logikou spolupráce JS | Testování částí | Je běžné, že komponenty pro dotazování na model DOM nebo spouštějí animace prostřednictvím zprostředkovatele komunikace JS. Testování částí je obvykle upřednostňováno v tomto scénáři, protože je jednoduché k napodobení interakce JS přes <xref:Microsoft.JSInterop.IJSRuntime> rozhraní. |
| Komponenta, která závisí na komplexním kódu JS | Testování částí a samostatné testování JS | Pokud komponenta používá zprostředkovatele komunikace JS pro volání rozsáhlých nebo složitých knihoven JS, ale interakce mezi Blazor knihovnou komponent a js je jednoduchá, pak nejlepší přístup je pravděpodobným způsobem zacházet s knihovnou komponenty a JS nebo s kódem jako dvěma samostatnými částmi a testovat každou jednotlivě. Otestujte Blazor komponentu pomocí knihovny testování částí a otestujte js pomocí knihovny testování js. |
| Komponenta s logikou, která závisí na manipulaci JS modelu DOM v prohlížeči | E2E testování | Je-li funkce komponenty závislá na JS a její manipulace s modelem DOM, ověřte, jak JS, tak i Blazor kód společně v e2e testu. Toto je přístup, který vývojáři architektury povedli Blazor s Blazor logikou vykreslování v prohlížeči, která má pevně spojený kód C# a js. Kód v jazyce C# a JS musí spolupracovat, aby bylo možné správně vykreslovat Blazor komponenty v prohlížeči.
| Komponenta, která závisí na knihovně komponent třetích stran s pevnými závislostmi | E2E testování | Pokud je funkce součásti závislá na knihovně komponent třetí strany, která má pevné závislosti, jako je například zprostředkovatel komunikace JS, může být testování E2E jedinou možností pro otestování komponenty. |

## <a name="test-components-with-bunit"></a>Testování komponent pomocí bUnit

K dispozici není oficiální testovací rozhraní Microsoftu pro Blazor , ale projekt založený na komunitě [bUnit](https://github.com/egil/bUnit) poskytuje pohodlný způsob, jak jednotky testování Blazor částí.

> [!NOTE]
> bUnit je knihovna pro testování třetí strany, kterou Microsoft nepodporuje nebo nespravuje.

bUnit pracuje s rozhraními pro obecné účely testování, jako je [MSTest](/dotnet/core/testing/unit-testing-with-mstest), [nunit](https://nunit.org/)a [xUnit](https://xunit.github.io/). Tyto testovací architektury provedou testy bUnit a vypadají stejně jako běžné testy jednotek. testy bUnit integrované s testovacím rozhraním pro obecné účely jsou obvykle spouštěny pomocí:

* [Průzkumník testů sady Visual Studio](/visualstudio/test/run-unit-tests-with-test-explorer).
* [`dotnet test`](/dotnet/core/tools/dotnet-test) Příkaz CLI v příkazovém prostředí.
* Automatizovaný kanál testování DevOps

> [!NOTE]
> Koncepty testů a testovací implementace v různých testovacích architekturách jsou podobné, ale nejsou totožné. Pokyny najdete v dokumentaci k testovacímu rozhraní.

Následující příklad ukazuje strukturu testu bUnit pro `Counter` komponentu v aplikaci na základě Blazor šablony projektu. `Counter`Komponenta zobrazí a zvýší čítač na základě toho, že uživatel vybere tlačítko na stránce:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

Následující test bUnit ověří, zda je čítač vyjmutí správně zvýšen, když je vybráno tlačítko:

```csharp
[Fact]
public void CounterShouldIncrementWhenSelected()
{
    // Arrange
    using var cxt = new TestContext();
    var cut = ctx.RenderComponent<Counter>();
    var paraElm = cut.Find("p");

    // Act
    cut.Find("button").Click();
    var paraElmText = paraElm.TextContent;

    // Assert
    paraElmText.MarkupMatches("Current count: 1");
}
```

V každém kroku testu probíhají následující akce:

* *Uspořádat*: `Counter` Komponenta se vykresluje pomocí bUnit `TestContext` . `<p>`Byl nalezen a přiřazen element řezu () pro vyjmutí `paraElm` .

* *Act*: element tlačítka ( `<button>` ) se nachází a pak se vybere podle volání `Click` , které by mělo zvýšit počítadlo a aktualizovat obsah značky odstavce ( `<p>` ). Textový obsah prvku odstavce je získán voláním `TextContent` .

* *Assert*: `MarkupMatches` je volána u textového obsahu pro ověření, že se shoduje s očekávaným řetězcem, který je `Current count: 1` .

> [!NOTE]
> `MarkupMatches`Metoda Assert se liší od kontrolního výrazu regulárního porovnání řetězců (například `Assert.Equal("Current count: 1", paraElmText);` ) `MarkupMatches` provede sémantické porovnání vstupu a očekávaného kódu HTML. Sémantické porovnání je vědomo sémantiky HTML, což znamená, že se ignorují nevýznamné prázdné znaky. Výsledkem je více stabilních testů. Další informace najdete v tématu [přizpůsobení sémantického porovnání HTML](https://bunit.egilhansen.com/docs/verification/semantic-html-comparison).

## <a name="additional-resources"></a>Další zdroje informací

* [Začínáme s bUnit](https://bunit.egilhansen.com/docs/getting-started/): pokyny bUnit zahrnují pokyny k vytvoření testovacího projektu, odkazování na testovací balíčky rozhraní a sestavování a spouštění testů.
