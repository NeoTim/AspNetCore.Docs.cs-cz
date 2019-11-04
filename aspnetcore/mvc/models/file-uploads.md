---
title: Nahrání souborů v ASP.NET Core
author: guardrex
description: Jak používat vazbu modelu a streamování k nahrávání souborů v ASP.NET Core MVC
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/31/2019
uid: mvc/models/file-uploads
ms.openlocfilehash: 04e7533aa190a4875d3f66e8665fec16abec48b3
ms.sourcegitcommit: 9e85c2562df5e108d7933635c830297f484bb775
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73462937"
---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="1eb10-103">Nahrání souborů v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1eb10-103">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="1eb10-104">Od [Luke Latham](https://github.com/guardrex), [Steve Smith](https://ardalis.com/)a [Rutger](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="1eb10-104">By [Luke Latham](https://github.com/guardrex), [Steve Smith](https://ardalis.com/), and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1eb10-105">ASP.NET Core podporuje nahrávání jednoho nebo více souborů pomocí vazby modelu ve vyrovnávací paměti pro menší soubory a streamování bez vyrovnávací paměti pro větší soubory.</span><span class="sxs-lookup"><span data-stu-id="1eb10-105">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="1eb10-106">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1eb10-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="1eb10-107">Důležité informace o zabezpečení</span><span class="sxs-lookup"><span data-stu-id="1eb10-107">Security considerations</span></span>

<span data-ttu-id="1eb10-108">Pokud chcete uživatelům poskytnout možnost nahrávat soubory na server, buďte opatrní.</span><span class="sxs-lookup"><span data-stu-id="1eb10-108">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="1eb10-109">Útočníci se můžou pokusit:</span><span class="sxs-lookup"><span data-stu-id="1eb10-109">Attackers may attempt to:</span></span>

