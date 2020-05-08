---
title: ASP.NET Core Blazor pokročilé scénáře
author: guardrex
description: Přečtěte si o rozšířených scénářích v Blazor, včetně postupu začlenění ruční RenderTreeBuilder logiky do aplikace.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/advanced-scenarios
ms.openlocfilehash: b47e7b1d7ff148bb5a8d299d3d2089999f017863
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967334"
---
# <a name="aspnet-core-blazor-advanced-scenarios"></a>ASP.NET Core pokročilé scénáře pro Blazor

Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)

## <a name="blazor-server-circuit-handler"></a>Obslužná rutina okruhu serveru Blazor

Blazor Server umožňuje kódu definovat *obslužnou rutinu okruhu*, která umožňuje spuštění kódu při změnách stavu okruhu uživatele. Obslužná rutina okruhu je implementována odvozením z `CircuitHandler` a registrací třídy v kontejneru služby aplikace. Následující příklad obslužné rutiny okruhu sleduje připojení Open Signal:

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => circuits.Count;
}
```

Obslužné rutiny okruhu jsou registrovány pomocí DI. Vymezené instance se vytvářejí pro jednotlivé instance okruhu. Pomocí `TrackingCircuitHandler` v předchozím příkladu je vytvořena singleton služba, protože stav všech okruhů musí být sledován:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

Pokud metody obslužné rutiny vlastního okruhu vyvolávají neošetřenou výjimku, je výjimka závažná pro okruh serveru Blazor. Chcete-li tolerovat výjimky v kódu obslužné rutiny nebo volané metody, zabalte kód v jednom nebo více příkazech [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) s zpracováním chyb a protokolováním.

Když je okruh ukončený, protože uživatel je odpojený a rozhraní čistí stav okruhu, rozhraní uvolní obor DI okruhu. Při likvidaci oboru se uvolní jakékoli DI služby, které implementují <xref:System.IDisposable?displayProperty=fullName>okruhy s rozsahem. Pokud jakákoli služba DI vyvolá neošetřenou výjimku při vyřazení, rozhraní zaprotokoluje výjimku.

## <a name="manual-rendertreebuilder-logic"></a>Ruční logika RenderTreeBuilder

`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder`poskytuje metody pro práci s komponentami a prvky, včetně sestavování komponent ručně v kódu jazyka C#.

> [!NOTE]
> Použití aplikace `RenderTreeBuilder` k vytvoření komponent je pokročilý scénář. Poškozená komponenta (například značka neuzavřeného označení) může mít za následek nedefinované chování.

Vezměte v úvahu `PetDetails` následující součást, kterou je možné ručně vytvořit do jiné komponenty:

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

V následujícím příkladu smyčka v `CreateComponent` metodě generuje tři `PetDetails` komponenty. Při volání `RenderTreeBuilder` metod pro vytvoření komponent (`OpenComponent` a `AddAttribute`) jsou pořadová čísla čísla řádků zdrojového kódu. Algoritmus rozdílu Blazor spoléhá na pořadová čísla, která odpovídají jedinečným řádkům kódu, a neliší vyvolání volání. Při vytváření komponenty s `RenderTreeBuilder` metodami nekódujte pevně argumenty pro pořadová čísla. **Použití výpočtu nebo čítače k vygenerování pořadového čísla může vést k špatnému výkonu.** Další informace naleznete v části [pořadové číslo se vztahuje na čísla řádků kódu a nikoli na oddíl pořadí provádění](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) .

`BuiltContent`část

```razor
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> [!WARNING]
> Typy v `Microsoft.AspNetCore.Components.RenderTree` nástroji umožňují zpracování *výsledků* operací vykreslování. Jedná se o interní podrobnosti implementace Blazor Framework. Tyto typy by měly být považovány za *nestabilní* a mohou se změnit v budoucích verzích.

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>Pořadová čísla se vztahují na čísla řádků kódu a nikoli na pořadí provádění.

Soubory komponent Razor (*. Razor*) jsou vždy kompilovány. Kompilace je potenciální výhodou pro interpretaci kódu, protože krok kompilace lze použít k vložení informací, které zvyšují výkon aplikace za běhu.

Hlavní příklad těchto vylepšení zahrnuje *pořadová čísla*. Pořadová čísla označují modul runtime, ze kterého výstupy pocházejí, ze kterých se liší a seřazené řádky kódu. Modul runtime používá tyto informace k vygenerování efektivních rozdílů stromu v lineárním čase, což je mnohem rychlejší než obvykle pro obecný rozdílový algoritmus stromu.

