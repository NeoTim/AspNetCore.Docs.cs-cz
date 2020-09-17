---
title: ASP.NET Core Blazor nahrávání souborů
author: guardrex
description: Přečtěte si, jak nahrát soubory do Blazor komponenty Vstupní_soubor.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/17/2020
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
ms.openlocfilehash: de4654f2efc401143e066628b096052efa65d7a0
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722973"
---
# <a name="aspnet-core-no-locblazor-file-uploads"></a>ASP.NET Core Blazor nahrávání souborů

Od [Daniel Skořepa](https://github.com/danroth27)

Pomocí `InputFile` komponenty můžete číst data ze souboru prohlížeče do kódu .NET, včetně nahrávání souborů. `InputFile`Komponenta se vykresluje jako vstup HTML typu `file` .

Ve výchozím nastavení uživatel vybere jednotlivé soubory. Přidejte `multiple` atribut, aby uživatel mohl nahrávat více souborů najednou. Když uživatel vybere jeden nebo více souborů, `InputFile` Komponenta vyvolá `OnChange` událost a předá v rámci `InputFileChangeEventArgs` , který poskytuje přístup k vybranému seznamu souborů, a podrobnosti o jednotlivých souborech.

Komponenta, která přijímá soubor obrázku, může volat `RequestImageFileAsync` metodu usnadnění v souboru, aby se změnila velikost dat obrázku v modulu runtime jazyka JavaScript v prohlížeči předtím, než se obrázek do aplikace streamuje.

Následující příklad ukazuje více nahrání souboru obrázku v komponentě:

```razor
<h3>Upload PNG images</h3>

<p>
    <InputFile OnChange="@OnInputFileChange" multiple />
</p>

@if (imageDataUrls.Count > 0)
{
    <h3>Images</h3>

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
        var imageFiles = e.GetMultipleFiles();
        var format = "image/png";

        foreach (var imageFile in imageFiles)
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

Chcete-li číst data z uživatelem vybraného souboru, zavolejte `OpenReadStream` na soubor a načtěte ho ze vráceného datového proudu. V Blazor WebAssembly aplikaci se data streamují přímo do kódu .NET v prohlížeči. V Blazor Server aplikaci se data souboru streamují do kódu .NET na serveru, protože soubor je načtený z datového proudu. 
