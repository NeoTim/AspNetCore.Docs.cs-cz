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
# <a name="aspnet-core-no-locblazor-file-uploads"></a><span data-ttu-id="675f9-103">ASP.NET Core Blazor nahrávání souborů</span><span class="sxs-lookup"><span data-stu-id="675f9-103">ASP.NET Core Blazor file uploads</span></span>

<span data-ttu-id="675f9-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="675f9-104">By [Daniel Roth](https://github.com/danroth27) and [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="675f9-105">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="675f9-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="675f9-106">Pomocí `InputFile` komponenty můžete číst data ze souboru prohlížeče do kódu .NET, včetně nahrávání souborů.</span><span class="sxs-lookup"><span data-stu-id="675f9-106">Use the `InputFile` component to read browser file data into .NET code, including for file uploads.</span></span>

> [!WARNING]
> <span data-ttu-id="675f9-107">Vždy Sledujte doporučené postupy zabezpečení nahrávání souborů.</span><span class="sxs-lookup"><span data-stu-id="675f9-107">Always follow file upload security best practices.</span></span> <span data-ttu-id="675f9-108">Další informace naleznete v tématu <xref:mvc/models/file-uploads#security-considerations>.</span><span class="sxs-lookup"><span data-stu-id="675f9-108">For more information, see <xref:mvc/models/file-uploads#security-considerations>.</span></span>

## <a name="inputfile-component"></a><span data-ttu-id="675f9-109">`InputFile` (komponenta)</span><span class="sxs-lookup"><span data-stu-id="675f9-109">`InputFile` component</span></span>

<span data-ttu-id="675f9-110">`InputFile`Komponenta se vykresluje jako vstup HTML typu `file` .</span><span class="sxs-lookup"><span data-stu-id="675f9-110">The `InputFile` component renders as an HTML input of type `file`.</span></span>

<span data-ttu-id="675f9-111">Ve výchozím nastavení uživatel vybere jednotlivé soubory.</span><span class="sxs-lookup"><span data-stu-id="675f9-111">By default, the user selects single files.</span></span> <span data-ttu-id="675f9-112">Přidejte `multiple` atribut, aby uživatel mohl nahrávat více souborů najednou.</span><span class="sxs-lookup"><span data-stu-id="675f9-112">Add the `multiple` attribute to permit the user to upload multiple files at once.</span></span> <span data-ttu-id="675f9-113">Když uživatel vybere jeden nebo více souborů, `InputFile` Komponenta vyvolá `OnChange` událost a předá v rámci `InputFileChangeEventArgs` , který poskytuje přístup k vybranému seznamu souborů, a podrobnosti o jednotlivých souborech.</span><span class="sxs-lookup"><span data-stu-id="675f9-113">When one or more files is selected by the user, the `InputFile` component fires an `OnChange` event and passes in an `InputFileChangeEventArgs` that provides access to the selected file list and details about each file.</span></span>

<span data-ttu-id="675f9-114">Čtení dat z uživatelem vybraného souboru:</span><span class="sxs-lookup"><span data-stu-id="675f9-114">To read data from a user-selected file:</span></span>