Vezměte v úvahu následující soubor součásti Razor (*. Razor*):

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

Předchozí kód se zkompiluje jako následující:

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

V případě, že se kód spustí poprvé, v `someFlag` případě `true`, že je, tvůrce obdrží:

| Sequence | Typ      | Data   |
| :------: | --------- | :----: |
| 0        | Textový uzel | První  |
| 1        | Textový uzel | 1 sekunda |

Představte si, že `someFlag` se `false`zobrazí a značka se znovu vykreslí. Tentokrát Tvůrce získá:

| Sequence | Typ       | Data   |
| :------: | ---------- | :----: |
| 1        | Textový uzel  | 1 sekunda |

Pokud modul runtime provede rozdíl, uvidí, že položka v sekvenci `0` byla odebrána, takže generuje následující skript triviálního *úprav*:

* Odeberte první textový uzel.

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a>Problém s tím, že se pořadová čísla generují programově

Představte si, že jste napsali následující logiku tvůrce stromu vykreslování:

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

Teď je první výstup:

| Sequence | Typ      | Data   |
| :------: | --------- | :----: |
| 0        | Textový uzel | První  |
| 1        | Textový uzel | 1 sekunda |

Tento výsledek je stejný jako předchozí případ, takže neexistují žádné negativní problémy. `someFlag`je `false` ve druhém vykreslování a výstup je:

| Sequence | Typ      | Data   |
| :------: | --------- | ------ |
| 0        | Textový uzel | 1 sekunda |

Tentokrát rozdílový algoritmus uvidí, že došlo ke *dvěma* změnám, a algoritmus generuje následující skript pro úpravy:

* Změňte hodnotu prvního textového uzlu na `Second`.
* Odeberte druhý textový uzel.

Generování pořadových čísel ztratilo všechny užitečné informace o tom, kde `if/else` byly větve a cykly přítomny v původním kódu. To vede ke rozdílu **dvakrát, jak dlouho** chcete.

Toto je triviální příklad. Ve složitějších případech se složitými a hluboce vnořenými strukturami a zejména s cykly jsou náklady na výkon obvykle vyšší. Místo toho, aby ihned identifikovaly, které bloky nebo větve smyčky byly vloženy nebo odebrány, je třeba rozdílový algoritmus přepracovat hluboko do stromů vykreslování. To obvykle vede k sestavování delších úprav skriptů, protože rozdílový algoritmus je neinformován o tom, jak se stará a nová struktura vzájemně souvisí.

### <a name="guidance-and-conclusions"></a>Doprovodné materiály a závěry

* Pokud jsou automaticky generována pořadová čísla, je výkon aplikace zhoršen.
* Rozhraní nemůže automaticky vytvořit vlastní pořadová čísla za běhu, protože potřebné informace neexistují, pokud není zachycena v době kompilace.
* Nepište dlouhé bloky ručně implementované `RenderTreeBuilder` logiky. Preferovat soubory *. Razor* a umožněte kompilátoru, aby se zabývat pořadovým číslem. Pokud se nemůžete vyhnout manuální `RenderTreeBuilder` logice, rozdělte dlouhé bloky kódu do menších částí zabalených v `OpenRegion` / `CloseRegion` voláních. Každá oblast má vlastní oddělený prostor pořadových čísel, takže v každé oblasti můžete restartovat z nuly (nebo jakéhokoli jiného libovolného čísla).
* Pokud jsou pořadová čísla pevně zakódované, rozdílový algoritmus vyžaduje pouze zvýšení hodnoty pořadových čísel. Počáteční hodnota a mezery jsou nepodstatné. Jednou z oprávněných možností je použít číslo řádku kódu jako pořadové číslo nebo začít od nuly a zvýšit podle hodnoty nebo stovky (případně z upřednostňovaného intervalu). 
* Blazorpoužívá pořadová čísla, zatímco jiné architektury uživatelského rozhraní rozdílového stromu je nepoužívají. Rozdílování je mnohem rychlejší při použití pořadových čísel a Blazor má výhodu kompilačního kroku, který se automaticky zabývá pořadovým číslem pro vývojáře, který vytváří soubory *. Razor* .

## <a name="perform-large-data-transfers-in-blazor-server-apps"></a>Provádění rozsáhlých přenosů Blazor dat v serverových aplikacích

V některých scénářích je nutné přenášet velké objemy dat mezi jazykem BlazorJavaScript a. K přenosu velkých objemů dat se obvykle dochází v těchto případech:

