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
# <a name="aspnet-core-no-locblazor-file-uploads"></a><span data-ttu-id="9998b-103">ASP.NET Core Blazor nahrávání souborů</span><span class="sxs-lookup"><span data-stu-id="9998b-103">ASP.NET Core Blazor file uploads</span></span>

<span data-ttu-id="9998b-104">Od [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="9998b-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="9998b-105">Pomocí `InputFile` komponenty můžete číst data ze souboru prohlížeče do kódu .NET, včetně nahrávání souborů.</span><span class="sxs-lookup"><span data-stu-id="9998b-105">Use the `InputFile` component to read browser file data into .NET code, including for file uploads.</span></span> <span data-ttu-id="9998b-106">`InputFile`Komponenta se vykresluje jako vstup HTML typu `file` .</span><span class="sxs-lookup"><span data-stu-id="9998b-106">The `InputFile` component renders as an HTML input of type `file`.</span></span>

<span data-ttu-id="9998b-107">Ve výchozím nastavení uživatel vybere jednotlivé soubory.</span><span class="sxs-lookup"><span data-stu-id="9998b-107">By default, the user selects single files.</span></span> <span data-ttu-id="9998b-108">Přidejte `multiple` atribut, aby uživatel mohl nahrávat více souborů najednou.</span><span class="sxs-lookup"><span data-stu-id="9998b-108">Add the `multiple` attribute to permit the user to upload multiple files at once.</span></span> <span data-ttu-id="9998b-109">Když uživatel vybere jeden nebo více souborů, `InputFile` Komponenta vyvolá `OnChange` událost a předá v rámci `InputFileChangeEventArgs` , který poskytuje přístup k vybranému seznamu souborů, a podrobnosti o jednotlivých souborech.</span><span class="sxs-lookup"><span data-stu-id="9998b-109">When one or more files is selected by the user, the `InputFile` component fires an `OnChange` event and passes in an `InputFileChangeEventArgs` that provides access to the selected file list and details about each file.</span></span>

<span data-ttu-id="9998b-110">Komponenta, která přijímá soubor obrázku, může volat `RequestImageFileAsync` metodu usnadnění v souboru, aby se změnila velikost dat obrázku v modulu runtime jazyka JavaScript v prohlížeči předtím, než se obrázek do aplikace streamuje.</span><span class="sxs-lookup"><span data-stu-id="9998b-110">A component that receives an image file can call the `RequestImageFileAsync` convenience method on the file to resize the image data within the browser's JavaScript runtime before the image is streamed into the app.</span></span>

<span data-ttu-id="9998b-111">Následující příklad ukazuje více nahrání souboru obrázku v komponentě:</span><span class="sxs-lookup"><span data-stu-id="9998b-111">The following example demonstrates multiple image file upload in a component:</span></span>

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

<span data-ttu-id="9998b-112">Chcete-li číst data z uživatelem vybraného souboru, zavolejte `OpenReadStream` na soubor a načtěte ho ze vráceného datového proudu.</span><span class="sxs-lookup"><span data-stu-id="9998b-112">To read data from a user-selected file, call `OpenReadStream` on the file and read from the returned stream.</span></span> <span data-ttu-id="9998b-113">V Blazor WebAssembly aplikaci se data streamují přímo do kódu .NET v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="9998b-113">In a Blazor WebAssembly app, the data is streamed directly into the .NET code within the browser.</span></span> <span data-ttu-id="9998b-114">V Blazor Server aplikaci se data souboru streamují do kódu .NET na serveru, protože soubor je načtený z datového proudu.</span><span class="sxs-lookup"><span data-stu-id="9998b-114">In a Blazor Server app, the file data is streamed into .NET code on the server as the file is read from the stream.</span></span> 
