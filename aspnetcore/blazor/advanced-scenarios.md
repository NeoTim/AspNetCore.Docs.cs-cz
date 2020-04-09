---
title: ASP.NET Blazor pokročilé scénáře Core
author: guardrex
description: Další informace o Blazorpokročilých scénářích v aplikaci , včetně toho, jak začlenit ruční logiku RenderTreeBuilder do aplikace.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
no-loc:
- Blazor
- SignalR
uid: blazor/advanced-scenarios
ms.openlocfilehash: 5edbbe36e8389bac0335594b1e4331aee1c02867
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659451"
---
# <a name="aspnet-core-blazor-advanced-scenarios"></a>ASP.NET core blazor pokročilé scénáře

[Luke Latham](https://github.com/guardrex) a [Daniel Roth](https://github.com/danroth27)

## <a name="blazor-server-circuit-handler"></a>Obslužná rutina obvodu serveru Blazor

Blazor Server umožňuje kódu definovat *obslužnou rutinu okruhu*, která umožňuje spuštění kódu na změny stavu okruhu uživatele. Obslužná rutina okruhu `CircuitHandler` je implementována odvozením a registrací třídy v kontejneru služeb aplikace. Následující příklad obslužné rutiny obvodu sleduje otevřená připojení SignalR:

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> _circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        _circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        _circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => _circuits.Count;
}
```

Obslužné rutiny obvodu jsou registrovány pomocí DI. Instance s vymezeným oborem jsou vytvořeny pro instanci okruhu. Pomocí `TrackingCircuitHandler` v předchozím příkladu je vytvořena služba singleton, protože musí být sledován stav všech obvodů:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

Pokud metody obslužné rutiny vlastního okruhu vyvolat neošetřené výjimky, výjimka je fatální blazor server obvodu. Chcete-li tolerovat výjimky v kódu obslužné rutiny nebo volané metody, zabalte kód do jednoho nebo více příkazů [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) se zpracováním chyb a protokolováním.

Pokud obvod končí, protože uživatel má odpojena a rozhraní je vyčištění stavu obvodu, rozhraní disponuje oboru DI obvodu. Odstranění oboru disponuje všechny služby DI <xref:System.IDisposable?displayProperty=fullName>s rozsahem okruhu, které implementují . Pokud všechny služby DI vyvolá neošetřené výjimky během likvidace, rozhraní protokoluje výjimku.

## <a name="manual-rendertreebuilder-logic"></a>Ruční rendertreebuilder logika

`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder`poskytuje metody pro manipulaci s komponentami a prvky, včetně vytváření komponent ručně v kódu jazyka C#.

> [!NOTE]
> Použití `RenderTreeBuilder` k vytvoření komponent je pokročilý scénář. Poškozená součást (například neuzavřená značkovací značka) může mít za následek nedefinované chování.

Zvažte `PetDetails` následující součást, která může být ručně zabudována do jiné součásti:

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

V následujícím příkladu smyčka `CreateComponent` v metodě `PetDetails` generuje tři součásti. Při `RenderTreeBuilder` volání metod pro vytvoření`OpenComponent` `AddAttribute`komponent (a ) pořadová čísla jsou čísla řádků kódu původu. Blazorův algoritmus rozdílu závisí na pořadových číslech odpovídajících odlišným řádkům kódu, nikoli odlišným voláním volání. Při vytváření komponenty pomocí `RenderTreeBuilder` metod pevně zakódujte argumenty pro pořadová čísla. **Použití výpočtu nebo čítače ke generování pořadového čísla může vést ke snížení výkonu.** Další informace naleznete v [části Pořadová čísla se vztahují k číslům řádků kódu a nikoli k oddílu pořadí provádění.](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order)

`BuiltContent`Komponenty:

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
> Typy v `Microsoft.AspNetCore.Components.RenderTree` umožňují zpracování *výsledků* vykreslování operací. Jedná se o interní podrobnosti implementace rámce Blazor. Tyto typy by měly být považovány za *nestabilní* a v budoucích verzích by se měly měnit.

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>Pořadová čísla se vztahují k číslům řádků kódu a nikoli k příkazu k provedení

Soubory komponent razor (*.razor*) jsou vždy kompilovány. Kompilace je potenciální výhodou oproti interpretaci kódu, protože krok kompilace lze použít k vložení informací, které zlepšují výkon aplikace za běhu.

Klíčovým příkladem těchto vylepšení jsou *pořadová čísla*. Pořadová čísla označují do běhu, které výstupy pocházejí z, ze kterých odlišných a seřazených řádků kódu. Runtime používá tyto informace ke generování efektivní strom rozdíly v lineární čas, což je mnohem rychlejší, než je obvykle možné pro obecný algoritmus rozdílu stromu.

Zvažte následující soubor komponenty Razor (*.razor):*

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

Předchozí kód zkompiluje na něco jako následující:

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

Při prvním spuštění kódu, pokud `someFlag` je `true`, tvůrce obdrží:

| Sequence | Typ      | Data   |
| :------: | --------- | :----: |
| 0        | Textový uzel | První  |
| 1        | Textový uzel | 1 sekunda |

Představte `someFlag` `false`si, že se stane , a značky je vykreslen znovu. Tentokrát tvůrce obdrží:

| Sequence | Typ       | Data   |
| :------: | ---------- | :----: |
| 1        | Textový uzel  | 1 sekunda |

Když runtime provede rozdíl, zjistí, že položka `0` v sekvenci byla odebrána, takže generuje následující triviální *editační skript*:

* Odeberte první textový uzel.

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a>Problém s generováním pořadových čísel programově

Představte si, že místo toho jste napsali následující logiku tvůrce vykreslení:

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

Nyní je první výstup:

| Sequence | Typ      | Data   |
| :------: | --------- | :----: |
| 0        | Textový uzel | První  |
| 1        | Textový uzel | 1 sekunda |

Tento výsledek je totožný s předchozím případem, takže neexistují žádné negativní problémy. `someFlag`je `false` na druhé vykreslení a výstup je:

| Sequence | Typ      | Data   |
| :------: | --------- | ------ |
| 0        | Textový uzel | 1 sekunda |

Tentokrát algoritmus diff zjistí, že došlo ke *dvěma* změnám a algoritmus generuje následující skript pro úpravy:

* Změňte hodnotu prvního textového uzlu na `Second`.
* Odeberte druhý textový uzel.

Generování pořadových čísel ztratilo všechny užitečné `if/else` informace o tom, kde byly větve a smyčky přítomny v původním kódu. To má za následek rozdíl **dvakrát tak dlouho jako** dříve.

Toto je triviální příklad. V realističtějších případech se složitými a hluboce vnořenými strukturami a zejména se smyčkami jsou náklady na výkon obvykle vyšší. Namísto okamžitého určení, které smyčky bloky nebo větve byly vloženy nebo odstraněny, algoritmus diff musí recurse hluboko do vykreslovat stromy. To obvykle vede k nutnosti vytvářet delší upravit skripty, protože algoritmus diff je nesprávně informován o tom, jak staré a nové struktury se vztahují k sobě navzájem.

### <a name="guidance-and-conclusions"></a>Pokyny a závěry

* Výkon aplikace trpí, pokud jsou dynamicky generována pořadová čísla.
* Rozhraní framework nelze vytvořit vlastní pořadová čísla automaticky za běhu, protože potřebné informace neexistuje, pokud je zachycena v době kompilace.
* Nepište dlouhé bloky ručně implementované `RenderTreeBuilder` logiky. Preferujte soubory *.razor* a povolte kompilátoru, aby se zabýval pořadovými čísly. Pokud se nemůžete vyhnout `RenderTreeBuilder` ruční logice, rozdělte dlouhé bloky `OpenRegion` / `CloseRegion` kódu na menší části zabalené do volání. Každá oblast má svůj vlastní samostatný prostor pořadových čísel, takže můžete restartovat z nuly (nebo jiné libovolné číslo) uvnitř každé oblasti.
* Pokud jsou posloupná čísla pevně zakódována, algoritmus rozdílu vyžaduje pouze zvýšení hodnoty pořadových čísel. Počáteční hodnota a mezery jsou irelevantní. Jednou z legitimních možností je použít číslo řádku kódu jako pořadové číslo nebo začít od nuly a zvýšit o jedny nebo stovky (nebo jakýkoli preferovaný interval). 
* Blazorpoužívá pořadová čísla, zatímco jiné architektury ui, které rozptylují strom, je nepoužívají. Rozptylování je mnohem rychlejší při použití Blazor pořadových čísel a má tu výhodu, že kompiluje krok, který se automaticky zabývá pořadovými čísly pro vývojáře, kteří authoritují soubory *.razor.*

## <a name="perform-large-data-transfers-in-opno-locblazor-server-apps"></a>Provádění velkých přenosů dat v Blazor serverových aplikacích

V některých případech musí být mezi Jazykem BlazorJavaScript a . K velkým přenosům dat obvykle dochází, když:

* Rozhraní API systému souborů prohlížeče se používají k nahrání nebo stažení souboru.
* Interop s knihovnou třetí strany je vyžadován.

V Blazor serveru je zavedeno omezení, které zabraňuje předávání jednu velké zprávy, které mohou mít za následek problémy s výkonem.

Při vývoji kódu, který přenáší data Blazormezi Jazykem JavaScript a :

* Řez data na menší části a odeslat datové segmenty postupně, dokud všechna data přijata serverem.
* Nepřidělujte velké objekty v kódu JavaScriptu a C#.
* Při odesílání nebo přijímání dat neblokujte hlavní vlákno uživatelského rozhraní po dlouhou dobu.
* Uvolněte všechny paměti spotřebované po dokončení nebo zrušení procesu.
* Vynucujte následující další požadavky pro účely zabezpečení:
  * Deklarujte maximální velikost souboru nebo dat, která může být předána.
  * Deklarujte minimální rychlost odesílání z klienta na server.
* Po přijetí dat serverem mohou být data:
  * Dočasně uloženy ve vyrovnávací paměti, dokud všechny segmenty jsou shromažďovány.
  * Spotřebované okamžitě. Data mohou být například okamžitě uložena v databázi nebo zapsána na disk při přijetí každého segmentu.

Následující třída nahrazovače souborů zpracovává js interop s klientem. Třída uploader používá js interop k:

* Dotazování klienta odeslat datový segment.
* Přerušit transakci, pokud časový interval dotazování.

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.JSInterop;

public class FileUploader : IDisposable
{
    private readonly IJSRuntime _jsRuntime;
    private readonly int _segmentSize = 6144;
    private readonly int _maxBase64SegmentSize = 8192;
    private readonly DotNetObjectReference<FileUploader> _thisReference;
    private List<IMemoryOwner<byte>> _uploadedSegments = 
        new List<IMemoryOwner<byte>>();

    public FileUploader(IJSRuntime jsRuntime)
    {
        _jsRuntime = jsRuntime;
    }

    public async Task<Stream> ReceiveFile(string selector, int maxSize)
    {
        var fileSize = 
            await _jsRuntime.InvokeAsync<int>("getFileSize", selector);

        if (fileSize > maxSize)
        {
            return null;
        }

        var numberOfSegments = Math.Floor(fileSize / (double)_segmentSize) + 1;
        var lastSegmentBytes = 0;
        string base64EncodedSegment;

        for (var i = 0; i < numberOfSegments; i++)
        {
            try
            {
                base64EncodedSegment = 
                    await _jsRuntime.InvokeAsync<string>(
                        "receiveSegment", i, selector);

                if (base64EncodedSegment.Length < _maxBase64SegmentSize && 
                    i < numberOfSegments - 1)
                {
                    return null;
                }
            }
            catch
            {
                return null;
            }

          var current = MemoryPool<byte>.Shared.Rent(_segmentSize);

          if (!Convert.TryFromBase64String(base64EncodedSegment, 
              current.Memory.Slice(0, _segmentSize).Span, out lastSegmentBytes))
          {
              return null;
          }

          _uploadedSegments.Add(current);
        }

        var segments = _uploadedSegments;
        _uploadedSegments = null;

        return new SegmentedStream(segments, _segmentSize, lastSegmentBytes);
    }

    public void Dispose()
    {
        if (_uploadedSegments != null)
        {
            foreach (var segment in _uploadedSegments)
            {
                segment.Dispose();
            }
        }
    }
}
```