* <span data-ttu-id="675f9-115">Zavolejte `OpenReadStream` na soubor a načtěte ho ze vráceného datového proudu.</span><span class="sxs-lookup"><span data-stu-id="675f9-115">Call `OpenReadStream` on the file and read from the returned stream.</span></span> <span data-ttu-id="675f9-116">Další informace najdete v části [streamy souborů](#file-streams) .</span><span class="sxs-lookup"><span data-stu-id="675f9-116">For more information, see the [File streams](#file-streams) section.</span></span>
* <span data-ttu-id="675f9-117">Použijte `ReadAsync`.</span><span class="sxs-lookup"><span data-stu-id="675f9-117">Use `ReadAsync`.</span></span> <span data-ttu-id="675f9-118">Ve výchozím nastavení `ReadAsync` povolí velikost jenom čtení souboru, který je menší než 524 288 KB (512 KB).</span><span class="sxs-lookup"><span data-stu-id="675f9-118">By default, `ReadAsync` only allows reading a file smaller than 524,288 KB (512 KB) in size.</span></span> <span data-ttu-id="675f9-119">Toto omezení je k dispozici, pokud chcete vývojářům zabránit v náhodném čtení velkých souborů v paměti.</span><span class="sxs-lookup"><span data-stu-id="675f9-119">This limit is present to prevent developers from accidentally reading large files in to memory.</span></span> <span data-ttu-id="675f9-120">Zadejte rozumnou aproximaci pro maximální očekávanou velikost souboru, pokud je potřeba podporovat větší soubory.</span><span class="sxs-lookup"><span data-stu-id="675f9-120">Specify a reasonable approximation for the maximum expected file size if larger files must be supported.</span></span> <span data-ttu-id="675f9-121">Vyhněte se čtení příchozího datového proudu souborů přímo do paměti.</span><span class="sxs-lookup"><span data-stu-id="675f9-121">Avoid reading the incoming file stream directly into memory.</span></span> <span data-ttu-id="675f9-122">Například nekopírujete bajty souborů do <xref:System.IO.MemoryStream> nebo přečtené jako bajtové pole.</span><span class="sxs-lookup"><span data-stu-id="675f9-122">For example, don't copy file bytes into a <xref:System.IO.MemoryStream> or read as a byte array.</span></span> <span data-ttu-id="675f9-123">Výsledkem těchto přístupů mohou být problémy s výkonem a zabezpečením, zejména v Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="675f9-123">These approaches can result in performance and security problems, especially in Blazor Server.</span></span> <span data-ttu-id="675f9-124">Místo toho zvažte zkopírování bajtů souborů do externího úložiště, jako je například objekt BLOB nebo soubor na disku.</span><span class="sxs-lookup"><span data-stu-id="675f9-124">Instead, consider copying file bytes to an external store, such as a a blob or a file on disk.</span></span>

<span data-ttu-id="675f9-125">Komponenta, která přijímá soubor obrázku, může volat `RequestImageFileAsync` metodu usnadnění v souboru, aby se změnila velikost dat obrázku v modulu runtime jazyka JavaScript v prohlížeči předtím, než se obrázek do aplikace streamuje.</span><span class="sxs-lookup"><span data-stu-id="675f9-125">A component that receives an image file can call the `RequestImageFileAsync` convenience method on the file to resize the image data within the browser's JavaScript runtime before the image is streamed into the app.</span></span>

<span data-ttu-id="675f9-126">Následující příklad ukazuje vícenásobné nahrání souboru obrázku v komponentě.</span><span class="sxs-lookup"><span data-stu-id="675f9-126">The following example demonstrates multiple image file upload in a component.</span></span> <span data-ttu-id="675f9-127">`InputFileChangeEventArgs.GetMultipleFiles` povoluje čtení více souborů.</span><span class="sxs-lookup"><span data-stu-id="675f9-127">`InputFileChangeEventArgs.GetMultipleFiles` allows reading multiple files.</span></span> <span data-ttu-id="675f9-128">Zadejte maximální počet souborů, které se mají přečíst, aby uživatel se zlými úmysly nemohlo nahrávat větší počet souborů, než očekává aplikace.</span><span class="sxs-lookup"><span data-stu-id="675f9-128">Specify the maximum number of files you expect to read to prevent a malicious user from uploading a larger number of files than the app expects.</span></span> <span data-ttu-id="675f9-129">`InputFileChangeEventArgs.File` umožňuje čtení prvního a pouze souboru, pokud nahrávání souboru nepodporuje více souborů.</span><span class="sxs-lookup"><span data-stu-id="675f9-129">`InputFileChangeEventArgs.File` allows reading the first and only file if the file upload does not support multiple files.</span></span>

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

<span data-ttu-id="675f9-130">`IBrowserFile` Vrátí metadata [vystavená prohlížečem](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) jako vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="675f9-130">`IBrowserFile` returns metadata [exposed by the browser](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) as properties.</span></span> <span data-ttu-id="675f9-131">Tato metadata můžou být užitečná pro předběžné ověření.</span><span class="sxs-lookup"><span data-stu-id="675f9-131">This metadata can be useful to preliminary validation.</span></span> <span data-ttu-id="675f9-132">Podívejte se například na [ `FileUpload.razor` `FilePreview.razor` ukázkové komponenty a](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/).</span><span class="sxs-lookup"><span data-stu-id="675f9-132">For example, see the [`FileUpload.razor` and `FilePreview.razor` sample components](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/).</span></span>

## <a name="file-streams"></a><span data-ttu-id="675f9-133">Datové proudy souborů</span><span class="sxs-lookup"><span data-stu-id="675f9-133">File streams</span></span>

<span data-ttu-id="675f9-134">V Blazor WebAssembly aplikaci se data streamují přímo do kódu .NET v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="675f9-134">In a Blazor WebAssembly app, the data is streamed directly into the .NET code within the browser.</span></span>

<span data-ttu-id="675f9-135">V Blazor Server aplikaci jsou data souboru streamovaná přes SignalR připojení do kódu .NET na serveru, protože soubor je načtený z datového proudu.</span><span class="sxs-lookup"><span data-stu-id="675f9-135">In a Blazor Server app, the file data is streamed over the SignalR connection into .NET code on the server as the file is read from the stream.</span></span> <span data-ttu-id="675f9-136">[`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) umožňuje konfigurovat charakteristiky nahrávání souborů pro Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="675f9-136">[`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) allows configuring file upload characteristics for Blazor Server.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="675f9-137">Další materiály</span><span class="sxs-lookup"><span data-stu-id="675f9-137">Additional resources</span></span>

* <xref:mvc/models/file-uploads#security-considerations>
* <xref:blazor/forms-validation>