* Rozhraní API pro systém souborů v prohlížeči slouží k nahrání nebo stažení souboru.
* Je vyžadována interoperabilita s knihovnou třetích stran.

Na Blazor serveru platí omezení, aby nedocházelo k předávání jednoduchých zpráv, které by mohly způsobit problémy s výkonem.

Při vývoji kódu, který přenáší data mezi JavaScriptem a Blazornásledujícími možnostmi, vezměte v úvahu následující pokyny:

* Rozdělí data na menší části a datové segmenty se postupně odesílají, dokud server neobdrží všechna data.
* Nepřiřazujte velké objekty v kódu JavaScript a C#.
* Při odesílání nebo přijímání dat Neblokujte hlavní vlákno uživatelského rozhraní pro dlouhá období.
* Uvolněte veškerou paměť, která je spotřebována při dokončení nebo zrušení procesu.
* Vyjistěte následující další požadavky pro účely zabezpečení:
  * Deklarujte maximální velikost souboru nebo dat, která se dají předat.
  * Deklarujte minimální rychlost nahrávání z klienta na server.
* Po přijetí dat serverem můžou být tato data:
  * Dočasně uloženo do vyrovnávací paměti, dokud nebudou všechny segmenty shromažďovány.
  * Spotřebované hned. Data můžete například uložit hned do databáze nebo zapsat na disk při přijetí každého segmentu.

Následující třída pro odeslání souborů zpracovává zprostředkovatele komunikace JS s klientem. Třída odeslání používá zprostředkovatele komunikace JS na:

* Cyklické dotazování klienta o odeslání datového segmentu.
* Zruší transakci, pokud vyprší časový limit pro cyklické dotazování.

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.JSInterop;

public class FileUploader : IDisposable
{
    private readonly IJSRuntime jsRuntime;
    private readonly int segmentSize = 6144;
    private readonly int maxBase64SegmentSize = 8192;
    private readonly DotNetObjectReference<FileUploader> thisReference;
    private List<IMemoryOwner<byte>> uploadedSegments = 
        new List<IMemoryOwner<byte>>();

    public FileUploader(IJSRuntime jsRuntime)
    {
        this.jsRuntime = jsRuntime;
    }

    public async Task<Stream> ReceiveFile(string selector, int maxSize)
    {
        var fileSize = 
            await jsRuntime.InvokeAsync<int>("getFileSize", selector);

        if (fileSize > maxSize)
        {
            return null;
        }

        var numberOfSegments = Math.Floor(fileSize / (double)segmentSize) + 1;
        var lastSegmentBytes = 0;
        string base64EncodedSegment;

        for (var i = 0; i < numberOfSegments; i++)
        {
            try
            {
                base64EncodedSegment = 
                    await jsRuntime.InvokeAsync<string>(
                        "receiveSegment", i, selector);

                if (base64EncodedSegment.Length < maxBase64SegmentSize && 
                    i < numberOfSegments - 1)
                {
                    return null;
                }
            }
            catch
            {
                return null;
            }

          var current = MemoryPool<byte>.Shared.Rent(segmentSize);

          if (!Convert.TryFromBase64String(base64EncodedSegment, 
              current.Memory.Slice(0, segmentSize).Span, out lastSegmentBytes))
          {
              return null;
          }

          uploadedSegments.Add(current);
        }

        var segments = uploadedSegments;
        uploadedSegments = null;

        return new SegmentedStream(segments, segmentSize, lastSegmentBytes);
    }