* <span data-ttu-id="1eb10-110">Vykoná útok [DOS (Denial of Service](/windows-hardware/drivers/ifs/denial-of-service) ).</span><span class="sxs-lookup"><span data-stu-id="1eb10-110">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="1eb10-111">Nahrání virů nebo malwaru</span><span class="sxs-lookup"><span data-stu-id="1eb10-111">Upload viruses or malware.</span></span>
* <span data-ttu-id="1eb10-112">Narušit sítě a servery jinými způsoby.</span><span class="sxs-lookup"><span data-stu-id="1eb10-112">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="1eb10-113">Bezpečnostní kroky, které snižují pravděpodobnost úspěšného útoku, jsou:</span><span class="sxs-lookup"><span data-stu-id="1eb10-113">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="1eb10-114">Nahrajte soubory do vyhrazené oblasti pro nahrávání souborů, nejlépe do nesystémové jednotky.</span><span class="sxs-lookup"><span data-stu-id="1eb10-114">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="1eb10-115">Vyhrazené umístění usnadňuje omezení zabezpečení pro nahrané soubory.</span><span class="sxs-lookup"><span data-stu-id="1eb10-115">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="1eb10-116">Zakažte oprávnění EXECUTE pro umístění pro nahrání souboru. &dagger;</span><span class="sxs-lookup"><span data-stu-id="1eb10-116">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="1eb10-117">Neuchovávat nahrané soubory ve stejném adresářovém stromu jako aplikace.&dagger;</span><span class="sxs-lookup"><span data-stu-id="1eb10-117">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="1eb10-118">Použijte název bezpečného souboru určený aplikací.</span><span class="sxs-lookup"><span data-stu-id="1eb10-118">Use a safe file name determined by the app.</span></span> <span data-ttu-id="1eb10-119">Nepoužívejte název souboru poskytnutý uživatelem nebo nedůvěryhodného názvu nahraného souboru.&dagger; při zobrazení kódování HTML kódovat název nedůvěryhodného souboru.</span><span class="sxs-lookup"><span data-stu-id="1eb10-119">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="1eb10-120">Například protokolování názvu souboru nebo zobrazení v uživatelském rozhraní (Razor automaticky kóduje výstup HTML).</span><span class="sxs-lookup"><span data-stu-id="1eb10-120">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="1eb10-121">Povolte pro specifikaci návrhu aplikace jenom schválené přípony souborů.&dagger;</span><span class="sxs-lookup"><span data-stu-id="1eb10-121">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="1eb10-122">Ověřte, zda jsou na serveru provedeny kontroly na straně klienta.&dagger; kontroly na straně klienta se snadno obejít.</span><span class="sxs-lookup"><span data-stu-id="1eb10-122">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="1eb10-123">Ověřte velikost nahraného souboru.</span><span class="sxs-lookup"><span data-stu-id="1eb10-123">Check the size of an uploaded file.</span></span> <span data-ttu-id="1eb10-124">Nastavte limit maximální velikosti, aby se zabránilo velkým nahrávání.&dagger;</span><span class="sxs-lookup"><span data-stu-id="1eb10-124">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="1eb10-125">Pokud by soubory neměly být přepsány nahraným souborem se stejným názvem, před nahráním souboru ověřte název souboru proti databázi nebo fyzickému úložišti.</span><span class="sxs-lookup"><span data-stu-id="1eb10-125">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="1eb10-126">**Před uložením souboru spusťte v nahraném obsahu skener virů nebo malwaru.**</span><span class="sxs-lookup"><span data-stu-id="1eb10-126">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="1eb10-127">Ukázková aplikace &dagger;The ukazuje přístup, který splňuje kritéria.</span><span class="sxs-lookup"><span data-stu-id="1eb10-127">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="1eb10-128">Nahrávání škodlivého kódu do systému je často prvním krokem ke spuštění kódu, který může:</span><span class="sxs-lookup"><span data-stu-id="1eb10-128">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="1eb10-129">Zcela získá kontrolu nad systémem.</span><span class="sxs-lookup"><span data-stu-id="1eb10-129">Completely gain control of a system.</span></span>
> * <span data-ttu-id="1eb10-130">Přetížit systém s výsledkem, že dojde k chybě systému.</span><span class="sxs-lookup"><span data-stu-id="1eb10-130">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="1eb10-131">Napadnout data uživatelů nebo systémových dat.</span><span class="sxs-lookup"><span data-stu-id="1eb10-131">Compromise user or system data.</span></span>
> * <span data-ttu-id="1eb10-132">Použijte graffiti pro veřejné uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="1eb10-132">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="1eb10-133">Informace o omezení oblasti útoku při přijímání souborů uživateli najdete v následujících zdrojích informací:</span><span class="sxs-lookup"><span data-stu-id="1eb10-133">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="1eb10-134">Neomezená nahrávání souboru</span><span class="sxs-lookup"><span data-stu-id="1eb10-134">Unrestricted File Upload</span></span>](https://www.owasp.org/index.php/Unrestricted_File_Upload)
> * [<span data-ttu-id="1eb10-135">Zabezpečení Azure: Ujistěte se, že jsou při přijímání souborů od uživatelů k dismístě správné ovládací prvky.</span><span class="sxs-lookup"><span data-stu-id="1eb10-135">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="1eb10-136">Další informace o implementaci bezpečnostních opatření, včetně příkladů z ukázkové aplikace, najdete v části [ověření](#validation) .</span><span class="sxs-lookup"><span data-stu-id="1eb10-136">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="1eb10-137">Scénáře úložiště</span><span class="sxs-lookup"><span data-stu-id="1eb10-137">Storage scenarios</span></span>

<span data-ttu-id="1eb10-138">Mezi běžné možnosti úložiště pro soubory patří:</span><span class="sxs-lookup"><span data-stu-id="1eb10-138">Common storage options for files include:</span></span>

* <span data-ttu-id="1eb10-139">Databáze</span><span class="sxs-lookup"><span data-stu-id="1eb10-139">Database</span></span>

  * <span data-ttu-id="1eb10-140">U malých nahrávání souborů je databáze často rychlejší než možnosti fyzického úložiště (systému souborů nebo síťového sdílení).</span><span class="sxs-lookup"><span data-stu-id="1eb10-140">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="1eb10-141">Databáze je často pohodlnější než možnosti fyzického úložiště, protože načtení záznamu databáze pro uživatelská data může současně poskytovat obsah souboru (například obrázek miniatury).</span><span class="sxs-lookup"><span data-stu-id="1eb10-141">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="1eb10-142">Databáze je potenciálně levnější než použití služby úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="1eb10-142">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="1eb10-143">Fyzické úložiště (systém souborů nebo síťová sdílená složka)</span><span class="sxs-lookup"><span data-stu-id="1eb10-143">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="1eb10-144">Pro nahrávání velkých souborů:</span><span class="sxs-lookup"><span data-stu-id="1eb10-144">For large file uploads:</span></span>
    * <span data-ttu-id="1eb10-145">Omezení databáze mohou omezit velikost nahrávání.</span><span class="sxs-lookup"><span data-stu-id="1eb10-145">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="1eb10-146">Fyzické úložiště je často méně hospodárné než úložiště v databázi.</span><span class="sxs-lookup"><span data-stu-id="1eb10-146">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="1eb10-147">Fyzické úložiště je potenciálně levnější než použití služby úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="1eb10-147">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="1eb10-148">Proces aplikace musí mít oprávnění ke čtení a zápisu do umístění úložiště.</span><span class="sxs-lookup"><span data-stu-id="1eb10-148">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="1eb10-149">**Nikdy neudělujte oprávnění EXECUTE.**</span><span class="sxs-lookup"><span data-stu-id="1eb10-149">**Never grant execute permission.**</span></span>

* <span data-ttu-id="1eb10-150">Služba úložiště dat (například [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="1eb10-150">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="1eb10-151">Služby obvykle nabízejí vylepšenou škálovatelnost a odolnost proti místním řešením, které obvykle podléhají jednomu bodu selhání.</span><span class="sxs-lookup"><span data-stu-id="1eb10-151">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="1eb10-152">Služby jsou potenciálně nižší náklady ve scénářích infrastruktury velkých úložišť.</span><span class="sxs-lookup"><span data-stu-id="1eb10-152">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="1eb10-153">Další informace najdete v tématu [rychlý Start: použití .NET k vytvoření objektu BLOB v úložišti objektů](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="1eb10-153">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="1eb10-154">Téma ukazuje <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, ale <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> lze použít k uložení <xref:System.IO.FileStream> do úložiště objektů BLOB při práci s <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="1eb10-154">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="1eb10-155">Scénáře nahrávání souborů</span><span class="sxs-lookup"><span data-stu-id="1eb10-155">File upload scenarios</span></span>

<span data-ttu-id="1eb10-156">Dva obecné přístupy k nahrávání souborů jsou ukládání do vyrovnávací paměti a streamování.</span><span class="sxs-lookup"><span data-stu-id="1eb10-156">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="1eb10-157">**Do vyrovnávací paměti**</span><span class="sxs-lookup"><span data-stu-id="1eb10-157">**Buffering**</span></span>

<span data-ttu-id="1eb10-158">Celý soubor se přečte do <xref:Microsoft.AspNetCore.Http.IFormFile>, což je C# reprezentace souboru, který se používá ke zpracování nebo uložení souboru.</span><span class="sxs-lookup"><span data-stu-id="1eb10-158">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="1eb10-159">Prostředky (disk, paměť) používané při nahrávání souborů závisí na počtu a velikosti souběžných nahrávání souborů.</span><span class="sxs-lookup"><span data-stu-id="1eb10-159">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="1eb10-160">Pokud se aplikace pokusí do vyrovnávací paměti příliš mnoho nahrávání, dojde k selhání lokality, když dojde k vynechání paměti nebo místa na disku.</span><span class="sxs-lookup"><span data-stu-id="1eb10-160">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="1eb10-161">Pokud velikost nebo frekvence nahrávání souborů vyčerpá prostředky aplikace, použijte streamování.</span><span class="sxs-lookup"><span data-stu-id="1eb10-161">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="1eb10-162">Z paměti do dočasného souboru na disku se přesune libovolný soubor s vyrovnávací pamětí větší než 64 KB.</span><span class="sxs-lookup"><span data-stu-id="1eb10-162">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="1eb10-163">Ukládání malých souborů do vyrovnávací paměti je popsáno v následujících částech tohoto tématu:</span><span class="sxs-lookup"><span data-stu-id="1eb10-163">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="1eb10-164">Fyzické úložiště</span><span class="sxs-lookup"><span data-stu-id="1eb10-164">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="1eb10-165">Databáze</span><span class="sxs-lookup"><span data-stu-id="1eb10-165">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="1eb10-166">**Streamování**</span><span class="sxs-lookup"><span data-stu-id="1eb10-166">**Streaming**</span></span>

<span data-ttu-id="1eb10-167">Soubor se přijímá z požadavku na více částí a přímo se zpracovává nebo ukládá v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1eb10-167">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="1eb10-168">Streamování nijak významně nezvyšuje výkon.</span><span class="sxs-lookup"><span data-stu-id="1eb10-168">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="1eb10-169">Streamování snižuje nároky na paměť nebo místo na disku při nahrávání souborů.</span><span class="sxs-lookup"><span data-stu-id="1eb10-169">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="1eb10-170">Streamování velkých souborů je zahrnuté v části [nahrávání velkých souborů pomocí streamování](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="1eb10-170">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="1eb10-171">Nahrávání malých souborů s vazbou modelu ve vyrovnávací paměti na fyzické úložiště</span><span class="sxs-lookup"><span data-stu-id="1eb10-171">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="1eb10-172">Pro nahrání malých souborů použijte formulář s více částmi nebo sestavte požadavek POST pomocí JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="1eb10-172">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="1eb10-173">Následující příklad ukazuje použití formuláře Razor Pages k nahrání jednoho souboru (*Pages/BufferedSingleFileUploadPhysical. cshtml* do ukázkové aplikace):</span><span class="sxs-lookup"><span data-stu-id="1eb10-173">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

<span data-ttu-id="1eb10-174">Následující příklad je podobný předchozímu příkladu s tím rozdílem, že:</span><span class="sxs-lookup"><span data-stu-id="1eb10-174">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="1eb10-175">K odeslání dat formuláře se používá JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)).</span><span class="sxs-lookup"><span data-stu-id="1eb10-175">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="1eb10-176">Neexistuje žádné ověření.</span><span class="sxs-lookup"><span data-stu-id="1eb10-176">There's no validation.</span></span>

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

<span data-ttu-id="1eb10-177">Chcete-li provést příspěvek formuláře v jazyce JavaScript pro klienty, kteří [nepodporují rozhraní API pro načítání](https://caniuse.com/#feat=fetch), použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="1eb10-177">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="1eb10-178">Použijte načtenou výplň (například [window. Fetch Fill (GitHub/Fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="1eb10-178">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="1eb10-179">Použijte `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="1eb10-179">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="1eb10-180">Příklad:</span><span class="sxs-lookup"><span data-stu-id="1eb10-180">For example:</span></span>

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

<span data-ttu-id="1eb10-181">Aby bylo možné podporovat nahrávání souborů, musí formuláře HTML určovat typ kódování (`enctype`) `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="1eb10-181">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="1eb10-182">V případě, že vstupní prvek `files` podporuje nahrávání více souborů, zadejte atribut `multiple` pro element `<input>`:</span><span class="sxs-lookup"><span data-stu-id="1eb10-182">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="1eb10-183">Jednotlivé soubory nahrané na server jsou k dispozici prostřednictvím [vazby modelu](xref:mvc/models/model-binding) pomocí <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="1eb10-183">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="1eb10-184">Ukázková aplikace ukazuje více ukládání souborů do vyrovnávací paměti pro scénáře databáze a fyzických úložišť.</span><span class="sxs-lookup"><span data-stu-id="1eb10-184">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="1eb10-185">Nepoužívejte **vlastnost** `FileName` <xref:Microsoft.AspNetCore.Http.IFormFile> jinou než pro zobrazení a protokolování.</span><span class="sxs-lookup"><span data-stu-id="1eb10-185">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="1eb10-186">Při zobrazení nebo protokolování je název souboru kódován HTML.</span><span class="sxs-lookup"><span data-stu-id="1eb10-186">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="1eb10-187">Útočník může poskytnout škodlivý název souboru, včetně úplných cest nebo relativních cest.</span><span class="sxs-lookup"><span data-stu-id="1eb10-187">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="1eb10-188">Aplikace by měly:</span><span class="sxs-lookup"><span data-stu-id="1eb10-188">Applications should:</span></span>
>
> * <span data-ttu-id="1eb10-189">Odeberte cestu z názvu souboru zadaného uživatelem.</span><span class="sxs-lookup"><span data-stu-id="1eb10-189">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="1eb10-190">Uložte název souboru s příponou PATH s kódováním HTML pro uživatelské rozhraní nebo protokolování.</span><span class="sxs-lookup"><span data-stu-id="1eb10-190">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="1eb10-191">Vygenerujte nový náhodný název souboru pro úložiště.</span><span class="sxs-lookup"><span data-stu-id="1eb10-191">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="1eb10-192">Následující kód odstraní cestu z názvu souboru:</span><span class="sxs-lookup"><span data-stu-id="1eb10-192">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="1eb10-193">Zde uvedené příklady neberou ohled na zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="1eb10-193">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="1eb10-194">Další informace jsou k dispozici v následujících částech a [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="1eb10-194">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="1eb10-195">Důležité informace o zabezpečení</span><span class="sxs-lookup"><span data-stu-id="1eb10-195">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="1eb10-196">Ověřování</span><span class="sxs-lookup"><span data-stu-id="1eb10-196">Validation</span></span>](#validation)

<span data-ttu-id="1eb10-197">Při nahrávání souborů pomocí vazeb modelů a <xref:Microsoft.AspNetCore.Http.IFormFile> může metoda Action přijmout:</span><span class="sxs-lookup"><span data-stu-id="1eb10-197">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="1eb10-198">Jeden <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="1eb10-198">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="1eb10-199">Kterákoli z následujících kolekcí, které reprezentují několik souborů:</span><span class="sxs-lookup"><span data-stu-id="1eb10-199">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="1eb10-200">[Seznam](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="1eb10-200">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="1eb10-201">Vazba odpovídá souborům formuláře podle názvu.</span><span class="sxs-lookup"><span data-stu-id="1eb10-201">Binding matches form files by name.</span></span> <span data-ttu-id="1eb10-202">Například hodnota HTML `name` v `<input type="file" name="formFile">` se musí shodovat s C# parametrem nebo vazbou vlastnosti (`FormFile`).</span><span class="sxs-lookup"><span data-stu-id="1eb10-202">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="1eb10-203">Další informace naleznete v části [název atributu matched na název parametru metody post](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="1eb10-203">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="1eb10-204">Následující příklad:</span><span class="sxs-lookup"><span data-stu-id="1eb10-204">The following example:</span></span>

* <span data-ttu-id="1eb10-205">Projde jedním nebo více nahranými soubory.</span><span class="sxs-lookup"><span data-stu-id="1eb10-205">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="1eb10-206">Pomocí [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) vrátí úplnou cestu k souboru, včetně názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="1eb10-206">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="1eb10-207">Uloží soubory do místního systému souborů pomocí názvu souboru generovaného aplikací.</span><span class="sxs-lookup"><span data-stu-id="1eb10-207">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="1eb10-208">Vrátí celkový počet nahraných souborů a jejich velikost.</span><span class="sxs-lookup"><span data-stu-id="1eb10-208">Returns the total number and size of files uploaded.</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size, filePath });
}
```

<span data-ttu-id="1eb10-209">K vygenerování názvu souboru bez cesty použijte `Path.GetRandomFileName`.</span><span class="sxs-lookup"><span data-stu-id="1eb10-209">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="1eb10-210">V následujícím příkladu je cesta získána z konfigurace:</span><span class="sxs-lookup"><span data-stu-id="1eb10-210">In the following example, the path is obtained from configuration:</span></span>

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

<span data-ttu-id="1eb10-211">Cesta předaná <xref:System.IO.FileStream> *musí* zahrnovat název souboru.</span><span class="sxs-lookup"><span data-stu-id="1eb10-211">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="1eb10-212">Pokud není zadán název souboru, je při spuštění vyvolána hodnota <xref:System.UnauthorizedAccessException>.</span><span class="sxs-lookup"><span data-stu-id="1eb10-212">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="1eb10-213">Soubory odeslané pomocí techniky <xref:Microsoft.AspNetCore.Http.IFormFile> jsou uloženy do vyrovnávací paměti nebo na disku na serveru před zpracováním.</span><span class="sxs-lookup"><span data-stu-id="1eb10-213">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="1eb10-214">V rámci metody Action je obsah <xref:Microsoft.AspNetCore.Http.IFormFile> přístupný jako <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="1eb10-214">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="1eb10-215">Kromě místního systému souborů je možné soubory ukládat do síťové sdílené složky nebo do služby úložiště souborů, jako je [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="1eb10-215">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="1eb10-216">Další příklad, který projde několik souborů pro nahrání a používá bezpečné názvy souborů, najdete v ukázkové aplikaci v části *Pages/BufferedMultipleFileUploadPhysical. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="1eb10-216">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="1eb10-217">[Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) vyvolá výjimku <xref:System.IO.IOException>, pokud jsou vytvořeny více než 65 535 souborů bez odstranění předchozích dočasných souborů.</span><span class="sxs-lookup"><span data-stu-id="1eb10-217">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="1eb10-218">Limit 65 535 souborů je omezen na server.</span><span class="sxs-lookup"><span data-stu-id="1eb10-218">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="1eb10-219">Další informace o tomto limitu pro operační systém Windows najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="1eb10-219">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="1eb10-220">GetTempFileNameA – funkce</span><span class="sxs-lookup"><span data-stu-id="1eb10-220">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="1eb10-221">Nahrávání malých souborů s vazbou modelu s vyrovnávací pamětí do databáze</span><span class="sxs-lookup"><span data-stu-id="1eb10-221">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="1eb10-222">Chcete-li uložit data binárního souboru do databáze pomocí [Entity Framework](/ef/core/index), definujte v entitě vlastnost pole <xref:System.Byte>:</span><span class="sxs-lookup"><span data-stu-id="1eb10-222">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="1eb10-223">Zadejte vlastnost modelu stránky pro třídu, která obsahuje <xref:Microsoft.AspNetCore.Http.IFormFile>:</span><span class="sxs-lookup"><span data-stu-id="1eb10-223">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="1eb10-224"><xref:Microsoft.AspNetCore.Http.IFormFile> lze použít přímo jako parametr metody akce nebo jako vlastnost vázaného modelu.</span><span class="sxs-lookup"><span data-stu-id="1eb10-224"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="1eb10-225">Předchozí příklad používá vlastnost vázaného modelu.</span><span class="sxs-lookup"><span data-stu-id="1eb10-225">The prior example uses a bound model property.</span></span>

<span data-ttu-id="1eb10-226">Ve formuláři Razor Pages se používá `FileUpload`:</span><span class="sxs-lookup"><span data-stu-id="1eb10-226">The `FileUpload` is used in the Razor Pages form:</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

<span data-ttu-id="1eb10-227">Když je formulář publikovaný na serveru, zkopírujte <xref:Microsoft.AspNetCore.Http.IFormFile> do datového proudu a uložte ho jako pole bajtů v databázi.</span><span class="sxs-lookup"><span data-stu-id="1eb10-227">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="1eb10-228">V následujícím příkladu `_dbContext` ukládá kontext databáze aplikace:</span><span class="sxs-lookup"><span data-stu-id="1eb10-228">In the following example, `_dbContext` stores the app's database context:</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

<span data-ttu-id="1eb10-229">Předchozí příklad je podobný scénáři, který je znázorněný v ukázkové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="1eb10-229">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="1eb10-230">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="1eb10-230">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="1eb10-231">*Pages/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="1eb10-231">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="1eb10-232">Při ukládání binárních dat do relačních databází buďte opatrní, protože to může mít nepříznivý vliv na výkon.</span><span class="sxs-lookup"><span data-stu-id="1eb10-232">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="1eb10-233">Nespoléhá se ani na nedůvěryhodnou vlastnost `FileName` <xref:Microsoft.AspNetCore.Http.IFormFile> bez ověření.</span><span class="sxs-lookup"><span data-stu-id="1eb10-233">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="1eb10-234">Vlastnost `FileName` by měla být použita pouze pro účely zobrazení a pouze po kódování HTML.</span><span class="sxs-lookup"><span data-stu-id="1eb10-234">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="1eb10-235">Uvedené příklady nevezmou ohled na zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="1eb10-235">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="1eb10-236">Další informace jsou k dispozici v následujících částech a [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="1eb10-236">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="1eb10-237">Důležité informace o zabezpečení</span><span class="sxs-lookup"><span data-stu-id="1eb10-237">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="1eb10-238">Ověřování</span><span class="sxs-lookup"><span data-stu-id="1eb10-238">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="1eb10-239">Nahrávání velkých souborů pomocí streamování</span><span class="sxs-lookup"><span data-stu-id="1eb10-239">Upload large files with streaming</span></span>

<span data-ttu-id="1eb10-240">Následující příklad ukazuje, jak použít JavaScript ke streamování souboru do akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="1eb10-240">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="1eb10-241">Token proti padělání souboru se generuje pomocí vlastního atributu filtru a předává se do hlaviček protokolu HTTP klienta místo v textu žádosti.</span><span class="sxs-lookup"><span data-stu-id="1eb10-241">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="1eb10-242">Vzhledem k tomu, že metoda akce zpracovává nahraná data přímo, vazba modelu formuláře je zakázána jiným vlastním filtrem.</span><span class="sxs-lookup"><span data-stu-id="1eb10-242">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="1eb10-243">V rámci akce je obsah formuláře čten pomocí `MultipartReader`, který čte každou jednotlivou `MultipartSection`, zpracovává soubor nebo ukládá obsah podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="1eb10-243">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="1eb10-244">Po načtení oddílů s více částmi provede akce vlastní vazbu modelu.</span><span class="sxs-lookup"><span data-stu-id="1eb10-244">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="1eb10-245">Počáteční odpověď stránky načte formulář a uloží token proti padělání do souboru cookie (prostřednictvím atributu `GenerateAntiforgeryTokenCookieAttribute`).</span><span class="sxs-lookup"><span data-stu-id="1eb10-245">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="1eb10-246">Atribut používá ASP.NET Core integrovanou [podporu proti padělání](xref:security/anti-request-forgery) pro nastavení souboru cookie s tokenem žádosti:</span><span class="sxs-lookup"><span data-stu-id="1eb10-246">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="1eb10-247">`DisableFormValueModelBindingAttribute` slouží k zakázání vazby modelu:</span><span class="sxs-lookup"><span data-stu-id="1eb10-247">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="1eb10-248">V ukázkové aplikaci `GenerateAntiforgeryTokenCookieAttribute` a `DisableFormValueModelBindingAttribute` se aplikují jako filtry pro modely stránky aplikace `/StreamedSingleFileUploadDb` a `/StreamedSingleFileUploadPhysical` v `Startup.ConfigureServices` pomocí [konvencí Razor Pages](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="1eb10-248">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=8-11,17-20)]

<span data-ttu-id="1eb10-249">Vzhledem k tomu, že vazba modelu nepřečte formulář, parametry, které jsou svázané z formuláře, se nezobrazují (budou pokračovat v práci s dotazem, trasou a hlavičkou).</span><span class="sxs-lookup"><span data-stu-id="1eb10-249">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="1eb10-250">Metoda Action funguje přímo s vlastností `Request`.</span><span class="sxs-lookup"><span data-stu-id="1eb10-250">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="1eb10-251">Pro čtení jednotlivých oddílů se používá `MultipartReader`.</span><span class="sxs-lookup"><span data-stu-id="1eb10-251">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="1eb10-252">Data klíč/hodnota jsou uložena v `KeyValueAccumulator`.</span><span class="sxs-lookup"><span data-stu-id="1eb10-252">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="1eb10-253">Po načtení oddílů s více částmi se obsah `KeyValueAccumulator` použije pro svázání dat formuláře s typem modelu.</span><span class="sxs-lookup"><span data-stu-id="1eb10-253">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="1eb10-254">Kompletní metoda `StreamingController.UploadDatabase` pro streamování do databáze s EF Core:</span><span class="sxs-lookup"><span data-stu-id="1eb10-254">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="1eb10-255">`MultipartRequestHelper` (*nástroje/MultipartRequestHelper. cs*):</span><span class="sxs-lookup"><span data-stu-id="1eb10-255">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="1eb10-256">Úplná metoda `StreamingController.UploadPhysical` pro streamování do fyzického umístění:</span><span class="sxs-lookup"><span data-stu-id="1eb10-256">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="1eb10-257">V ukázkové aplikaci jsou kontroly ověřování zpracovávány `FileHelpers.ProcessStreamedFile`.</span><span class="sxs-lookup"><span data-stu-id="1eb10-257">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="1eb10-258">Ověřování</span><span class="sxs-lookup"><span data-stu-id="1eb10-258">Validation</span></span>

<span data-ttu-id="1eb10-259">Třída `FileHelpers` ukázkové aplikace ukazuje několik kontrol uložených do vyrovnávací paměti <xref:Microsoft.AspNetCore.Http.IFormFile> a streamovaná nahrávání souborů.</span><span class="sxs-lookup"><span data-stu-id="1eb10-259">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="1eb10-260">Pro zpracování <xref:Microsoft.AspNetCore.Http.IFormFile> ukládání souborů do vyrovnávací paměti v ukázkové aplikaci si přečtěte část `ProcessFormFile` v souboru *. cs nástrojů Utilities/App.*</span><span class="sxs-lookup"><span data-stu-id="1eb10-260">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="1eb10-261">Pro zpracování streamované soubory si Projděte metodu `ProcessStreamedFile` ve stejném souboru.</span><span class="sxs-lookup"><span data-stu-id="1eb10-261">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="1eb10-262">Metody zpracování ověřování, které jsou znázorněné v ukázkové aplikaci, nekontrolují obsah nahraných souborů.</span><span class="sxs-lookup"><span data-stu-id="1eb10-262">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="1eb10-263">Ve většině produkčních scénářů se v souboru používá rozhraní API pro skenování virů nebo malwaru, než je soubor dostupný uživatelům nebo jiným systémům.</span><span class="sxs-lookup"><span data-stu-id="1eb10-263">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="1eb10-264">I když ukázka tématu poskytuje pracovní příklad technik ověřování, Neimplementujte třídu `FileHelpers` v produkční aplikaci, pokud:</span><span class="sxs-lookup"><span data-stu-id="1eb10-264">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="1eb10-265">Plně rozumíte implementaci.</span><span class="sxs-lookup"><span data-stu-id="1eb10-265">Fully understand the implementation.</span></span>
> * <span data-ttu-id="1eb10-266">Upravte implementaci podle potřeby pro prostředí a specifikace aplikace.</span><span class="sxs-lookup"><span data-stu-id="1eb10-266">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="1eb10-267">**Nikdy nepoužívejte nerozlišený kód zabezpečení v aplikaci bez nutnosti řešit tyto požadavky.**</span><span class="sxs-lookup"><span data-stu-id="1eb10-267">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="1eb10-268">Ověření obsahu</span><span class="sxs-lookup"><span data-stu-id="1eb10-268">Content validation</span></span>

<span data-ttu-id="1eb10-269">**Pro nahraný obsah použijte rozhraní API pro kontrolu virů a malwaru třetí strany.**</span><span class="sxs-lookup"><span data-stu-id="1eb10-269">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="1eb10-270">Prohledávání souborů je náročné na prostředky serveru ve scénářích s vysokým objemem.</span><span class="sxs-lookup"><span data-stu-id="1eb10-270">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="1eb10-271">Pokud dojde ke snížení výkonu zpracování požadavků z důvodu kontroly souborů, zvažte přesměrování práce skenování na službu na [pozadí](xref:fundamentals/host/hosted-services), případně služby spuštěné na serveru, který se liší od serveru aplikace.</span><span class="sxs-lookup"><span data-stu-id="1eb10-271">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="1eb10-272">Nahrané soubory se obvykle uchovávají v oblasti v karanténě, dokud je kontrola virů na pozadí nevrátí.</span><span class="sxs-lookup"><span data-stu-id="1eb10-272">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="1eb10-273">Když soubor projde, soubor se přesune do normálního umístění úložiště souborů.</span><span class="sxs-lookup"><span data-stu-id="1eb10-273">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="1eb10-274">Tyto kroky se obvykle provádí ve spojení s databázovým záznamem, který indikuje stav kontroly souboru.</span><span class="sxs-lookup"><span data-stu-id="1eb10-274">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="1eb10-275">Při použití takového přístupu zůstane aplikace a Server App zaměřené na reakci na požadavky.</span><span class="sxs-lookup"><span data-stu-id="1eb10-275">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="1eb10-276">Ověření přípony souboru</span><span class="sxs-lookup"><span data-stu-id="1eb10-276">File extension validation</span></span>

<span data-ttu-id="1eb10-277">Přípona nahraného souboru by měla být zaškrtnutá na seznamu povolených rozšíření.</span><span class="sxs-lookup"><span data-stu-id="1eb10-277">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="1eb10-278">Příklad:</span><span class="sxs-lookup"><span data-stu-id="1eb10-278">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="1eb10-279">Ověření podpisu souboru</span><span class="sxs-lookup"><span data-stu-id="1eb10-279">File signature validation</span></span>

<span data-ttu-id="1eb10-280">Podpis souboru se určuje na prvních několika bajtech na začátku souboru.</span><span class="sxs-lookup"><span data-stu-id="1eb10-280">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="1eb10-281">Tyto bajty lze použít k určení, zda přípona odpovídá obsahu souboru.</span><span class="sxs-lookup"><span data-stu-id="1eb10-281">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="1eb10-282">Ukázková aplikace zkontroluje podpisy souborů pro několik běžných typů souborů.</span><span class="sxs-lookup"><span data-stu-id="1eb10-282">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="1eb10-283">V následujícím příkladu se pro tento soubor kontroluje podpis souboru obrázku JPEG:</span><span class="sxs-lookup"><span data-stu-id="1eb10-283">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

<span data-ttu-id="1eb10-284">Další signatury souborů získáte v dokumentaci [signatury souborů](https://www.filesignatures.net/) a oficiálních souborů.</span><span class="sxs-lookup"><span data-stu-id="1eb10-284">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="1eb10-285">Zabezpečení názvu souboru</span><span class="sxs-lookup"><span data-stu-id="1eb10-285">File name security</span></span>

<span data-ttu-id="1eb10-286">Nikdy nepoužívejte název souboru dodaný klientem pro uložení souboru do fyzického úložiště.</span><span class="sxs-lookup"><span data-stu-id="1eb10-286">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="1eb10-287">Vytvořte bezpečný název souboru pro soubor pomocí [cesty. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) nebo [cesty. GetTempFileName](xref:System.IO.Path.GetTempFileName*) a vytvořte úplnou cestu (včetně názvu souboru) pro dočasné úložiště.</span><span class="sxs-lookup"><span data-stu-id="1eb10-287">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="1eb10-288">Razor automaticky kóduje hodnoty vlastností pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="1eb10-288">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="1eb10-289">Následující kód je bezpečné použít:</span><span class="sxs-lookup"><span data-stu-id="1eb10-289">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="1eb10-290">Mimo Razor, vždy <xref:System.Net.WebUtility.HtmlEncode*> obsah názvu souboru z požadavku uživatele.</span><span class="sxs-lookup"><span data-stu-id="1eb10-290">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="1eb10-291">Mnoho implementací musí zahrnovat kontrolu, že soubor existuje. v opačném případě je soubor přepsán souborem se stejným názvem.</span><span class="sxs-lookup"><span data-stu-id="1eb10-291">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="1eb10-292">Poskytněte další logiku pro splnění specifikací vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="1eb10-292">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="1eb10-293">Ověřování velikosti</span><span class="sxs-lookup"><span data-stu-id="1eb10-293">Size validation</span></span>

<span data-ttu-id="1eb10-294">Omezte velikost nahraných souborů.</span><span class="sxs-lookup"><span data-stu-id="1eb10-294">Limit the size of uploaded files.</span></span>

<span data-ttu-id="1eb10-295">V ukázkové aplikaci je velikost souboru omezená na 2 MB (uvedené v bajtech).</span><span class="sxs-lookup"><span data-stu-id="1eb10-295">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="1eb10-296">Limit je zadán prostřednictvím [Konfigurace](xref:fundamentals/configuration/index) ze souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="1eb10-296">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="1eb10-297">`FileSizeLimit` je vložen do tříd `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="1eb10-297">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

<span data-ttu-id="1eb10-298">Když velikost souboru překročí limit, soubor se odmítne:</span><span class="sxs-lookup"><span data-stu-id="1eb10-298">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="1eb10-299">Porovnává hodnotu atributu name s parametrem název metody POST</span><span class="sxs-lookup"><span data-stu-id="1eb10-299">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="1eb10-300">V nestandardních formulářích, které PUBLIKují data formuláře nebo přímo používají `FormData` JavaScriptu, musí název zadaný v prvku formuláře nebo `FormData` odpovídat názvu parametru v akci kontroleru.</span><span class="sxs-lookup"><span data-stu-id="1eb10-300">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="1eb10-301">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="1eb10-301">In the following example:</span></span>

* <span data-ttu-id="1eb10-302">Při použití elementu `<input>` je atribut `name` nastaven na hodnotu `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="1eb10-302">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="1eb10-303">Při použití `FormData` v jazyce JavaScript je název nastaven na hodnotu `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="1eb10-303">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="1eb10-304">Pro parametr C# metody použijte stejný název (`battlePlans`):</span><span class="sxs-lookup"><span data-stu-id="1eb10-304">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="1eb10-305">Pro Razor Pages metodu obslužné rutiny stránky s názvem `Upload`:</span><span class="sxs-lookup"><span data-stu-id="1eb10-305">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="1eb10-306">Pro metodu akce po kontroléru MVC:</span><span class="sxs-lookup"><span data-stu-id="1eb10-306">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="1eb10-307">Konfigurace serveru a aplikace</span><span class="sxs-lookup"><span data-stu-id="1eb10-307">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="1eb10-308">Omezení délky těla částí</span><span class="sxs-lookup"><span data-stu-id="1eb10-308">Multipart body length limit</span></span>

<span data-ttu-id="1eb10-309"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> nastaví limit délky jednotlivých částí části.</span><span class="sxs-lookup"><span data-stu-id="1eb10-309"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="1eb10-310">Oddíly formuláře, které překračují toto omezení, vyvolávají při analýze <xref:System.IO.InvalidDataException>.</span><span class="sxs-lookup"><span data-stu-id="1eb10-310">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="1eb10-311">Výchozí hodnota je 134 217 728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="1eb10-311">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="1eb10-312">Upravte limit pomocí nastavení <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="1eb10-312">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<span data-ttu-id="1eb10-313"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> slouží k nastavení <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> pro jednu stránku nebo akci.</span><span class="sxs-lookup"><span data-stu-id="1eb10-313"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="1eb10-314">V aplikaci Razor Pages použijte filtr s [konvencí](xref:razor-pages/razor-pages-conventions) v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="1eb10-314">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model.Filters.Add(
                    new RequestFormLimitsAttribute()
                    {
                        // Set the limit to 256 MB
                        MultipartBodyLengthLimit = 268435456
                    });
    });
```

<span data-ttu-id="1eb10-315">V aplikaci Razor Pages nebo aplikaci MVC použijte filtr na model stránky nebo metodu akce:</span><span class="sxs-lookup"><span data-stu-id="1eb10-315">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="1eb10-316">Kestrel maximální velikost textu požadavku</span><span class="sxs-lookup"><span data-stu-id="1eb10-316">Kestrel maximum request body size</span></span>

<span data-ttu-id="1eb10-317">Pro aplikace hostované v Kestrel je výchozí maximální velikost textu požadavku 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="1eb10-317">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="1eb10-318">Přizpůsobte limit pomocí možnosti serveru [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:</span><span class="sxs-lookup"><span data-stu-id="1eb10-318">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            // Handle requests up to 50 MB
            options.Limits.MaxRequestBodySize = 52428800;
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="1eb10-319"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> slouží k nastavení [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) pro jednu stránku nebo akci.</span><span class="sxs-lookup"><span data-stu-id="1eb10-319"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="1eb10-320">V aplikaci Razor Pages použijte filtr s [konvencí](xref:razor-pages/razor-pages-conventions) v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="1eb10-320">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model =>
                {
                    // Handle requests up to 50 MB
                    model.Filters.Add(
                        new RequestSizeLimitAttribute(52428800));
                });
    });
```

<span data-ttu-id="1eb10-321">V aplikaci se stránkami Razor nebo v aplikaci MVC použijte filtr na třídu obslužné rutiny stránky nebo na metodu akce:</span><span class="sxs-lookup"><span data-stu-id="1eb10-321">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="1eb10-322">`RequestSizeLimitAttribute` lze také použít pomocí direktivy [@attribute](xref:mvc/views/razor#attribute) Razor:</span><span class="sxs-lookup"><span data-stu-id="1eb10-322">The `RequestSizeLimitAttribute` can also be applied using the [@attribute](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="1eb10-323">Další omezení Kestrel</span><span class="sxs-lookup"><span data-stu-id="1eb10-323">Other Kestrel limits</span></span>

<span data-ttu-id="1eb10-324">Pro aplikace hostované v Kestrel se můžou vztahovat další omezení Kestrel:</span><span class="sxs-lookup"><span data-stu-id="1eb10-324">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="1eb10-325">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="1eb10-325">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="1eb10-326">Sazby za data požadavků a odpovědí</span><span class="sxs-lookup"><span data-stu-id="1eb10-326">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="1eb10-327">Omezení délky obsahu služby IIS</span><span class="sxs-lookup"><span data-stu-id="1eb10-327">IIS content length limit</span></span>

<span data-ttu-id="1eb10-328">Výchozí limit počtu požadavků (`maxAllowedContentLength`) je 30 000 000 bajtů, což je přibližně 28.6 MB.</span><span class="sxs-lookup"><span data-stu-id="1eb10-328">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="1eb10-329">Upravte limit v souboru *Web. config* :</span><span class="sxs-lookup"><span data-stu-id="1eb10-329">Customize the limit in the *web.config* file:</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- Handle requests up to 50 MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="1eb10-330">Toto nastavení platí pouze pro službu IIS.</span><span class="sxs-lookup"><span data-stu-id="1eb10-330">This setting only applies to IIS.</span></span> <span data-ttu-id="1eb10-331">K tomuto chování nedochází ve výchozím nastavení při hostování v Kestrel.</span><span class="sxs-lookup"><span data-stu-id="1eb10-331">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="1eb10-332">Další informace najdete v tématu [omezení požadavků \<requestLimits >](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="1eb10-332">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="1eb10-333">Omezení v modulu ASP.NET Core nebo přítomnosti modulu filtrování požadavků služby IIS mohou omezit nahrávání na 2 nebo 4 GB.</span><span class="sxs-lookup"><span data-stu-id="1eb10-333">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="1eb10-334">Další informace najdete v tématu [nelze odeslat soubor o velikosti větší než 2 GB (ASPNET/AspNetCore #2711)](https://github.com/aspnet/AspNetCore/issues/2711).</span><span class="sxs-lookup"><span data-stu-id="1eb10-334">For more information, see [Unable to upload file greater than 2GB in size (aspnet/AspNetCore #2711)](https://github.com/aspnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="1eb10-335">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="1eb10-335">Troubleshoot</span></span>

<span data-ttu-id="1eb10-336">Níže jsou uvedeny některé běžné problémy, které se vyskytly při práci s nahráváním souborů a jejich možnými řešeními.</span><span class="sxs-lookup"><span data-stu-id="1eb10-336">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="1eb10-337">Při nasazení na server služby IIS se nenašla chyba.</span><span class="sxs-lookup"><span data-stu-id="1eb10-337">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="1eb10-338">Následující chyba znamená, že nahraný soubor překračuje délku nakonfigurovaného obsahu serveru:</span><span class="sxs-lookup"><span data-stu-id="1eb10-338">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="1eb10-339">Další informace o zvýšení limitu najdete v části [omezení délky obsahu služby IIS](#iis-content-length-limit) .</span><span class="sxs-lookup"><span data-stu-id="1eb10-339">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="1eb10-340">Chyba připojení</span><span class="sxs-lookup"><span data-stu-id="1eb10-340">Connection failure</span></span>

<span data-ttu-id="1eb10-341">Chyba připojení a připojení k serveru pro resetování pravděpodobně znamená, že nahraný soubor překračuje maximální velikost textu požadavku Kestrel.</span><span class="sxs-lookup"><span data-stu-id="1eb10-341">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="1eb10-342">Další informace najdete v části [Kestrel maximální velikost textu požadavku](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="1eb10-342">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="1eb10-343">Omezení připojení klientů Kestrel mohou také vyžadovat úpravu.</span><span class="sxs-lookup"><span data-stu-id="1eb10-343">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="1eb10-344">Výjimka odkazu s hodnotou null s IFormFile</span><span class="sxs-lookup"><span data-stu-id="1eb10-344">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="1eb10-345">Pokud kontroler přijímá odeslané soubory pomocí <xref:Microsoft.AspNetCore.Http.IFormFile>, ale hodnota je `null`, potvrďte, že formulář HTML určuje hodnotu `enctype` `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="1eb10-345">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="1eb10-346">Pokud tento atribut není nastaven u elementu `<form>`, odeslání souboru neproběhne a všechny vázané argumenty <xref:Microsoft.AspNetCore.Http.IFormFile> jsou `null`.</span><span class="sxs-lookup"><span data-stu-id="1eb10-346">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="1eb10-347">Ujistěte se také, že [nahrávání názvů v datech formuláře odpovídá pojmenování aplikace](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="1eb10-347">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1eb10-348">ASP.NET Core podporuje nahrávání jednoho nebo více souborů pomocí vazby modelu ve vyrovnávací paměti pro menší soubory a streamování bez vyrovnávací paměti pro větší soubory.</span><span class="sxs-lookup"><span data-stu-id="1eb10-348">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="1eb10-349">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1eb10-349">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="1eb10-350">Důležité informace o zabezpečení</span><span class="sxs-lookup"><span data-stu-id="1eb10-350">Security considerations</span></span>

<span data-ttu-id="1eb10-351">Pokud chcete uživatelům poskytnout možnost nahrávat soubory na server, buďte opatrní.</span><span class="sxs-lookup"><span data-stu-id="1eb10-351">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="1eb10-352">Útočníci se můžou pokusit:</span><span class="sxs-lookup"><span data-stu-id="1eb10-352">Attackers may attempt to:</span></span>

* <span data-ttu-id="1eb10-353">Vykoná útok [DOS (Denial of Service](/windows-hardware/drivers/ifs/denial-of-service) ).</span><span class="sxs-lookup"><span data-stu-id="1eb10-353">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="1eb10-354">Nahrání virů nebo malwaru</span><span class="sxs-lookup"><span data-stu-id="1eb10-354">Upload viruses or malware.</span></span>
* <span data-ttu-id="1eb10-355">Narušit sítě a servery jinými způsoby.</span><span class="sxs-lookup"><span data-stu-id="1eb10-355">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="1eb10-356">Bezpečnostní kroky, které snižují pravděpodobnost úspěšného útoku, jsou:</span><span class="sxs-lookup"><span data-stu-id="1eb10-356">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="1eb10-357">Nahrajte soubory do vyhrazené oblasti pro nahrávání souborů, nejlépe do nesystémové jednotky.</span><span class="sxs-lookup"><span data-stu-id="1eb10-357">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="1eb10-358">Vyhrazené umístění usnadňuje omezení zabezpečení pro nahrané soubory.</span><span class="sxs-lookup"><span data-stu-id="1eb10-358">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="1eb10-359">Zakažte oprávnění EXECUTE pro umístění pro nahrání souboru. &dagger;</span><span class="sxs-lookup"><span data-stu-id="1eb10-359">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="1eb10-360">Neuchovávat nahrané soubory ve stejném adresářovém stromu jako aplikace.&dagger;</span><span class="sxs-lookup"><span data-stu-id="1eb10-360">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="1eb10-361">Použijte název bezpečného souboru určený aplikací.</span><span class="sxs-lookup"><span data-stu-id="1eb10-361">Use a safe file name determined by the app.</span></span> <span data-ttu-id="1eb10-362">Nepoužívejte název souboru poskytnutý uživatelem nebo nedůvěryhodného názvu nahraného souboru.&dagger; při zobrazení kódování HTML kódovat název nedůvěryhodného souboru.</span><span class="sxs-lookup"><span data-stu-id="1eb10-362">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="1eb10-363">Například protokolování názvu souboru nebo zobrazení v uživatelském rozhraní (Razor automaticky kóduje výstup HTML).</span><span class="sxs-lookup"><span data-stu-id="1eb10-363">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="1eb10-364">Povolte pro specifikaci návrhu aplikace jenom schválené přípony souborů.&dagger;</span><span class="sxs-lookup"><span data-stu-id="1eb10-364">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="1eb10-365">Ověřte, zda jsou na serveru provedeny kontroly na straně klienta.&dagger; kontroly na straně klienta se snadno obejít.</span><span class="sxs-lookup"><span data-stu-id="1eb10-365">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="1eb10-366">Ověřte velikost nahraného souboru.</span><span class="sxs-lookup"><span data-stu-id="1eb10-366">Check the size of an uploaded file.</span></span> <span data-ttu-id="1eb10-367">Nastavte limit maximální velikosti, aby se zabránilo velkým nahrávání.&dagger;</span><span class="sxs-lookup"><span data-stu-id="1eb10-367">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="1eb10-368">Pokud by soubory neměly být přepsány nahraným souborem se stejným názvem, před nahráním souboru ověřte název souboru proti databázi nebo fyzickému úložišti.</span><span class="sxs-lookup"><span data-stu-id="1eb10-368">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="1eb10-369">**Před uložením souboru spusťte v nahraném obsahu skener virů nebo malwaru.**</span><span class="sxs-lookup"><span data-stu-id="1eb10-369">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="1eb10-370">Ukázková aplikace &dagger;The ukazuje přístup, který splňuje kritéria.</span><span class="sxs-lookup"><span data-stu-id="1eb10-370">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="1eb10-371">Nahrávání škodlivého kódu do systému je často prvním krokem ke spuštění kódu, který může:</span><span class="sxs-lookup"><span data-stu-id="1eb10-371">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="1eb10-372">Zcela získá kontrolu nad systémem.</span><span class="sxs-lookup"><span data-stu-id="1eb10-372">Completely gain control of a system.</span></span>
> * <span data-ttu-id="1eb10-373">Přetížit systém s výsledkem, že dojde k chybě systému.</span><span class="sxs-lookup"><span data-stu-id="1eb10-373">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="1eb10-374">Napadnout data uživatelů nebo systémových dat.</span><span class="sxs-lookup"><span data-stu-id="1eb10-374">Compromise user or system data.</span></span>
> * <span data-ttu-id="1eb10-375">Použijte graffiti pro veřejné uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="1eb10-375">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="1eb10-376">Informace o omezení oblasti útoku při přijímání souborů uživateli najdete v následujících zdrojích informací:</span><span class="sxs-lookup"><span data-stu-id="1eb10-376">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="1eb10-377">Neomezená nahrávání souboru</span><span class="sxs-lookup"><span data-stu-id="1eb10-377">Unrestricted File Upload</span></span>](https://www.owasp.org/index.php/Unrestricted_File_Upload)
> * [<span data-ttu-id="1eb10-378">Zabezpečení Azure: Ujistěte se, že jsou při přijímání souborů od uživatelů k dismístě správné ovládací prvky.</span><span class="sxs-lookup"><span data-stu-id="1eb10-378">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="1eb10-379">Další informace o implementaci bezpečnostních opatření, včetně příkladů z ukázkové aplikace, najdete v části [ověření](#validation) .</span><span class="sxs-lookup"><span data-stu-id="1eb10-379">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="1eb10-380">Scénáře úložiště</span><span class="sxs-lookup"><span data-stu-id="1eb10-380">Storage scenarios</span></span>

<span data-ttu-id="1eb10-381">Mezi běžné možnosti úložiště pro soubory patří:</span><span class="sxs-lookup"><span data-stu-id="1eb10-381">Common storage options for files include:</span></span>

* <span data-ttu-id="1eb10-382">Databáze</span><span class="sxs-lookup"><span data-stu-id="1eb10-382">Database</span></span>

  * <span data-ttu-id="1eb10-383">U malých nahrávání souborů je databáze často rychlejší než možnosti fyzického úložiště (systému souborů nebo síťového sdílení).</span><span class="sxs-lookup"><span data-stu-id="1eb10-383">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="1eb10-384">Databáze je často pohodlnější než možnosti fyzického úložiště, protože načtení záznamu databáze pro uživatelská data může současně poskytovat obsah souboru (například obrázek miniatury).</span><span class="sxs-lookup"><span data-stu-id="1eb10-384">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="1eb10-385">Databáze je potenciálně levnější než použití služby úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="1eb10-385">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="1eb10-386">Fyzické úložiště (systém souborů nebo síťová sdílená složka)</span><span class="sxs-lookup"><span data-stu-id="1eb10-386">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="1eb10-387">Pro nahrávání velkých souborů:</span><span class="sxs-lookup"><span data-stu-id="1eb10-387">For large file uploads:</span></span>
    * <span data-ttu-id="1eb10-388">Omezení databáze mohou omezit velikost nahrávání.</span><span class="sxs-lookup"><span data-stu-id="1eb10-388">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="1eb10-389">Fyzické úložiště je často méně hospodárné než úložiště v databázi.</span><span class="sxs-lookup"><span data-stu-id="1eb10-389">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="1eb10-390">Fyzické úložiště je potenciálně levnější než použití služby úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="1eb10-390">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="1eb10-391">Proces aplikace musí mít oprávnění ke čtení a zápisu do umístění úložiště.</span><span class="sxs-lookup"><span data-stu-id="1eb10-391">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="1eb10-392">**Nikdy neudělujte oprávnění EXECUTE.**</span><span class="sxs-lookup"><span data-stu-id="1eb10-392">**Never grant execute permission.**</span></span>

* <span data-ttu-id="1eb10-393">Služba úložiště dat (například [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="1eb10-393">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="1eb10-394">Služby obvykle nabízejí vylepšenou škálovatelnost a odolnost proti místním řešením, které obvykle podléhají jednomu bodu selhání.</span><span class="sxs-lookup"><span data-stu-id="1eb10-394">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="1eb10-395">Služby jsou potenciálně nižší náklady ve scénářích infrastruktury velkých úložišť.</span><span class="sxs-lookup"><span data-stu-id="1eb10-395">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="1eb10-396">Další informace najdete v tématu [rychlý Start: použití .NET k vytvoření objektu BLOB v úložišti objektů](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="1eb10-396">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="1eb10-397">Téma ukazuje <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, ale <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> lze použít k uložení <xref:System.IO.FileStream> do úložiště objektů BLOB při práci s <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="1eb10-397">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="1eb10-398">Scénáře nahrávání souborů</span><span class="sxs-lookup"><span data-stu-id="1eb10-398">File upload scenarios</span></span>

<span data-ttu-id="1eb10-399">Dva obecné přístupy k nahrávání souborů jsou ukládání do vyrovnávací paměti a streamování.</span><span class="sxs-lookup"><span data-stu-id="1eb10-399">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="1eb10-400">**Do vyrovnávací paměti**</span><span class="sxs-lookup"><span data-stu-id="1eb10-400">**Buffering**</span></span>

<span data-ttu-id="1eb10-401">Celý soubor se přečte do <xref:Microsoft.AspNetCore.Http.IFormFile>, což je C# reprezentace souboru, který se používá ke zpracování nebo uložení souboru.</span><span class="sxs-lookup"><span data-stu-id="1eb10-401">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="1eb10-402">Prostředky (disk, paměť) používané při nahrávání souborů závisí na počtu a velikosti souběžných nahrávání souborů.</span><span class="sxs-lookup"><span data-stu-id="1eb10-402">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="1eb10-403">Pokud se aplikace pokusí do vyrovnávací paměti příliš mnoho nahrávání, dojde k selhání lokality, když dojde k vynechání paměti nebo místa na disku.</span><span class="sxs-lookup"><span data-stu-id="1eb10-403">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="1eb10-404">Pokud velikost nebo frekvence nahrávání souborů vyčerpá prostředky aplikace, použijte streamování.</span><span class="sxs-lookup"><span data-stu-id="1eb10-404">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="1eb10-405">Z paměti do dočasného souboru na disku se přesune libovolný soubor s vyrovnávací pamětí větší než 64 KB.</span><span class="sxs-lookup"><span data-stu-id="1eb10-405">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="1eb10-406">Ukládání malých souborů do vyrovnávací paměti je popsáno v následujících částech tohoto tématu:</span><span class="sxs-lookup"><span data-stu-id="1eb10-406">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="1eb10-407">Fyzické úložiště</span><span class="sxs-lookup"><span data-stu-id="1eb10-407">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="1eb10-408">Databáze</span><span class="sxs-lookup"><span data-stu-id="1eb10-408">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="1eb10-409">**Streamování**</span><span class="sxs-lookup"><span data-stu-id="1eb10-409">**Streaming**</span></span>

<span data-ttu-id="1eb10-410">Soubor se přijímá z požadavku na více částí a přímo se zpracovává nebo ukládá v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1eb10-410">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="1eb10-411">Streamování nijak významně nezvyšuje výkon.</span><span class="sxs-lookup"><span data-stu-id="1eb10-411">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="1eb10-412">Streamování snižuje nároky na paměť nebo místo na disku při nahrávání souborů.</span><span class="sxs-lookup"><span data-stu-id="1eb10-412">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="1eb10-413">Streamování velkých souborů je zahrnuté v části [nahrávání velkých souborů pomocí streamování](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="1eb10-413">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="1eb10-414">Nahrávání malých souborů s vazbou modelu ve vyrovnávací paměti na fyzické úložiště</span><span class="sxs-lookup"><span data-stu-id="1eb10-414">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="1eb10-415">Pro nahrání malých souborů použijte formulář s více částmi nebo sestavte požadavek POST pomocí JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="1eb10-415">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="1eb10-416">Následující příklad ukazuje použití formuláře Razor Pages k nahrání jednoho souboru (*Pages/BufferedSingleFileUploadPhysical. cshtml* do ukázkové aplikace):</span><span class="sxs-lookup"><span data-stu-id="1eb10-416">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

<span data-ttu-id="1eb10-417">Následující příklad je podobný předchozímu příkladu s tím rozdílem, že:</span><span class="sxs-lookup"><span data-stu-id="1eb10-417">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="1eb10-418">K odeslání dat formuláře se používá JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)).</span><span class="sxs-lookup"><span data-stu-id="1eb10-418">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="1eb10-419">Neexistuje žádné ověření.</span><span class="sxs-lookup"><span data-stu-id="1eb10-419">There's no validation.</span></span>

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

<span data-ttu-id="1eb10-420">Chcete-li provést příspěvek formuláře v jazyce JavaScript pro klienty, kteří [nepodporují rozhraní API pro načítání](https://caniuse.com/#feat=fetch), použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="1eb10-420">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="1eb10-421">Použijte načtenou výplň (například [window. Fetch Fill (GitHub/Fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="1eb10-421">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="1eb10-422">Použijte `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="1eb10-422">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="1eb10-423">Příklad:</span><span class="sxs-lookup"><span data-stu-id="1eb10-423">For example:</span></span>

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

<span data-ttu-id="1eb10-424">Aby bylo možné podporovat nahrávání souborů, musí formuláře HTML určovat typ kódování (`enctype`) `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="1eb10-424">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="1eb10-425">V případě, že vstupní prvek `files` podporuje nahrávání více souborů, zadejte atribut `multiple` pro element `<input>`:</span><span class="sxs-lookup"><span data-stu-id="1eb10-425">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="1eb10-426">Jednotlivé soubory nahrané na server jsou k dispozici prostřednictvím [vazby modelu](xref:mvc/models/model-binding) pomocí <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="1eb10-426">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="1eb10-427">Ukázková aplikace ukazuje více ukládání souborů do vyrovnávací paměti pro scénáře databáze a fyzických úložišť.</span><span class="sxs-lookup"><span data-stu-id="1eb10-427">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="1eb10-428">Nepoužívejte **vlastnost** `FileName` <xref:Microsoft.AspNetCore.Http.IFormFile> jinou než pro zobrazení a protokolování.</span><span class="sxs-lookup"><span data-stu-id="1eb10-428">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="1eb10-429">Při zobrazení nebo protokolování je název souboru kódován HTML.</span><span class="sxs-lookup"><span data-stu-id="1eb10-429">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="1eb10-430">Útočník může poskytnout škodlivý název souboru, včetně úplných cest nebo relativních cest.</span><span class="sxs-lookup"><span data-stu-id="1eb10-430">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="1eb10-431">Aplikace by měly:</span><span class="sxs-lookup"><span data-stu-id="1eb10-431">Applications should:</span></span>
>
> * <span data-ttu-id="1eb10-432">Odeberte cestu z názvu souboru zadaného uživatelem.</span><span class="sxs-lookup"><span data-stu-id="1eb10-432">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="1eb10-433">Uložte název souboru s příponou PATH s kódováním HTML pro uživatelské rozhraní nebo protokolování.</span><span class="sxs-lookup"><span data-stu-id="1eb10-433">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="1eb10-434">Vygenerujte nový náhodný název souboru pro úložiště.</span><span class="sxs-lookup"><span data-stu-id="1eb10-434">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="1eb10-435">Následující kód odstraní cestu z názvu souboru:</span><span class="sxs-lookup"><span data-stu-id="1eb10-435">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="1eb10-436">Zde uvedené příklady neberou ohled na zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="1eb10-436">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="1eb10-437">Další informace jsou k dispozici v následujících částech a [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="1eb10-437">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="1eb10-438">Důležité informace o zabezpečení</span><span class="sxs-lookup"><span data-stu-id="1eb10-438">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="1eb10-439">Ověřování</span><span class="sxs-lookup"><span data-stu-id="1eb10-439">Validation</span></span>](#validation)

<span data-ttu-id="1eb10-440">Při nahrávání souborů pomocí vazeb modelů a <xref:Microsoft.AspNetCore.Http.IFormFile> může metoda Action přijmout:</span><span class="sxs-lookup"><span data-stu-id="1eb10-440">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="1eb10-441">Jeden <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="1eb10-441">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="1eb10-442">Kterákoli z následujících kolekcí, které reprezentují několik souborů:</span><span class="sxs-lookup"><span data-stu-id="1eb10-442">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="1eb10-443">[Seznam](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="1eb10-443">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="1eb10-444">Vazba odpovídá souborům formuláře podle názvu.</span><span class="sxs-lookup"><span data-stu-id="1eb10-444">Binding matches form files by name.</span></span> <span data-ttu-id="1eb10-445">Například hodnota HTML `name` v `<input type="file" name="formFile">` se musí shodovat s C# parametrem nebo vazbou vlastnosti (`FormFile`).</span><span class="sxs-lookup"><span data-stu-id="1eb10-445">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="1eb10-446">Další informace naleznete v části [název atributu matched na název parametru metody post](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="1eb10-446">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="1eb10-447">Následující příklad:</span><span class="sxs-lookup"><span data-stu-id="1eb10-447">The following example:</span></span>

* <span data-ttu-id="1eb10-448">Projde jedním nebo více nahranými soubory.</span><span class="sxs-lookup"><span data-stu-id="1eb10-448">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="1eb10-449">Pomocí [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) vrátí úplnou cestu k souboru, včetně názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="1eb10-449">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="1eb10-450">Uloží soubory do místního systému souborů pomocí názvu souboru generovaného aplikací.</span><span class="sxs-lookup"><span data-stu-id="1eb10-450">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="1eb10-451">Vrátí celkový počet nahraných souborů a jejich velikost.</span><span class="sxs-lookup"><span data-stu-id="1eb10-451">Returns the total number and size of files uploaded.</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size, filePath });
}
```

<span data-ttu-id="1eb10-452">K vygenerování názvu souboru bez cesty použijte `Path.GetRandomFileName`.</span><span class="sxs-lookup"><span data-stu-id="1eb10-452">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="1eb10-453">V následujícím příkladu je cesta získána z konfigurace:</span><span class="sxs-lookup"><span data-stu-id="1eb10-453">In the following example, the path is obtained from configuration:</span></span>

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

<span data-ttu-id="1eb10-454">Cesta předaná <xref:System.IO.FileStream> *musí* zahrnovat název souboru.</span><span class="sxs-lookup"><span data-stu-id="1eb10-454">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="1eb10-455">Pokud není zadán název souboru, je při spuštění vyvolána hodnota <xref:System.UnauthorizedAccessException>.</span><span class="sxs-lookup"><span data-stu-id="1eb10-455">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="1eb10-456">Soubory odeslané pomocí techniky <xref:Microsoft.AspNetCore.Http.IFormFile> jsou uloženy do vyrovnávací paměti nebo na disku na serveru před zpracováním.</span><span class="sxs-lookup"><span data-stu-id="1eb10-456">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="1eb10-457">V rámci metody Action je obsah <xref:Microsoft.AspNetCore.Http.IFormFile> přístupný jako <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="1eb10-457">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="1eb10-458">Kromě místního systému souborů je možné soubory ukládat do síťové sdílené složky nebo do služby úložiště souborů, jako je [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="1eb10-458">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="1eb10-459">Další příklad, který projde několik souborů pro nahrání a používá bezpečné názvy souborů, najdete v ukázkové aplikaci v části *Pages/BufferedMultipleFileUploadPhysical. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="1eb10-459">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="1eb10-460">[Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) vyvolá výjimku <xref:System.IO.IOException>, pokud jsou vytvořeny více než 65 535 souborů bez odstranění předchozích dočasných souborů.</span><span class="sxs-lookup"><span data-stu-id="1eb10-460">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="1eb10-461">Limit 65 535 souborů je omezen na server.</span><span class="sxs-lookup"><span data-stu-id="1eb10-461">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="1eb10-462">Další informace o tomto limitu pro operační systém Windows najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="1eb10-462">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="1eb10-463">GetTempFileNameA – funkce</span><span class="sxs-lookup"><span data-stu-id="1eb10-463">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="1eb10-464">Nahrávání malých souborů s vazbou modelu s vyrovnávací pamětí do databáze</span><span class="sxs-lookup"><span data-stu-id="1eb10-464">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="1eb10-465">Chcete-li uložit data binárního souboru do databáze pomocí [Entity Framework](/ef/core/index), definujte v entitě vlastnost pole <xref:System.Byte>:</span><span class="sxs-lookup"><span data-stu-id="1eb10-465">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="1eb10-466">Zadejte vlastnost modelu stránky pro třídu, která obsahuje <xref:Microsoft.AspNetCore.Http.IFormFile>:</span><span class="sxs-lookup"><span data-stu-id="1eb10-466">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="1eb10-467"><xref:Microsoft.AspNetCore.Http.IFormFile> lze použít přímo jako parametr metody akce nebo jako vlastnost vázaného modelu.</span><span class="sxs-lookup"><span data-stu-id="1eb10-467"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="1eb10-468">Předchozí příklad používá vlastnost vázaného modelu.</span><span class="sxs-lookup"><span data-stu-id="1eb10-468">The prior example uses a bound model property.</span></span>

<span data-ttu-id="1eb10-469">Ve formuláři Razor Pages se používá `FileUpload`:</span><span class="sxs-lookup"><span data-stu-id="1eb10-469">The `FileUpload` is used in the Razor Pages form:</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

<span data-ttu-id="1eb10-470">Když je formulář publikovaný na serveru, zkopírujte <xref:Microsoft.AspNetCore.Http.IFormFile> do datového proudu a uložte ho jako pole bajtů v databázi.</span><span class="sxs-lookup"><span data-stu-id="1eb10-470">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="1eb10-471">V následujícím příkladu `_dbContext` ukládá kontext databáze aplikace:</span><span class="sxs-lookup"><span data-stu-id="1eb10-471">In the following example, `_dbContext` stores the app's database context:</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

<span data-ttu-id="1eb10-472">Předchozí příklad je podobný scénáři, který je znázorněný v ukázkové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="1eb10-472">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="1eb10-473">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="1eb10-473">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="1eb10-474">*Pages/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="1eb10-474">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="1eb10-475">Při ukládání binárních dat do relačních databází buďte opatrní, protože to může mít nepříznivý vliv na výkon.</span><span class="sxs-lookup"><span data-stu-id="1eb10-475">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="1eb10-476">Nespoléhá se ani na nedůvěryhodnou vlastnost `FileName` <xref:Microsoft.AspNetCore.Http.IFormFile> bez ověření.</span><span class="sxs-lookup"><span data-stu-id="1eb10-476">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="1eb10-477">Vlastnost `FileName` by měla být použita pouze pro účely zobrazení a pouze po kódování HTML.</span><span class="sxs-lookup"><span data-stu-id="1eb10-477">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="1eb10-478">Uvedené příklady nevezmou ohled na zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="1eb10-478">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="1eb10-479">Další informace jsou k dispozici v následujících částech a [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="1eb10-479">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="1eb10-480">Důležité informace o zabezpečení</span><span class="sxs-lookup"><span data-stu-id="1eb10-480">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="1eb10-481">Ověřování</span><span class="sxs-lookup"><span data-stu-id="1eb10-481">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="1eb10-482">Nahrávání velkých souborů pomocí streamování</span><span class="sxs-lookup"><span data-stu-id="1eb10-482">Upload large files with streaming</span></span>

<span data-ttu-id="1eb10-483">Následující příklad ukazuje, jak použít JavaScript ke streamování souboru do akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="1eb10-483">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="1eb10-484">Token proti padělání souboru se generuje pomocí vlastního atributu filtru a předává se do hlaviček protokolu HTTP klienta místo v textu žádosti.</span><span class="sxs-lookup"><span data-stu-id="1eb10-484">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="1eb10-485">Vzhledem k tomu, že metoda akce zpracovává nahraná data přímo, vazba modelu formuláře je zakázána jiným vlastním filtrem.</span><span class="sxs-lookup"><span data-stu-id="1eb10-485">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="1eb10-486">V rámci akce je obsah formuláře čten pomocí `MultipartReader`, který čte každou jednotlivou `MultipartSection`, zpracovává soubor nebo ukládá obsah podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="1eb10-486">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="1eb10-487">Po načtení oddílů s více částmi provede akce vlastní vazbu modelu.</span><span class="sxs-lookup"><span data-stu-id="1eb10-487">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="1eb10-488">Počáteční odpověď stránky načte formulář a uloží token proti padělání do souboru cookie (prostřednictvím atributu `GenerateAntiforgeryTokenCookieAttribute`).</span><span class="sxs-lookup"><span data-stu-id="1eb10-488">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="1eb10-489">Atribut používá ASP.NET Core integrovanou [podporu proti padělání](xref:security/anti-request-forgery) pro nastavení souboru cookie s tokenem žádosti:</span><span class="sxs-lookup"><span data-stu-id="1eb10-489">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="1eb10-490">`DisableFormValueModelBindingAttribute` slouží k zakázání vazby modelu:</span><span class="sxs-lookup"><span data-stu-id="1eb10-490">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="1eb10-491">V ukázkové aplikaci `GenerateAntiforgeryTokenCookieAttribute` a `DisableFormValueModelBindingAttribute` se aplikují jako filtry pro modely stránky aplikace `/StreamedSingleFileUploadDb` a `/StreamedSingleFileUploadPhysical` v `Startup.ConfigureServices` pomocí [konvencí Razor Pages](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="1eb10-491">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="1eb10-492">Vzhledem k tomu, že vazba modelu nepřečte formulář, parametry, které jsou svázané z formuláře, se nezobrazují (budou pokračovat v práci s dotazem, trasou a hlavičkou).</span><span class="sxs-lookup"><span data-stu-id="1eb10-492">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="1eb10-493">Metoda Action funguje přímo s vlastností `Request`.</span><span class="sxs-lookup"><span data-stu-id="1eb10-493">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="1eb10-494">Pro čtení jednotlivých oddílů se používá `MultipartReader`.</span><span class="sxs-lookup"><span data-stu-id="1eb10-494">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="1eb10-495">Data klíč/hodnota jsou uložena v `KeyValueAccumulator`.</span><span class="sxs-lookup"><span data-stu-id="1eb10-495">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="1eb10-496">Po načtení oddílů s více částmi se obsah `KeyValueAccumulator` použije pro svázání dat formuláře s typem modelu.</span><span class="sxs-lookup"><span data-stu-id="1eb10-496">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="1eb10-497">Kompletní metoda `StreamingController.UploadDatabase` pro streamování do databáze s EF Core:</span><span class="sxs-lookup"><span data-stu-id="1eb10-497">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="1eb10-498">`MultipartRequestHelper` (*nástroje/MultipartRequestHelper. cs*):</span><span class="sxs-lookup"><span data-stu-id="1eb10-498">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="1eb10-499">Úplná metoda `StreamingController.UploadPhysical` pro streamování do fyzického umístění:</span><span class="sxs-lookup"><span data-stu-id="1eb10-499">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="1eb10-500">V ukázkové aplikaci jsou kontroly ověřování zpracovávány `FileHelpers.ProcessStreamedFile`.</span><span class="sxs-lookup"><span data-stu-id="1eb10-500">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="1eb10-501">Ověřování</span><span class="sxs-lookup"><span data-stu-id="1eb10-501">Validation</span></span>

<span data-ttu-id="1eb10-502">Třída `FileHelpers` ukázkové aplikace ukazuje několik kontrol uložených do vyrovnávací paměti <xref:Microsoft.AspNetCore.Http.IFormFile> a streamovaná nahrávání souborů.</span><span class="sxs-lookup"><span data-stu-id="1eb10-502">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="1eb10-503">Pro zpracování <xref:Microsoft.AspNetCore.Http.IFormFile> ukládání souborů do vyrovnávací paměti v ukázkové aplikaci si přečtěte část `ProcessFormFile` v souboru *. cs nástrojů Utilities/App.*</span><span class="sxs-lookup"><span data-stu-id="1eb10-503">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="1eb10-504">Pro zpracování streamované soubory si Projděte metodu `ProcessStreamedFile` ve stejném souboru.</span><span class="sxs-lookup"><span data-stu-id="1eb10-504">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="1eb10-505">Metody zpracování ověřování, které jsou znázorněné v ukázkové aplikaci, nekontrolují obsah nahraných souborů.</span><span class="sxs-lookup"><span data-stu-id="1eb10-505">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="1eb10-506">Ve většině produkčních scénářů se v souboru používá rozhraní API pro skenování virů nebo malwaru, než je soubor dostupný uživatelům nebo jiným systémům.</span><span class="sxs-lookup"><span data-stu-id="1eb10-506">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="1eb10-507">I když ukázka tématu poskytuje pracovní příklad technik ověřování, Neimplementujte třídu `FileHelpers` v produkční aplikaci, pokud:</span><span class="sxs-lookup"><span data-stu-id="1eb10-507">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="1eb10-508">Plně rozumíte implementaci.</span><span class="sxs-lookup"><span data-stu-id="1eb10-508">Fully understand the implementation.</span></span>
> * <span data-ttu-id="1eb10-509">Upravte implementaci podle potřeby pro prostředí a specifikace aplikace.</span><span class="sxs-lookup"><span data-stu-id="1eb10-509">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="1eb10-510">**Nikdy nepoužívejte nerozlišený kód zabezpečení v aplikaci bez nutnosti řešit tyto požadavky.**</span><span class="sxs-lookup"><span data-stu-id="1eb10-510">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="1eb10-511">Ověření obsahu</span><span class="sxs-lookup"><span data-stu-id="1eb10-511">Content validation</span></span>

<span data-ttu-id="1eb10-512">**Pro nahraný obsah použijte rozhraní API pro kontrolu virů a malwaru třetí strany.**</span><span class="sxs-lookup"><span data-stu-id="1eb10-512">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="1eb10-513">Prohledávání souborů je náročné na prostředky serveru ve scénářích s vysokým objemem.</span><span class="sxs-lookup"><span data-stu-id="1eb10-513">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="1eb10-514">Pokud dojde ke snížení výkonu zpracování požadavků z důvodu kontroly souborů, zvažte přesměrování práce skenování na službu na [pozadí](xref:fundamentals/host/hosted-services), případně služby spuštěné na serveru, který se liší od serveru aplikace.</span><span class="sxs-lookup"><span data-stu-id="1eb10-514">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="1eb10-515">Nahrané soubory se obvykle uchovávají v oblasti v karanténě, dokud je kontrola virů na pozadí nevrátí.</span><span class="sxs-lookup"><span data-stu-id="1eb10-515">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="1eb10-516">Když soubor projde, soubor se přesune do normálního umístění úložiště souborů.</span><span class="sxs-lookup"><span data-stu-id="1eb10-516">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="1eb10-517">Tyto kroky se obvykle provádí ve spojení s databázovým záznamem, který indikuje stav kontroly souboru.</span><span class="sxs-lookup"><span data-stu-id="1eb10-517">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="1eb10-518">Při použití takového přístupu zůstane aplikace a Server App zaměřené na reakci na požadavky.</span><span class="sxs-lookup"><span data-stu-id="1eb10-518">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="1eb10-519">Ověření přípony souboru</span><span class="sxs-lookup"><span data-stu-id="1eb10-519">File extension validation</span></span>

<span data-ttu-id="1eb10-520">Přípona nahraného souboru by měla být zaškrtnutá na seznamu povolených rozšíření.</span><span class="sxs-lookup"><span data-stu-id="1eb10-520">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="1eb10-521">Příklad:</span><span class="sxs-lookup"><span data-stu-id="1eb10-521">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="1eb10-522">Ověření podpisu souboru</span><span class="sxs-lookup"><span data-stu-id="1eb10-522">File signature validation</span></span>

<span data-ttu-id="1eb10-523">Podpis souboru se určuje na prvních několika bajtech na začátku souboru.</span><span class="sxs-lookup"><span data-stu-id="1eb10-523">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="1eb10-524">Tyto bajty lze použít k určení, zda přípona odpovídá obsahu souboru.</span><span class="sxs-lookup"><span data-stu-id="1eb10-524">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="1eb10-525">Ukázková aplikace zkontroluje podpisy souborů pro několik běžných typů souborů.</span><span class="sxs-lookup"><span data-stu-id="1eb10-525">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="1eb10-526">V následujícím příkladu se pro tento soubor kontroluje podpis souboru obrázku JPEG:</span><span class="sxs-lookup"><span data-stu-id="1eb10-526">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

<span data-ttu-id="1eb10-527">Další signatury souborů získáte v dokumentaci [signatury souborů](https://www.filesignatures.net/) a oficiálních souborů.</span><span class="sxs-lookup"><span data-stu-id="1eb10-527">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="1eb10-528">Zabezpečení názvu souboru</span><span class="sxs-lookup"><span data-stu-id="1eb10-528">File name security</span></span>

<span data-ttu-id="1eb10-529">Nikdy nepoužívejte název souboru dodaný klientem pro uložení souboru do fyzického úložiště.</span><span class="sxs-lookup"><span data-stu-id="1eb10-529">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="1eb10-530">Vytvořte bezpečný název souboru pro soubor pomocí [cesty. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) nebo [cesty. GetTempFileName](xref:System.IO.Path.GetTempFileName*) a vytvořte úplnou cestu (včetně názvu souboru) pro dočasné úložiště.</span><span class="sxs-lookup"><span data-stu-id="1eb10-530">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="1eb10-531">Razor automaticky kóduje hodnoty vlastností pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="1eb10-531">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="1eb10-532">Následující kód je bezpečné použít:</span><span class="sxs-lookup"><span data-stu-id="1eb10-532">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="1eb10-533">Mimo Razor, vždy <xref:System.Net.WebUtility.HtmlEncode*> obsah názvu souboru z požadavku uživatele.</span><span class="sxs-lookup"><span data-stu-id="1eb10-533">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="1eb10-534">Mnoho implementací musí zahrnovat kontrolu, že soubor existuje. v opačném případě je soubor přepsán souborem se stejným názvem.</span><span class="sxs-lookup"><span data-stu-id="1eb10-534">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="1eb10-535">Poskytněte další logiku pro splnění specifikací vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="1eb10-535">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="1eb10-536">Ověřování velikosti</span><span class="sxs-lookup"><span data-stu-id="1eb10-536">Size validation</span></span>

<span data-ttu-id="1eb10-537">Omezte velikost nahraných souborů.</span><span class="sxs-lookup"><span data-stu-id="1eb10-537">Limit the size of uploaded files.</span></span>

<span data-ttu-id="1eb10-538">V ukázkové aplikaci je velikost souboru omezená na 2 MB (uvedené v bajtech).</span><span class="sxs-lookup"><span data-stu-id="1eb10-538">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="1eb10-539">Limit je zadán prostřednictvím [Konfigurace](xref:fundamentals/configuration/index) ze souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="1eb10-539">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="1eb10-540">`FileSizeLimit` je vložen do tříd `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="1eb10-540">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

<span data-ttu-id="1eb10-541">Když velikost souboru překročí limit, soubor se odmítne:</span><span class="sxs-lookup"><span data-stu-id="1eb10-541">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="1eb10-542">Porovnává hodnotu atributu name s parametrem název metody POST</span><span class="sxs-lookup"><span data-stu-id="1eb10-542">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="1eb10-543">V nestandardních formulářích, které PUBLIKují data formuláře nebo přímo používají `FormData` JavaScriptu, musí název zadaný v prvku formuláře nebo `FormData` odpovídat názvu parametru v akci kontroleru.</span><span class="sxs-lookup"><span data-stu-id="1eb10-543">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="1eb10-544">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="1eb10-544">In the following example:</span></span>

* <span data-ttu-id="1eb10-545">Při použití elementu `<input>` je atribut `name` nastaven na hodnotu `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="1eb10-545">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="1eb10-546">Při použití `FormData` v jazyce JavaScript je název nastaven na hodnotu `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="1eb10-546">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="1eb10-547">Pro parametr C# metody použijte stejný název (`battlePlans`):</span><span class="sxs-lookup"><span data-stu-id="1eb10-547">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="1eb10-548">Pro Razor Pages metodu obslužné rutiny stránky s názvem `Upload`:</span><span class="sxs-lookup"><span data-stu-id="1eb10-548">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="1eb10-549">Pro metodu akce po kontroléru MVC:</span><span class="sxs-lookup"><span data-stu-id="1eb10-549">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="1eb10-550">Konfigurace serveru a aplikace</span><span class="sxs-lookup"><span data-stu-id="1eb10-550">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="1eb10-551">Omezení délky těla částí</span><span class="sxs-lookup"><span data-stu-id="1eb10-551">Multipart body length limit</span></span>

<span data-ttu-id="1eb10-552"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> nastaví limit délky jednotlivých částí části.</span><span class="sxs-lookup"><span data-stu-id="1eb10-552"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="1eb10-553">Oddíly formuláře, které překračují toto omezení, vyvolávají při analýze <xref:System.IO.InvalidDataException>.</span><span class="sxs-lookup"><span data-stu-id="1eb10-553">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="1eb10-554">Výchozí hodnota je 134 217 728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="1eb10-554">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="1eb10-555">Upravte limit pomocí nastavení <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="1eb10-555">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<span data-ttu-id="1eb10-556"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> slouží k nastavení <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> pro jednu stránku nebo akci.</span><span class="sxs-lookup"><span data-stu-id="1eb10-556"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="1eb10-557">V aplikaci Razor Pages použijte filtr s [konvencí](xref:razor-pages/razor-pages-conventions) v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="1eb10-557">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model.Filters.Add(
                    new RequestFormLimitsAttribute()
                    {
                        // Set the limit to 256 MB
                        MultipartBodyLengthLimit = 268435456
                    });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="1eb10-558">V aplikaci Razor Pages nebo aplikaci MVC použijte filtr na model stránky nebo metodu akce:</span><span class="sxs-lookup"><span data-stu-id="1eb10-558">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="1eb10-559">Kestrel maximální velikost textu požadavku</span><span class="sxs-lookup"><span data-stu-id="1eb10-559">Kestrel maximum request body size</span></span>

<span data-ttu-id="1eb10-560">Pro aplikace hostované v Kestrel je výchozí maximální velikost textu požadavku 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="1eb10-560">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="1eb10-561">Přizpůsobte limit pomocí možnosti serveru [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:</span><span class="sxs-lookup"><span data-stu-id="1eb10-561">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            // Handle requests up to 50 MB
            options.Limits.MaxRequestBodySize = 52428800;
        });
```

<span data-ttu-id="1eb10-562"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> slouží k nastavení [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) pro jednu stránku nebo akci.</span><span class="sxs-lookup"><span data-stu-id="1eb10-562"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="1eb10-563">V aplikaci Razor Pages použijte filtr s [konvencí](xref:razor-pages/razor-pages-conventions) v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="1eb10-563">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model =>
                {
                    // Handle requests up to 50 MB
                    model.Filters.Add(
                        new RequestSizeLimitAttribute(52428800));
                });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="1eb10-564">V aplikaci se stránkami Razor nebo v aplikaci MVC použijte filtr na třídu obslužné rutiny stránky nebo na metodu akce:</span><span class="sxs-lookup"><span data-stu-id="1eb10-564">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="1eb10-565">Další omezení Kestrel</span><span class="sxs-lookup"><span data-stu-id="1eb10-565">Other Kestrel limits</span></span>

<span data-ttu-id="1eb10-566">Pro aplikace hostované v Kestrel se můžou vztahovat další omezení Kestrel:</span><span class="sxs-lookup"><span data-stu-id="1eb10-566">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="1eb10-567">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="1eb10-567">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="1eb10-568">Sazby za data požadavků a odpovědí</span><span class="sxs-lookup"><span data-stu-id="1eb10-568">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="1eb10-569">Omezení délky obsahu služby IIS</span><span class="sxs-lookup"><span data-stu-id="1eb10-569">IIS content length limit</span></span>

<span data-ttu-id="1eb10-570">Výchozí limit počtu požadavků (`maxAllowedContentLength`) je 30 000 000 bajtů, což je přibližně 28.6 MB.</span><span class="sxs-lookup"><span data-stu-id="1eb10-570">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="1eb10-571">Upravte limit v souboru *Web. config* :</span><span class="sxs-lookup"><span data-stu-id="1eb10-571">Customize the limit in the *web.config* file:</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- Handle requests up to 50 MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="1eb10-572">Toto nastavení platí pouze pro službu IIS.</span><span class="sxs-lookup"><span data-stu-id="1eb10-572">This setting only applies to IIS.</span></span> <span data-ttu-id="1eb10-573">K tomuto chování nedochází ve výchozím nastavení při hostování v Kestrel.</span><span class="sxs-lookup"><span data-stu-id="1eb10-573">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="1eb10-574">Další informace najdete v tématu [omezení požadavků \<requestLimits >](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="1eb10-574">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="1eb10-575">Omezení v modulu ASP.NET Core nebo přítomnosti modulu filtrování požadavků služby IIS mohou omezit nahrávání na 2 nebo 4 GB.</span><span class="sxs-lookup"><span data-stu-id="1eb10-575">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="1eb10-576">Další informace najdete v tématu [nelze odeslat soubor o velikosti větší než 2 GB (ASPNET/AspNetCore #2711)](https://github.com/aspnet/AspNetCore/issues/2711).</span><span class="sxs-lookup"><span data-stu-id="1eb10-576">For more information, see [Unable to upload file greater than 2GB in size (aspnet/AspNetCore #2711)](https://github.com/aspnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="1eb10-577">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="1eb10-577">Troubleshoot</span></span>

<span data-ttu-id="1eb10-578">Níže jsou uvedeny některé běžné problémy, které se vyskytly při práci s nahráváním souborů a jejich možnými řešeními.</span><span class="sxs-lookup"><span data-stu-id="1eb10-578">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="1eb10-579">Při nasazení na server služby IIS se nenašla chyba.</span><span class="sxs-lookup"><span data-stu-id="1eb10-579">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="1eb10-580">Následující chyba znamená, že nahraný soubor překračuje délku nakonfigurovaného obsahu serveru:</span><span class="sxs-lookup"><span data-stu-id="1eb10-580">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="1eb10-581">Další informace o zvýšení limitu najdete v části [omezení délky obsahu služby IIS](#iis-content-length-limit) .</span><span class="sxs-lookup"><span data-stu-id="1eb10-581">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="1eb10-582">Chyba připojení</span><span class="sxs-lookup"><span data-stu-id="1eb10-582">Connection failure</span></span>

<span data-ttu-id="1eb10-583">Chyba připojení a připojení k serveru pro resetování pravděpodobně znamená, že nahraný soubor překračuje maximální velikost textu požadavku Kestrel.</span><span class="sxs-lookup"><span data-stu-id="1eb10-583">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="1eb10-584">Další informace najdete v části [Kestrel maximální velikost textu požadavku](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="1eb10-584">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="1eb10-585">Omezení připojení klientů Kestrel mohou také vyžadovat úpravu.</span><span class="sxs-lookup"><span data-stu-id="1eb10-585">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="1eb10-586">Výjimka odkazu s hodnotou null s IFormFile</span><span class="sxs-lookup"><span data-stu-id="1eb10-586">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="1eb10-587">Pokud kontroler přijímá odeslané soubory pomocí <xref:Microsoft.AspNetCore.Http.IFormFile>, ale hodnota je `null`, potvrďte, že formulář HTML určuje hodnotu `enctype` `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="1eb10-587">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="1eb10-588">Pokud tento atribut není nastaven u elementu `<form>`, odeslání souboru neproběhne a všechny vázané argumenty <xref:Microsoft.AspNetCore.Http.IFormFile> jsou `null`.</span><span class="sxs-lookup"><span data-stu-id="1eb10-588">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="1eb10-589">Ujistěte se také, že [nahrávání názvů v datech formuláře odpovídá pojmenování aplikace](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="1eb10-589">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="1eb10-590">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="1eb10-590">Additional resources</span></span>

* [<span data-ttu-id="1eb10-591">Neomezená nahrávání souboru</span><span class="sxs-lookup"><span data-stu-id="1eb10-591">Unrestricted File Upload</span></span>](https://www.owasp.org/index.php/Unrestricted_File_Upload)
* [<span data-ttu-id="1eb10-592">Zabezpečení Azure: rámec zabezpečení: ověření vstupu | Hrozeb</span><span class="sxs-lookup"><span data-stu-id="1eb10-592">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="1eb10-593">Vzory návrhu cloudu Azure: vzor osobního Key</span><span class="sxs-lookup"><span data-stu-id="1eb10-593">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