V předchozím příkladu:

* Je `_maxBase64SegmentSize` nastavena `8192`na , `_maxBase64SegmentSize = _segmentSize * 4 / 3`která je vypočtena z .
* Rozhraní API pro správu paměti .NET core nízké úrovně se `_uploadedSegments`používají k ukládání segmentů paměti na serveru v .
* Metoda `ReceiveFile` se používá ke zpracování nahrávání prostřednictvím JS interop:
  * Velikost souboru je určena v bajtů `_jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`prostřednictvím JS interop s .
  * Počet segmentů, které mají být `numberOfSegments`přijímány, se vypočítá a uloží do .
  * Segmenty jsou požadovány `for` ve smyčce prostřednictvím JS interop s `_jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`. Všechny segmenty, ale poslední musí být 8 192 bajtů před dekódováním. Klient je nucen odeslat data efektivním způsobem.
  * Pro každý přijatý segment se před <xref:System.Convert.TryFromBase64String*>dekódováním pomocí kontroly provádějí kontroly.
  * Datový proud s daty je <xref:System.IO.Stream> `SegmentedStream`vrácen jako nový ( ) po dokončení nahrávání.

Segmentovaná třída datového proudu zveřejňuje seznam segmentů jako nevyhledatelný <xref:System.IO.Stream>seznam pouze pro čtení :

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;

public class SegmentedStream : Stream
{
    private readonly ReadOnlySequence<byte> _sequence;
    private long _currentPosition = 0;

    public SegmentedStream(IList<IMemoryOwner<byte>> segments, int segmentSize, 
        int lastSegmentSize)
    {
        if (segments.Count == 1)
        {
            _sequence = new ReadOnlySequence<byte>(
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

        _sequence = new ReadOnlySequence<byte>(
            sequenceSegment, 0, lastSegment, lastSegmentSize);
    }

    public override long Position
    {
        get => throw new NotImplementedException();
        set => throw new NotImplementedException();
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
        var bytesToWrite = (int)(_currentPosition + count < _sequence.Length ? 
            count : _sequence.Length - _currentPosition);
        var data = _sequence.Slice(_currentPosition, bytesToWrite);
        data.CopyTo(buffer.AsSpan(offset, bytesToWrite));
        _currentPosition += bytesToWrite;

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

Následující kód implementuje funkce Jazyka JavaScript pro příjem dat:

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