    public void Dispose()
    {
        if (uploadedSegments != null)
        {
            foreach (var segment in uploadedSegments)
            {
                segment.Dispose();
            }
        }
    }
}
```

V předchozím příkladu:

* `maxBase64SegmentSize` Je nastaven na `8192`, který je vypočten z `maxBase64SegmentSize = segmentSize * 4 / 3`.
* Rozhraní API pro správu paměti nižší úrovně v rozhraní .NET Core se používají k ukládání segmentů paměti na `uploadedSegments`serveru v.
* `ReceiveFile` Metoda se používá ke zpracování odesílání prostřednictvím zprostředkovatele komunikace JS:
  * Velikost souboru je určena v bajtech přes interoperabilitu JS `jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`s.
  * Počet segmentů, které se mají přijmout, se vypočítávají a ukládají v `numberOfSegments`.
  * Segmenty jsou požadovány ve `for` smyčce přes interoperabilitu js `jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`s. Všechny segmenty, ale poslední, musí mít 8 192 bajtů před dekódováním. Klient je nucen posílat data účinným způsobem.
  * Pro každý přijatý segment jsou kontroly provedeny před dekódováním pomocí <xref:System.Convert.TryFromBase64String%2A>.
  * Po dokončení nahrávání se datový proud s daty vrátí jako <xref:System.IO.Stream> nový`SegmentedStream`().

Třída segmentace Stream zpřístupňuje seznam segmentů jako jen pro čtení, které nemůžete prohledávat <xref:System.IO.Stream>:

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;

public class SegmentedStream : Stream
{
    private readonly ReadOnlySequence<byte> sequence;
    private long currentPosition = 0;

    public SegmentedStream(IList<IMemoryOwner<byte>> segments, int segmentSize, 
        int lastSegmentSize)
    {
        if (segments.Count == 1)
        {
            sequence = new ReadOnlySequence<byte>(
                segments[0].Memory.Slice(0, lastSegmentSize));
            return;
        }

        var sequenceSegment = new BufferSegment<byte>(
            segments[0].Memory.Slice(0, segmentSize));
        var lastSegment = sequenceSegment;

        for (int i = 1; i < segments.Count; i++)
        {
            var isLastSegment = i + 1 == segments.Count;
            lastSegment = lastSegment.Append(segments[i].Memory.Slice(
                0, isLastSegment ? lastSegmentSize : segmentSize));
        }

        sequence = new ReadOnlySequence<byte>(
            sequenceSegment, 0, lastSegment, lastSegmentSize);
    }

    public override long Position
    {
        get => throw new NotImplementedException();
        set => throw new NotImplementedException();
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
        var bytesToWrite = (int)(currentPosition + count < sequence.Length ? 
            count : sequence.Length - currentPosition);
        var data = sequence.Slice(currentPosition, bytesToWrite);
        data.CopyTo(buffer.AsSpan(offset, bytesToWrite));
        currentPosition += bytesToWrite;

        return bytesToWrite;
    }

    private class BufferSegment<T> : ReadOnlySequenceSegment<T>
    {
        public BufferSegment(ReadOnlyMemory<T> memory)
        {
            Memory = memory;
        }

        public BufferSegment<T> Append(ReadOnlyMemory<T> memory)
        {
            var segment = new BufferSegment<T>(memory)
            {
                RunningIndex = RunningIndex + Memory.Length
            };

            Next = segment;

            return segment;
        }
    }

    public override bool CanRead => true;

    public override bool CanSeek => false;

    public override bool CanWrite => false;

    public override long Length => throw new NotImplementedException();

    public override void Flush() => throw new NotImplementedException();

    public override long Seek(long offset, SeekOrigin origin) => 
        throw new NotImplementedException();

    public override void SetLength(long value) => 
        throw new NotImplementedException();

    public override void Write(byte[] buffer, int offset, int count) => 
        throw new NotImplementedException();
}
```

Následující kód implementuje funkce JavaScriptu pro příjem dat:

```javascript
function getFileSize(selector) {
  const file = getFile(selector);
  return file.size;
}

async function receiveSegment(segmentNumber, selector) {
  const file = getFile(selector);
  var segments = getFileSegments(file);
  var index = segmentNumber * 6144;
  return await getNextChunk(file, index);
}

function getFile(selector) {
  const element = document.querySelector(selector);
  if (!element) {
    throw new Error('Invalid selector');
  }
  const files = element.files;
  if (!files || files.length === 0) {
    throw new Error(`Element ${elementId} doesn't contain any files.`);
  }
  const file = files[0];
  return file;
}

function getFileSegments(file) {
  const segments = Math.floor(size % 6144 === 0 ? size / 6144 : 1 + size / 6144);
  return segments;
}

async function getNextChunk(file, index) {
  const length = file.size - index <= 6144 ? file.size - index : 6144;
  const chunk = file.slice(index, index + length);
  index += length;
  const base64Chunk = await this.base64EncodeAsync(chunk);
  return { base64Chunk, index };
}

async function base64EncodeAsync(chunk) {
  const reader = new FileReader();
  const result = new Promise((resolve, reject) => {
    reader.addEventListener('load',
      () => {
        const base64Chunk = reader.result;
        const cleanChunk = 
          base64Chunk.replace('data:application/octet-stream;base64,', '');
        resolve(cleanChunk);
      },
      false);
    reader.addEventListener('error', reject);
  });
  reader.readAsDataURL(chunk);
  return result;
}
```
