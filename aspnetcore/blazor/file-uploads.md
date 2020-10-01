---
title: ASP.NET Core Blazor nahrávání souborů
author: guardrex
description: Přečtěte si, jak nahrát soubory do Blazor komponenty Vstupní_soubor.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/29/2020
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
uid: blazor/file-uploads
ms.openlocfilehash: 06d1464cb731a8008362fc911f463e4ff8a37b6b
ms.sourcegitcommit: d1a897ebd89daa05170ac448e4831d327f6b21a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91606660"
---
# <a name="aspnet-core-no-locblazor-file-uploads"></a>ASP.NET Core Blazor nahrávání souborů

Od [Daniel Skořepa](https://github.com/danroth27) a [Pranav Krishnamoorthy](https://github.com/pranavkm)

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Pomocí `InputFile` komponenty můžete číst data ze souboru prohlížeče do kódu .NET, včetně nahrávání souborů.

> [!WARNING]
> Vždy Sledujte doporučené postupy zabezpečení nahrávání souborů. Další informace naleznete v tématu <xref:mvc/models/file-uploads#security-considerations>.

## <a name="inputfile-component"></a>`InputFile` (komponenta)

`InputFile`Komponenta se vykresluje jako vstup HTML typu `file` .

Ve výchozím nastavení uživatel vybere jednotlivé soubory. Přidejte `multiple` atribut, aby uživatel mohl nahrávat více souborů najednou. Když uživatel vybere jeden nebo více souborů, `InputFile` Komponenta vyvolá `OnChange` událost a předá v rámci `InputFileChangeEventArgs` , který poskytuje přístup k vybranému seznamu souborů, a podrobnosti o jednotlivých souborech.

Čtení dat z uživatelem vybraného souboru:

* Zavolejte `OpenReadStream` na soubor a načtěte ho ze vráceného datového proudu. Další informace najdete v části [streamy souborů](#file-streams) .
* Použijte `ReadAsync`. Ve výchozím nastavení `ReadAsync` povolí velikost jenom čtení souboru, který je menší než 524 288 KB (512 KB). Toto omezení je k dispozici, pokud chcete vývojářům zabránit v náhodném čtení velkých souborů v paměti. Zadejte rozumnou aproximaci pro maximální očekávanou velikost souboru, pokud je potřeba podporovat větší soubory. Vyhněte se čtení příchozího datového proudu souborů přímo do paměti. Například nekopírujete bajty souborů do <xref:System.IO.MemoryStream> nebo přečtené jako bajtové pole. Výsledkem těchto přístupů mohou být problémy s výkonem a zabezpečením, zejména v Blazor Server . Místo toho zvažte zkopírování bajtů souborů do externího úložiště, jako je například objekt BLOB nebo soubor na disku.

Komponenta, která přijímá soubor obrázku, může volat `RequestImageFileAsync` metodu usnadnění v souboru, aby se změnila velikost dat obrázku v modulu runtime jazyka JavaScript v prohlížeči předtím, než se obrázek do aplikace streamuje.

Následující příklad ukazuje vícenásobné nahrání souboru obrázku v komponentě. `InputFileChangeEventArgs.GetMultipleFiles` povoluje čtení více souborů. Zadejte maximální počet souborů, které se mají přečíst, aby uživatel se zlými úmysly nemohlo nahrávat větší počet souborů, než očekává aplikace. `InputFileChangeEventArgs.File` umožňuje čtení prvního a pouze souboru, pokud nahrávání souboru nepodporuje více souborů.

```razor
<h3>Upload PNG images</h3>

<p>
    <InputFile OnChange="@OnInputFileChange" multiple />
</p>

@if (imageDataUrls.Count > 0)
{
    <h4>Images</h4>

    <div class="card" style="width:30rem;">
        <div class="card-body">
            @foreach (var imageDataUrl in imageDataUrls)
            {
                <img class="rounded m-1" src="@imageDataUrl" />
            }
        </div>
    </div>
}

@code {
    IList<string> imageDataUrls = new List<string>();

    private async Task OnInputFileChange(InputFileChangeEventArgs e)
    {
        var maxAllowedFiles = 3;
        var format = "image/png";

        foreach (var imageFile in e.GetMultipleFiles(maxAllowedFiles))
        {
            var resizedImageFile = await imageFile.RequestImageFileAsync(format, 
                100, 100);
            var buffer = new byte[resizedImageFile.Size];
            await resizedImageFile.OpenReadStream().ReadAsync(buffer);
            var imageDataUrl = 
                $"data:{format};base64,{Convert.ToBase64String(buffer)}";
            imageDataUrls.Add(imageDataUrl);
        }
    }
}
```

`IBrowserFile` Vrátí metadata [vystavená prohlížečem](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) jako vlastnosti. Tato metadata můžou být užitečná pro předběžné ověření. Podívejte se například na [ `FileUpload.razor` `FilePreview.razor` ukázkové komponenty a](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/).

## <a name="file-streams"></a>Datové proudy souborů

V Blazor WebAssembly aplikaci se data streamují přímo do kódu .NET v prohlížeči.

V Blazor Server aplikaci jsou data souboru streamovaná přes SignalR připojení do kódu .NET na serveru, protože soubor je načtený z datového proudu. [`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) umožňuje konfigurovat charakteristiky nahrávání souborů pro Blazor Server .

## <a name="additional-resources"></a>Další materiály

* <xref:mvc/models/file-uploads#security-considerations>
* <xref:blazor/forms-validation>
