---
title: Nahrání souborů v ASP.NET Core
author: guardrex
description: Jak používat vazbu modelu a streamování k nahrávání souborů v ASP.NET Core MVC
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/04/2019
uid: mvc/models/file-uploads
ms.openlocfilehash: b57ad4fe62de38085c11d7026d278cc6e0c565ce
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963161"
---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="9a941-103">Nahrání souborů v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9a941-103">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="9a941-104">Od [Luke Latham](https://github.com/guardrex), [Steve Smith](https://ardalis.com/)a [Rutger](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="9a941-104">By [Luke Latham](https://github.com/guardrex), [Steve Smith](https://ardalis.com/), and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9a941-105">ASP.NET Core podporuje nahrávání jednoho nebo více souborů pomocí vazby modelu ve vyrovnávací paměti pro menší soubory a streamování bez vyrovnávací paměti pro větší soubory.</span><span class="sxs-lookup"><span data-stu-id="9a941-105">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="9a941-106">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9a941-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="9a941-107">Důležité informace o zabezpečení</span><span class="sxs-lookup"><span data-stu-id="9a941-107">Security considerations</span></span>

<span data-ttu-id="9a941-108">Pokud chcete uživatelům poskytnout možnost nahrávat soubory na server, buďte opatrní.</span><span class="sxs-lookup"><span data-stu-id="9a941-108">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="9a941-109">Útočníci se můžou pokusit:</span><span class="sxs-lookup"><span data-stu-id="9a941-109">Attackers may attempt to:</span></span>

* <span data-ttu-id="9a941-110">Vykoná útok [DOS (Denial of Service](/windows-hardware/drivers/ifs/denial-of-service) ).</span><span class="sxs-lookup"><span data-stu-id="9a941-110">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="9a941-111">Nahrání virů nebo malwaru</span><span class="sxs-lookup"><span data-stu-id="9a941-111">Upload viruses or malware.</span></span>
* <span data-ttu-id="9a941-112">Narušit sítě a servery jinými způsoby.</span><span class="sxs-lookup"><span data-stu-id="9a941-112">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="9a941-113">Bezpečnostní kroky, které snižují pravděpodobnost úspěšného útoku, jsou:</span><span class="sxs-lookup"><span data-stu-id="9a941-113">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="9a941-114">Nahrajte soubory do vyhrazené oblasti pro nahrávání souborů, nejlépe do nesystémové jednotky.</span><span class="sxs-lookup"><span data-stu-id="9a941-114">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="9a941-115">Vyhrazené umístění usnadňuje omezení zabezpečení pro nahrané soubory.</span><span class="sxs-lookup"><span data-stu-id="9a941-115">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="9a941-116">Zakažte oprávnění EXECUTE pro umístění pro nahrání souboru.&dagger;</span><span class="sxs-lookup"><span data-stu-id="9a941-116">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="9a941-117">Neuchovávat nahrané soubory ve stejném adresářovém stromu jako aplikace.&dagger;</span><span class="sxs-lookup"><span data-stu-id="9a941-117">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="9a941-118">Použijte název bezpečného souboru určený aplikací.</span><span class="sxs-lookup"><span data-stu-id="9a941-118">Use a safe file name determined by the app.</span></span> <span data-ttu-id="9a941-119">Nepoužívejte název souboru poskytnutý uživatelem nebo nedůvěryhodného názvu nahraného souboru.&dagger; při zobrazení kódování HTML kódovat název nedůvěryhodného souboru.</span><span class="sxs-lookup"><span data-stu-id="9a941-119">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="9a941-120">Například protokolování názvu souboru nebo zobrazení v uživatelském rozhraní (Razor automaticky kóduje výstup HTML).</span><span class="sxs-lookup"><span data-stu-id="9a941-120">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="9a941-121">Povolte pro specifikaci návrhu aplikace jenom schválené přípony souborů.&dagger;</span><span class="sxs-lookup"><span data-stu-id="9a941-121">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="9a941-122">Ověřte, zda jsou na serveru provedeny kontroly na straně klienta.&dagger; kontroly na straně klienta se snadno obejít.</span><span class="sxs-lookup"><span data-stu-id="9a941-122">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="9a941-123">Ověřte velikost nahraného souboru.</span><span class="sxs-lookup"><span data-stu-id="9a941-123">Check the size of an uploaded file.</span></span> <span data-ttu-id="9a941-124">Nastavte limit maximální velikosti, aby se zabránilo velkým nahrávání.&dagger;</span><span class="sxs-lookup"><span data-stu-id="9a941-124">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="9a941-125">Pokud by soubory neměly být přepsány nahraným souborem se stejným názvem, před nahráním souboru ověřte název souboru proti databázi nebo fyzickému úložišti.</span><span class="sxs-lookup"><span data-stu-id="9a941-125">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="9a941-126">**Před uložením souboru spusťte v nahraném obsahu skener virů nebo malwaru.**</span><span class="sxs-lookup"><span data-stu-id="9a941-126">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="9a941-127">&dagger;ukázková aplikace ukazuje přístup, který splňuje kritéria.</span><span class="sxs-lookup"><span data-stu-id="9a941-127">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="9a941-128">Nahrávání škodlivého kódu do systému je často prvním krokem ke spuštění kódu, který může:</span><span class="sxs-lookup"><span data-stu-id="9a941-128">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="9a941-129">Zcela získá kontrolu nad systémem.</span><span class="sxs-lookup"><span data-stu-id="9a941-129">Completely gain control of a system.</span></span>
> * <span data-ttu-id="9a941-130">Přetížit systém s výsledkem, že dojde k chybě systému.</span><span class="sxs-lookup"><span data-stu-id="9a941-130">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="9a941-131">Napadnout data uživatelů nebo systémových dat.</span><span class="sxs-lookup"><span data-stu-id="9a941-131">Compromise user or system data.</span></span>
> * <span data-ttu-id="9a941-132">Použijte graffiti pro veřejné uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="9a941-132">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="9a941-133">Informace o omezení oblasti útoku při přijímání souborů uživateli najdete v následujících zdrojích informací:</span><span class="sxs-lookup"><span data-stu-id="9a941-133">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="9a941-134">Neomezená nahrávání souboru</span><span class="sxs-lookup"><span data-stu-id="9a941-134">Unrestricted File Upload</span></span>](https://www.owasp.org/index.php/Unrestricted_File_Upload)
> * [<span data-ttu-id="9a941-135">Zabezpečení Azure: Ujistěte se, že jsou při přijímání souborů od uživatelů k dismístě správné ovládací prvky.</span><span class="sxs-lookup"><span data-stu-id="9a941-135">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="9a941-136">Další informace o implementaci bezpečnostních opatření, včetně příkladů z ukázkové aplikace, najdete v části [ověření](#validation) .</span><span class="sxs-lookup"><span data-stu-id="9a941-136">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="9a941-137">Scénáře úložiště</span><span class="sxs-lookup"><span data-stu-id="9a941-137">Storage scenarios</span></span>

<span data-ttu-id="9a941-138">Mezi běžné možnosti úložiště pro soubory patří:</span><span class="sxs-lookup"><span data-stu-id="9a941-138">Common storage options for files include:</span></span>

* <span data-ttu-id="9a941-139">Databáze</span><span class="sxs-lookup"><span data-stu-id="9a941-139">Database</span></span>

  * <span data-ttu-id="9a941-140">U malých nahrávání souborů je databáze často rychlejší než možnosti fyzického úložiště (systému souborů nebo síťového sdílení).</span><span class="sxs-lookup"><span data-stu-id="9a941-140">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="9a941-141">Databáze je často pohodlnější než možnosti fyzického úložiště, protože načtení záznamu databáze pro uživatelská data může současně poskytovat obsah souboru (například obrázek miniatury).</span><span class="sxs-lookup"><span data-stu-id="9a941-141">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="9a941-142">Databáze je potenciálně levnější než použití služby úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="9a941-142">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="9a941-143">Fyzické úložiště (systém souborů nebo síťová sdílená složka)</span><span class="sxs-lookup"><span data-stu-id="9a941-143">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="9a941-144">Pro nahrávání velkých souborů:</span><span class="sxs-lookup"><span data-stu-id="9a941-144">For large file uploads:</span></span>
    * <span data-ttu-id="9a941-145">Omezení databáze mohou omezit velikost nahrávání.</span><span class="sxs-lookup"><span data-stu-id="9a941-145">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="9a941-146">Fyzické úložiště je často méně hospodárné než úložiště v databázi.</span><span class="sxs-lookup"><span data-stu-id="9a941-146">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="9a941-147">Fyzické úložiště je potenciálně levnější než použití služby úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="9a941-147">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="9a941-148">Proces aplikace musí mít oprávnění ke čtení a zápisu do umístění úložiště.</span><span class="sxs-lookup"><span data-stu-id="9a941-148">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="9a941-149">**Nikdy neudělujte oprávnění EXECUTE.**</span><span class="sxs-lookup"><span data-stu-id="9a941-149">**Never grant execute permission.**</span></span>

* <span data-ttu-id="9a941-150">Služba úložiště dat (například [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="9a941-150">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="9a941-151">Služby obvykle nabízejí vylepšenou škálovatelnost a odolnost proti místním řešením, které obvykle podléhají jednomu bodu selhání.</span><span class="sxs-lookup"><span data-stu-id="9a941-151">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="9a941-152">Služby jsou potenciálně nižší náklady ve scénářích infrastruktury velkých úložišť.</span><span class="sxs-lookup"><span data-stu-id="9a941-152">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="9a941-153">Další informace najdete v tématu [rychlý Start: použití .NET k vytvoření objektu BLOB v úložišti objektů](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="9a941-153">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="9a941-154">Téma ukazuje <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, ale <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> lze použít k uložení <xref:System.IO.FileStream> do úložiště objektů BLOB při práci s <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="9a941-154">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="9a941-155">Scénáře nahrávání souborů</span><span class="sxs-lookup"><span data-stu-id="9a941-155">File upload scenarios</span></span>

<span data-ttu-id="9a941-156">Dva obecné přístupy k nahrávání souborů jsou ukládání do vyrovnávací paměti a streamování.</span><span class="sxs-lookup"><span data-stu-id="9a941-156">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="9a941-157">**Do vyrovnávací paměti**</span><span class="sxs-lookup"><span data-stu-id="9a941-157">**Buffering**</span></span>

<span data-ttu-id="9a941-158">Celý soubor je čten do <xref:Microsoft.AspNetCore.Http.IFormFile>, což je C# reprezentace souboru používaného ke zpracování nebo uložení souboru.</span><span class="sxs-lookup"><span data-stu-id="9a941-158">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="9a941-159">Prostředky (disk, paměť) používané při nahrávání souborů závisí na počtu a velikosti souběžných nahrávání souborů.</span><span class="sxs-lookup"><span data-stu-id="9a941-159">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="9a941-160">Pokud se aplikace pokusí do vyrovnávací paměti příliš mnoho nahrávání, dojde k selhání lokality, když dojde k vynechání paměti nebo místa na disku.</span><span class="sxs-lookup"><span data-stu-id="9a941-160">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="9a941-161">Pokud velikost nebo frekvence nahrávání souborů vyčerpá prostředky aplikace, použijte streamování.</span><span class="sxs-lookup"><span data-stu-id="9a941-161">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="9a941-162">Z paměti do dočasného souboru na disku se přesune libovolný soubor s vyrovnávací pamětí větší než 64 KB.</span><span class="sxs-lookup"><span data-stu-id="9a941-162">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="9a941-163">Ukládání malých souborů do vyrovnávací paměti je popsáno v následujících částech tohoto tématu:</span><span class="sxs-lookup"><span data-stu-id="9a941-163">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="9a941-164">Fyzické úložiště</span><span class="sxs-lookup"><span data-stu-id="9a941-164">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="9a941-165">Databáze</span><span class="sxs-lookup"><span data-stu-id="9a941-165">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="9a941-166">**Streamování**</span><span class="sxs-lookup"><span data-stu-id="9a941-166">**Streaming**</span></span>

<span data-ttu-id="9a941-167">Soubor se přijímá z požadavku na více částí a přímo se zpracovává nebo ukládá v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9a941-167">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="9a941-168">Streamování nijak významně nezvyšuje výkon.</span><span class="sxs-lookup"><span data-stu-id="9a941-168">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="9a941-169">Streamování snižuje nároky na paměť nebo místo na disku při nahrávání souborů.</span><span class="sxs-lookup"><span data-stu-id="9a941-169">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="9a941-170">Streamování velkých souborů je zahrnuté v části [nahrávání velkých souborů pomocí streamování](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="9a941-170">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="9a941-171">Nahrávání malých souborů s vazbou modelu ve vyrovnávací paměti na fyzické úložiště</span><span class="sxs-lookup"><span data-stu-id="9a941-171">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="9a941-172">Pro nahrání malých souborů použijte formulář s více částmi nebo sestavte požadavek POST pomocí JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="9a941-172">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="9a941-173">Následující příklad ukazuje použití formuláře Razor Pages k nahrání jednoho souboru (*Pages/BufferedSingleFileUploadPhysical. cshtml* do ukázkové aplikace):</span><span class="sxs-lookup"><span data-stu-id="9a941-173">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="9a941-174">Následující příklad je podobný předchozímu příkladu s tím rozdílem, že:</span><span class="sxs-lookup"><span data-stu-id="9a941-174">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="9a941-175">K odeslání dat formuláře se používá JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)).</span><span class="sxs-lookup"><span data-stu-id="9a941-175">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="9a941-176">Neexistuje žádné ověření.</span><span class="sxs-lookup"><span data-stu-id="9a941-176">There's no validation.</span></span>

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

<span data-ttu-id="9a941-177">Chcete-li provést příspěvek formuláře v jazyce JavaScript pro klienty, kteří [nepodporují rozhraní API pro načítání](https://caniuse.com/#feat=fetch), použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="9a941-177">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="9a941-178">Použijte načtenou výplň (například [window. Fetch Fill (GitHub/Fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="9a941-178">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="9a941-179">Použijte `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="9a941-179">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="9a941-180">Příklad:</span><span class="sxs-lookup"><span data-stu-id="9a941-180">For example:</span></span>

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

<span data-ttu-id="9a941-181">Aby bylo možné podporovat nahrávání souborů, musí formuláře HTML určovat typ kódování (`enctype`) `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="9a941-181">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="9a941-182">Pro `files` vstupní prvek, který podporuje nahrávání více souborů, poskytněte `multiple` atribut `<input>` elementu:</span><span class="sxs-lookup"><span data-stu-id="9a941-182">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="9a941-183">Jednotlivé soubory nahrané na server jsou k dispozici prostřednictvím [vazby modelu](xref:mvc/models/model-binding) pomocí <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="9a941-183">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="9a941-184">Ukázková aplikace ukazuje více ukládání souborů do vyrovnávací paměti pro scénáře databáze a fyzických úložišť.</span><span class="sxs-lookup"><span data-stu-id="9a941-184">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="9a941-185">Nepoužívejte **vlastnost** `FileName` <xref:Microsoft.AspNetCore.Http.IFormFile> jinou než pro zobrazení a protokolování.</span><span class="sxs-lookup"><span data-stu-id="9a941-185">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="9a941-186">Při zobrazení nebo protokolování je název souboru kódován HTML.</span><span class="sxs-lookup"><span data-stu-id="9a941-186">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="9a941-187">Útočník může poskytnout škodlivý název souboru, včetně úplných cest nebo relativních cest.</span><span class="sxs-lookup"><span data-stu-id="9a941-187">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="9a941-188">Aplikace by měly:</span><span class="sxs-lookup"><span data-stu-id="9a941-188">Applications should:</span></span>
>
> * <span data-ttu-id="9a941-189">Odeberte cestu z názvu souboru zadaného uživatelem.</span><span class="sxs-lookup"><span data-stu-id="9a941-189">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="9a941-190">Uložte název souboru s příponou PATH s kódováním HTML pro uživatelské rozhraní nebo protokolování.</span><span class="sxs-lookup"><span data-stu-id="9a941-190">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="9a941-191">Vygenerujte nový náhodný název souboru pro úložiště.</span><span class="sxs-lookup"><span data-stu-id="9a941-191">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="9a941-192">Následující kód odstraní cestu z názvu souboru:</span><span class="sxs-lookup"><span data-stu-id="9a941-192">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="9a941-193">Zde uvedené příklady neberou ohled na zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="9a941-193">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="9a941-194">Další informace jsou k dispozici v následujících částech a [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="9a941-194">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="9a941-195">Důležité informace o zabezpečení</span><span class="sxs-lookup"><span data-stu-id="9a941-195">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="9a941-196">Ověřování</span><span class="sxs-lookup"><span data-stu-id="9a941-196">Validation</span></span>](#validation)

<span data-ttu-id="9a941-197">Při nahrávání souborů pomocí vazeb modelů a <xref:Microsoft.AspNetCore.Http.IFormFile>může metoda Action přijmout:</span><span class="sxs-lookup"><span data-stu-id="9a941-197">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="9a941-198">Jeden <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="9a941-198">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="9a941-199">Kterákoli z následujících kolekcí, které reprezentují několik souborů:</span><span class="sxs-lookup"><span data-stu-id="9a941-199">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="9a941-200">[Seznam](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="9a941-200">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="9a941-201">Vazba odpovídá souborům formuláře podle názvu.</span><span class="sxs-lookup"><span data-stu-id="9a941-201">Binding matches form files by name.</span></span> <span data-ttu-id="9a941-202">Například hodnota `name` HTML v `<input type="file" name="formFile">` musí odpovídat vázanému C# parametru nebo vlastnosti (`FormFile`).</span><span class="sxs-lookup"><span data-stu-id="9a941-202">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="9a941-203">Další informace naleznete v části [název atributu matched na název parametru metody post](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="9a941-203">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="9a941-204">Následující příklad:</span><span class="sxs-lookup"><span data-stu-id="9a941-204">The following example:</span></span>

* <span data-ttu-id="9a941-205">Projde jedním nebo více nahranými soubory.</span><span class="sxs-lookup"><span data-stu-id="9a941-205">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="9a941-206">Pomocí [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) vrátí úplnou cestu k souboru, včetně názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="9a941-206">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="9a941-207">Uloží soubory do místního systému souborů pomocí názvu souboru generovaného aplikací.</span><span class="sxs-lookup"><span data-stu-id="9a941-207">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="9a941-208">Vrátí celkový počet nahraných souborů a jejich velikost.</span><span class="sxs-lookup"><span data-stu-id="9a941-208">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="9a941-209">K vygenerování názvu souboru bez cesty použijte `Path.GetRandomFileName`.</span><span class="sxs-lookup"><span data-stu-id="9a941-209">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="9a941-210">V následujícím příkladu je cesta získána z konfigurace:</span><span class="sxs-lookup"><span data-stu-id="9a941-210">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="9a941-211">Cesta předaná <xref:System.IO.FileStream> *musí* zahrnovat název souboru.</span><span class="sxs-lookup"><span data-stu-id="9a941-211">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="9a941-212">Pokud není zadán název souboru, <xref:System.UnauthorizedAccessException> je vyvolána za běhu.</span><span class="sxs-lookup"><span data-stu-id="9a941-212">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="9a941-213">Soubory odeslané pomocí <xref:Microsoft.AspNetCore.Http.IFormFile> techniky jsou uloženy do vyrovnávací paměti nebo na disku na serveru před zpracováním.</span><span class="sxs-lookup"><span data-stu-id="9a941-213">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="9a941-214">V rámci metody Action je obsah <xref:Microsoft.AspNetCore.Http.IFormFile> přístupný jako <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="9a941-214">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="9a941-215">Kromě místního systému souborů je možné soubory ukládat do síťové sdílené složky nebo do služby úložiště souborů, jako je [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="9a941-215">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="9a941-216">Další příklad, který projde několik souborů pro nahrání a používá bezpečné názvy souborů, najdete v ukázkové aplikaci v části *Pages/BufferedMultipleFileUploadPhysical. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="9a941-216">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="9a941-217">[Cesta. GetTempFileName](xref:System.IO.Path.GetTempFileName*) vyvolá <xref:System.IO.IOException>, pokud je vytvořeno více než 65 535 souborů, aniž by bylo nutné odstraňovat předchozí dočasné soubory.</span><span class="sxs-lookup"><span data-stu-id="9a941-217">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="9a941-218">Limit 65 535 souborů je omezen na server.</span><span class="sxs-lookup"><span data-stu-id="9a941-218">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="9a941-219">Další informace o tomto limitu pro operační systém Windows najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="9a941-219">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="9a941-220">GetTempFileNameA – funkce</span><span class="sxs-lookup"><span data-stu-id="9a941-220">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="9a941-221">Nahrávání malých souborů s vazbou modelu s vyrovnávací pamětí do databáze</span><span class="sxs-lookup"><span data-stu-id="9a941-221">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="9a941-222">Chcete-li uložit data binárního souboru do databáze pomocí [Entity Framework](/ef/core/index), definujte v entitě vlastnost <xref:System.Byte> pole:</span><span class="sxs-lookup"><span data-stu-id="9a941-222">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="9a941-223">Zadejte vlastnost modelu stránky pro třídu, která obsahuje <xref:Microsoft.AspNetCore.Http.IFormFile>:</span><span class="sxs-lookup"><span data-stu-id="9a941-223">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="9a941-224"><xref:Microsoft.AspNetCore.Http.IFormFile> lze použít přímo jako parametr metody akce nebo jako vlastnost vázaného modelu.</span><span class="sxs-lookup"><span data-stu-id="9a941-224"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="9a941-225">Předchozí příklad používá vlastnost vázaného modelu.</span><span class="sxs-lookup"><span data-stu-id="9a941-225">The prior example uses a bound model property.</span></span>

<span data-ttu-id="9a941-226">`FileUpload` se používá ve formuláři Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="9a941-226">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="9a941-227">Když je formulář publikovaný na serveru, zkopírujte <xref:Microsoft.AspNetCore.Http.IFormFile> do datového proudu a uložte ho jako pole bajtů v databázi.</span><span class="sxs-lookup"><span data-stu-id="9a941-227">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="9a941-228">V následujícím příkladu `_dbContext` ukládá kontext databáze aplikace:</span><span class="sxs-lookup"><span data-stu-id="9a941-228">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="9a941-229">Předchozí příklad je podobný scénáři, který je znázorněný v ukázkové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="9a941-229">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="9a941-230">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="9a941-230">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="9a941-231">*Pages/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="9a941-231">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="9a941-232">Při ukládání binárních dat do relačních databází buďte opatrní, protože to může mít nepříznivý vliv na výkon.</span><span class="sxs-lookup"><span data-stu-id="9a941-232">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="9a941-233">Nespoléhá se na nebo důvěřujete vlastnosti `FileName` <xref:Microsoft.AspNetCore.Http.IFormFile> bez ověření.</span><span class="sxs-lookup"><span data-stu-id="9a941-233">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="9a941-234">Vlastnost `FileName` by měla být použita pouze pro účely zobrazení a pouze po kódování HTML.</span><span class="sxs-lookup"><span data-stu-id="9a941-234">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="9a941-235">Uvedené příklady nevezmou ohled na zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="9a941-235">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="9a941-236">Další informace jsou k dispozici v následujících částech a [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="9a941-236">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="9a941-237">Důležité informace o zabezpečení</span><span class="sxs-lookup"><span data-stu-id="9a941-237">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="9a941-238">Ověřování</span><span class="sxs-lookup"><span data-stu-id="9a941-238">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="9a941-239">Nahrávání velkých souborů pomocí streamování</span><span class="sxs-lookup"><span data-stu-id="9a941-239">Upload large files with streaming</span></span>

<span data-ttu-id="9a941-240">Následující příklad ukazuje, jak použít JavaScript ke streamování souboru do akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="9a941-240">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="9a941-241">Token proti padělání souboru se generuje pomocí vlastního atributu filtru a předává se do hlaviček protokolu HTTP klienta místo v textu žádosti.</span><span class="sxs-lookup"><span data-stu-id="9a941-241">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="9a941-242">Vzhledem k tomu, že metoda akce zpracovává nahraná data přímo, vazba modelu formuláře je zakázána jiným vlastním filtrem.</span><span class="sxs-lookup"><span data-stu-id="9a941-242">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="9a941-243">V rámci akce je obsah formuláře čten pomocí `MultipartReader`, který čte jednotlivé `MultipartSection`y, zpracovává soubor nebo ukládá obsah podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="9a941-243">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="9a941-244">Po načtení oddílů s více částmi provede akce vlastní vazbu modelu.</span><span class="sxs-lookup"><span data-stu-id="9a941-244">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="9a941-245">Počáteční odpověď stránky načte formulář a uloží token proti padělání do souboru cookie (prostřednictvím atributu `GenerateAntiforgeryTokenCookieAttribute`).</span><span class="sxs-lookup"><span data-stu-id="9a941-245">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="9a941-246">Atribut používá ASP.NET Core integrovanou [podporu proti padělání](xref:security/anti-request-forgery) pro nastavení souboru cookie s tokenem žádosti:</span><span class="sxs-lookup"><span data-stu-id="9a941-246">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="9a941-247">`DisableFormValueModelBindingAttribute` slouží k zakázání vazby modelu:</span><span class="sxs-lookup"><span data-stu-id="9a941-247">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="9a941-248">V ukázkové aplikaci jsou `GenerateAntiforgeryTokenCookieAttribute` a `DisableFormValueModelBindingAttribute` použity jako filtry pro modely stránky aplikace `/StreamedSingleFileUploadDb` a `/StreamedSingleFileUploadPhysical` v `Startup.ConfigureServices` pomocí [Razor Pagesch konvencí](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="9a941-248">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=8-11,17-20)]

<span data-ttu-id="9a941-249">Vzhledem k tomu, že vazba modelu nepřečte formulář, parametry, které jsou svázané z formuláře, se nezobrazují (budou pokračovat v práci s dotazem, trasou a hlavičkou).</span><span class="sxs-lookup"><span data-stu-id="9a941-249">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="9a941-250">Metoda Action pracuje přímo s vlastností `Request`.</span><span class="sxs-lookup"><span data-stu-id="9a941-250">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="9a941-251">Pro čtení jednotlivých oddílů se používá `MultipartReader`.</span><span class="sxs-lookup"><span data-stu-id="9a941-251">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="9a941-252">Data klíč/hodnota se ukládají do `KeyValueAccumulator`.</span><span class="sxs-lookup"><span data-stu-id="9a941-252">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="9a941-253">Po načtení oddílů s více částmi se obsah `KeyValueAccumulator` používá k vytvoření vazby dat formuláře k typu modelu.</span><span class="sxs-lookup"><span data-stu-id="9a941-253">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="9a941-254">Kompletní metoda `StreamingController.UploadDatabase` pro streamování do databáze pomocí EF Core:</span><span class="sxs-lookup"><span data-stu-id="9a941-254">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="9a941-255">`MultipartRequestHelper` (*nástroje/MultipartRequestHelper. cs*):</span><span class="sxs-lookup"><span data-stu-id="9a941-255">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="9a941-256">Kompletní metoda `StreamingController.UploadPhysical` pro streamování do fyzického umístění:</span><span class="sxs-lookup"><span data-stu-id="9a941-256">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="9a941-257">V ukázkové aplikaci jsou kontroly ověřování zpracovávány `FileHelpers.ProcessStreamedFile`.</span><span class="sxs-lookup"><span data-stu-id="9a941-257">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="9a941-258">Ověřování</span><span class="sxs-lookup"><span data-stu-id="9a941-258">Validation</span></span>

<span data-ttu-id="9a941-259">Třída `FileHelpers` ukázkové aplikace ukazuje několik kontrol <xref:Microsoft.AspNetCore.Http.IFormFile> vyrovnávací paměti a nahrávání souborů v datových proudech.</span><span class="sxs-lookup"><span data-stu-id="9a941-259">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="9a941-260">Pro zpracování <xref:Microsoft.AspNetCore.Http.IFormFile> ukládání souborů do vyrovnávací paměti v ukázkové aplikaci si přečtěte část `ProcessFormFile` v souboru *. cs nástrojů Utilities/App.*</span><span class="sxs-lookup"><span data-stu-id="9a941-260">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="9a941-261">Pro zpracování streamované soubory si přečtěte část `ProcessStreamedFile` metoda ve stejném souboru.</span><span class="sxs-lookup"><span data-stu-id="9a941-261">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="9a941-262">Metody zpracování ověřování, které jsou znázorněné v ukázkové aplikaci, nekontrolují obsah nahraných souborů.</span><span class="sxs-lookup"><span data-stu-id="9a941-262">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="9a941-263">Ve většině produkčních scénářů se v souboru používá rozhraní API pro skenování virů nebo malwaru, než je soubor dostupný uživatelům nebo jiným systémům.</span><span class="sxs-lookup"><span data-stu-id="9a941-263">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="9a941-264">I když ukázka tématu poskytuje pracovní příklad technik ověřování, Neimplementujte třídu `FileHelpers` v produkční aplikaci, pokud:</span><span class="sxs-lookup"><span data-stu-id="9a941-264">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="9a941-265">Plně rozumíte implementaci.</span><span class="sxs-lookup"><span data-stu-id="9a941-265">Fully understand the implementation.</span></span>
> * <span data-ttu-id="9a941-266">Upravte implementaci podle potřeby pro prostředí a specifikace aplikace.</span><span class="sxs-lookup"><span data-stu-id="9a941-266">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="9a941-267">**Nikdy nepoužívejte nerozlišený kód zabezpečení v aplikaci bez nutnosti řešit tyto požadavky.**</span><span class="sxs-lookup"><span data-stu-id="9a941-267">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="9a941-268">Ověření obsahu</span><span class="sxs-lookup"><span data-stu-id="9a941-268">Content validation</span></span>

<span data-ttu-id="9a941-269">**Pro nahraný obsah použijte rozhraní API pro kontrolu virů a malwaru třetí strany.**</span><span class="sxs-lookup"><span data-stu-id="9a941-269">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="9a941-270">Prohledávání souborů je náročné na prostředky serveru ve scénářích s vysokým objemem.</span><span class="sxs-lookup"><span data-stu-id="9a941-270">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="9a941-271">Pokud dojde ke snížení výkonu zpracování požadavků z důvodu kontroly souborů, zvažte přesměrování práce skenování na službu na [pozadí](xref:fundamentals/host/hosted-services), případně služby spuštěné na serveru, který se liší od serveru aplikace.</span><span class="sxs-lookup"><span data-stu-id="9a941-271">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="9a941-272">Nahrané soubory se obvykle uchovávají v oblasti v karanténě, dokud je kontrola virů na pozadí nevrátí.</span><span class="sxs-lookup"><span data-stu-id="9a941-272">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="9a941-273">Když soubor projde, soubor se přesune do normálního umístění úložiště souborů.</span><span class="sxs-lookup"><span data-stu-id="9a941-273">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="9a941-274">Tyto kroky se obvykle provádí ve spojení s databázovým záznamem, který indikuje stav kontroly souboru.</span><span class="sxs-lookup"><span data-stu-id="9a941-274">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="9a941-275">Při použití takového přístupu zůstane aplikace a Server App zaměřené na reakci na požadavky.</span><span class="sxs-lookup"><span data-stu-id="9a941-275">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="9a941-276">Ověření přípony souboru</span><span class="sxs-lookup"><span data-stu-id="9a941-276">File extension validation</span></span>

<span data-ttu-id="9a941-277">Přípona nahraného souboru by měla být zaškrtnutá na seznamu povolených rozšíření.</span><span class="sxs-lookup"><span data-stu-id="9a941-277">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="9a941-278">Příklad:</span><span class="sxs-lookup"><span data-stu-id="9a941-278">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="9a941-279">Ověření podpisu souboru</span><span class="sxs-lookup"><span data-stu-id="9a941-279">File signature validation</span></span>

<span data-ttu-id="9a941-280">Podpis souboru se určuje na prvních několika bajtech na začátku souboru.</span><span class="sxs-lookup"><span data-stu-id="9a941-280">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="9a941-281">Tyto bajty lze použít k určení, zda přípona odpovídá obsahu souboru.</span><span class="sxs-lookup"><span data-stu-id="9a941-281">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="9a941-282">Ukázková aplikace zkontroluje podpisy souborů pro několik běžných typů souborů.</span><span class="sxs-lookup"><span data-stu-id="9a941-282">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="9a941-283">V následujícím příkladu se pro tento soubor kontroluje podpis souboru obrázku JPEG:</span><span class="sxs-lookup"><span data-stu-id="9a941-283">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="9a941-284">Další signatury souborů získáte v dokumentaci [signatury souborů](https://www.filesignatures.net/) a oficiálních souborů.</span><span class="sxs-lookup"><span data-stu-id="9a941-284">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="9a941-285">Zabezpečení názvu souboru</span><span class="sxs-lookup"><span data-stu-id="9a941-285">File name security</span></span>

<span data-ttu-id="9a941-286">Nikdy nepoužívejte název souboru dodaný klientem pro uložení souboru do fyzického úložiště.</span><span class="sxs-lookup"><span data-stu-id="9a941-286">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="9a941-287">Vytvořte bezpečný název souboru pro soubor pomocí [cesty. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) nebo [cesty. GetTempFileName](xref:System.IO.Path.GetTempFileName*) a vytvořte úplnou cestu (včetně názvu souboru) pro dočasné úložiště.</span><span class="sxs-lookup"><span data-stu-id="9a941-287">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="9a941-288">Razor automaticky kóduje hodnoty vlastností pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="9a941-288">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="9a941-289">Následující kód je bezpečné použít:</span><span class="sxs-lookup"><span data-stu-id="9a941-289">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="9a941-290">Mimo Razor je vždy <xref:System.Net.WebUtility.HtmlEncode*> obsahu názvu souboru z požadavku uživatele.</span><span class="sxs-lookup"><span data-stu-id="9a941-290">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="9a941-291">Mnoho implementací musí zahrnovat kontrolu, že soubor existuje. v opačném případě je soubor přepsán souborem se stejným názvem.</span><span class="sxs-lookup"><span data-stu-id="9a941-291">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="9a941-292">Poskytněte další logiku pro splnění specifikací vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="9a941-292">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="9a941-293">Ověřování velikosti</span><span class="sxs-lookup"><span data-stu-id="9a941-293">Size validation</span></span>

<span data-ttu-id="9a941-294">Omezte velikost nahraných souborů.</span><span class="sxs-lookup"><span data-stu-id="9a941-294">Limit the size of uploaded files.</span></span>

<span data-ttu-id="9a941-295">V ukázkové aplikaci je velikost souboru omezená na 2 MB (uvedené v bajtech).</span><span class="sxs-lookup"><span data-stu-id="9a941-295">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="9a941-296">Limit je zadán prostřednictvím [Konfigurace](xref:fundamentals/configuration/index) ze souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="9a941-296">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="9a941-297">`FileSizeLimit` je vložen do tříd `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="9a941-297">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="9a941-298">Když velikost souboru překročí limit, soubor se odmítne:</span><span class="sxs-lookup"><span data-stu-id="9a941-298">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="9a941-299">Porovnává hodnotu atributu name s parametrem název metody POST</span><span class="sxs-lookup"><span data-stu-id="9a941-299">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="9a941-300">V nestandardních formulářích, které PUBLIKují data formuláře nebo přímo používají `FormData` JavaScriptu, musí název zadaný v prvku formuláře nebo `FormData` odpovídat názvu parametru v akci kontroleru.</span><span class="sxs-lookup"><span data-stu-id="9a941-300">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="9a941-301">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="9a941-301">In the following example:</span></span>

* <span data-ttu-id="9a941-302">Při použití prvku `<input>` je atribut `name` nastaven na hodnotu `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="9a941-302">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="9a941-303">Při použití `FormData` v jazyce JavaScript je název nastaven na hodnotu `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="9a941-303">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="9a941-304">Použijte pro parametr C# metody (`battlePlans`) shodný název:</span><span class="sxs-lookup"><span data-stu-id="9a941-304">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="9a941-305">Pro Razor Pages metodu obslužné rutiny stránky s názvem `Upload`:</span><span class="sxs-lookup"><span data-stu-id="9a941-305">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="9a941-306">Pro metodu akce po kontroléru MVC:</span><span class="sxs-lookup"><span data-stu-id="9a941-306">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="9a941-307">Konfigurace serveru a aplikace</span><span class="sxs-lookup"><span data-stu-id="9a941-307">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="9a941-308">Omezení délky těla částí</span><span class="sxs-lookup"><span data-stu-id="9a941-308">Multipart body length limit</span></span>

<span data-ttu-id="9a941-309"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> nastaví limit délky jednotlivých částí části.</span><span class="sxs-lookup"><span data-stu-id="9a941-309"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="9a941-310">Oddíly formuláře, které překračují toto omezení, vyvolávají při analýze <xref:System.IO.InvalidDataException>.</span><span class="sxs-lookup"><span data-stu-id="9a941-310">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="9a941-311">Výchozí hodnota je 134 217 728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="9a941-311">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="9a941-312">Upravte limit pomocí <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> nastavení v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9a941-312">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="9a941-313"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> slouží k nastavení <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> pro jednu stránku nebo akci.</span><span class="sxs-lookup"><span data-stu-id="9a941-313"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="9a941-314">V aplikaci Razor Pages použijte filtr s [konvencí](xref:razor-pages/razor-pages-conventions) v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9a941-314">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="9a941-315">V aplikaci Razor Pages nebo aplikaci MVC použijte filtr na model stránky nebo metodu akce:</span><span class="sxs-lookup"><span data-stu-id="9a941-315">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="9a941-316">Kestrel maximální velikost textu požadavku</span><span class="sxs-lookup"><span data-stu-id="9a941-316">Kestrel maximum request body size</span></span>

<span data-ttu-id="9a941-317">Pro aplikace hostované v Kestrel je výchozí maximální velikost textu požadavku 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="9a941-317">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="9a941-318">Přizpůsobte limit pomocí možnosti serveru [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:</span><span class="sxs-lookup"><span data-stu-id="9a941-318">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="9a941-319"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> slouží k nastavení [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) pro jednu stránku nebo akci.</span><span class="sxs-lookup"><span data-stu-id="9a941-319"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="9a941-320">V aplikaci Razor Pages použijte filtr s [konvencí](xref:razor-pages/razor-pages-conventions) v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9a941-320">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="9a941-321">V aplikaci se stránkami Razor nebo v aplikaci MVC použijte filtr na třídu obslužné rutiny stránky nebo na metodu akce:</span><span class="sxs-lookup"><span data-stu-id="9a941-321">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="9a941-322">`RequestSizeLimitAttribute` lze také použít pomocí direktivy [@attribute](xref:mvc/views/razor#attribute) Razor:</span><span class="sxs-lookup"><span data-stu-id="9a941-322">The `RequestSizeLimitAttribute` can also be applied using the [@attribute](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="9a941-323">Další omezení Kestrel</span><span class="sxs-lookup"><span data-stu-id="9a941-323">Other Kestrel limits</span></span>

<span data-ttu-id="9a941-324">Pro aplikace hostované v Kestrel se můžou vztahovat další omezení Kestrel:</span><span class="sxs-lookup"><span data-stu-id="9a941-324">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="9a941-325">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="9a941-325">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="9a941-326">Sazby za data požadavků a odpovědí</span><span class="sxs-lookup"><span data-stu-id="9a941-326">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="9a941-327">Omezení délky obsahu služby IIS</span><span class="sxs-lookup"><span data-stu-id="9a941-327">IIS content length limit</span></span>

<span data-ttu-id="9a941-328">Výchozí limit počtu požadavků (`maxAllowedContentLength`) je 30 000 000 bajtů, což je přibližně 28.6 MB.</span><span class="sxs-lookup"><span data-stu-id="9a941-328">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="9a941-329">Upravte limit v souboru *Web. config* :</span><span class="sxs-lookup"><span data-stu-id="9a941-329">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="9a941-330">Toto nastavení platí pouze pro službu IIS.</span><span class="sxs-lookup"><span data-stu-id="9a941-330">This setting only applies to IIS.</span></span> <span data-ttu-id="9a941-331">K tomuto chování nedochází ve výchozím nastavení při hostování v Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9a941-331">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="9a941-332">Další informace najdete v tématu [omezení požadavků \<requestLimits >](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="9a941-332">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="9a941-333">Omezení v modulu ASP.NET Core nebo přítomnosti modulu filtrování požadavků služby IIS mohou omezit nahrávání na 2 nebo 4 GB.</span><span class="sxs-lookup"><span data-stu-id="9a941-333">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="9a941-334">Další informace najdete v tématu [nelze odeslat soubor o velikosti větší než 2 GB (ASPNET/AspNetCore #2711)](https://github.com/aspnet/AspNetCore/issues/2711).</span><span class="sxs-lookup"><span data-stu-id="9a941-334">For more information, see [Unable to upload file greater than 2GB in size (aspnet/AspNetCore #2711)](https://github.com/aspnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="9a941-335">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="9a941-335">Troubleshoot</span></span>

<span data-ttu-id="9a941-336">Níže jsou uvedeny některé běžné problémy, které se vyskytly při práci s nahráváním souborů a jejich možnými řešeními.</span><span class="sxs-lookup"><span data-stu-id="9a941-336">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="9a941-337">Při nasazení na server služby IIS se nenašla chyba.</span><span class="sxs-lookup"><span data-stu-id="9a941-337">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="9a941-338">Následující chyba znamená, že nahraný soubor překračuje délku nakonfigurovaného obsahu serveru:</span><span class="sxs-lookup"><span data-stu-id="9a941-338">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="9a941-339">Další informace o zvýšení limitu najdete v části [omezení délky obsahu služby IIS](#iis-content-length-limit) .</span><span class="sxs-lookup"><span data-stu-id="9a941-339">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="9a941-340">Chyba připojení</span><span class="sxs-lookup"><span data-stu-id="9a941-340">Connection failure</span></span>

<span data-ttu-id="9a941-341">Chyba připojení a připojení k serveru pro resetování pravděpodobně znamená, že nahraný soubor překračuje maximální velikost textu požadavku Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9a941-341">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="9a941-342">Další informace najdete v části [Kestrel maximální velikost textu požadavku](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="9a941-342">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="9a941-343">Omezení připojení klientů Kestrel mohou také vyžadovat úpravu.</span><span class="sxs-lookup"><span data-stu-id="9a941-343">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="9a941-344">Výjimka odkazu s hodnotou null s IFormFile</span><span class="sxs-lookup"><span data-stu-id="9a941-344">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="9a941-345">Pokud kontroler přijímá odeslané soubory pomocí <xref:Microsoft.AspNetCore.Http.IFormFile>, ale hodnota je `null`, potvrďte, že formulář HTML určuje `enctype` hodnotu `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="9a941-345">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="9a941-346">Pokud tento atribut není nastaven u prvku `<form>`, k odeslání souboru nedochází a všechny vázané argumenty <xref:Microsoft.AspNetCore.Http.IFormFile> jsou `null`.</span><span class="sxs-lookup"><span data-stu-id="9a941-346">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="9a941-347">Ujistěte se také, že [nahrávání názvů v datech formuláře odpovídá pojmenování aplikace](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="9a941-347">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="9a941-348">Proud je příliš dlouhý.</span><span class="sxs-lookup"><span data-stu-id="9a941-348">Stream was too long</span></span>

<span data-ttu-id="9a941-349">Příklady v tomto tématu se spoléhají na <xref:System.IO.MemoryStream> pro uložení obsahu nahraného souboru.</span><span class="sxs-lookup"><span data-stu-id="9a941-349">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="9a941-350">Omezení velikosti `MemoryStream` je `int.MaxValue`.</span><span class="sxs-lookup"><span data-stu-id="9a941-350">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="9a941-351">Pokud scénář nahrávání souborů aplikace vyžaduje, aby obsah souboru byl větší než 50 MB, použijte alternativní přístup, který nespoléhá na jednu `MemoryStream` pro uchovávání obsahu nahraného souboru.</span><span class="sxs-lookup"><span data-stu-id="9a941-351">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9a941-352">ASP.NET Core podporuje nahrávání jednoho nebo více souborů pomocí vazby modelu ve vyrovnávací paměti pro menší soubory a streamování bez vyrovnávací paměti pro větší soubory.</span><span class="sxs-lookup"><span data-stu-id="9a941-352">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="9a941-353">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9a941-353">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="9a941-354">Důležité informace o zabezpečení</span><span class="sxs-lookup"><span data-stu-id="9a941-354">Security considerations</span></span>

<span data-ttu-id="9a941-355">Pokud chcete uživatelům poskytnout možnost nahrávat soubory na server, buďte opatrní.</span><span class="sxs-lookup"><span data-stu-id="9a941-355">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="9a941-356">Útočníci se můžou pokusit:</span><span class="sxs-lookup"><span data-stu-id="9a941-356">Attackers may attempt to:</span></span>

* <span data-ttu-id="9a941-357">Vykoná útok [DOS (Denial of Service](/windows-hardware/drivers/ifs/denial-of-service) ).</span><span class="sxs-lookup"><span data-stu-id="9a941-357">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="9a941-358">Nahrání virů nebo malwaru</span><span class="sxs-lookup"><span data-stu-id="9a941-358">Upload viruses or malware.</span></span>
* <span data-ttu-id="9a941-359">Narušit sítě a servery jinými způsoby.</span><span class="sxs-lookup"><span data-stu-id="9a941-359">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="9a941-360">Bezpečnostní kroky, které snižují pravděpodobnost úspěšného útoku, jsou:</span><span class="sxs-lookup"><span data-stu-id="9a941-360">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="9a941-361">Nahrajte soubory do vyhrazené oblasti pro nahrávání souborů, nejlépe do nesystémové jednotky.</span><span class="sxs-lookup"><span data-stu-id="9a941-361">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="9a941-362">Vyhrazené umístění usnadňuje omezení zabezpečení pro nahrané soubory.</span><span class="sxs-lookup"><span data-stu-id="9a941-362">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="9a941-363">Zakažte oprávnění EXECUTE pro umístění pro nahrání souboru.&dagger;</span><span class="sxs-lookup"><span data-stu-id="9a941-363">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="9a941-364">Neuchovávat nahrané soubory ve stejném adresářovém stromu jako aplikace.&dagger;</span><span class="sxs-lookup"><span data-stu-id="9a941-364">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="9a941-365">Použijte název bezpečného souboru určený aplikací.</span><span class="sxs-lookup"><span data-stu-id="9a941-365">Use a safe file name determined by the app.</span></span> <span data-ttu-id="9a941-366">Nepoužívejte název souboru poskytnutý uživatelem nebo nedůvěryhodného názvu nahraného souboru.&dagger; při zobrazení kódování HTML kódovat název nedůvěryhodného souboru.</span><span class="sxs-lookup"><span data-stu-id="9a941-366">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="9a941-367">Například protokolování názvu souboru nebo zobrazení v uživatelském rozhraní (Razor automaticky kóduje výstup HTML).</span><span class="sxs-lookup"><span data-stu-id="9a941-367">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="9a941-368">Povolte pro specifikaci návrhu aplikace jenom schválené přípony souborů.&dagger;</span><span class="sxs-lookup"><span data-stu-id="9a941-368">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="9a941-369">Ověřte, zda jsou na serveru provedeny kontroly na straně klienta.&dagger; kontroly na straně klienta se snadno obejít.</span><span class="sxs-lookup"><span data-stu-id="9a941-369">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="9a941-370">Ověřte velikost nahraného souboru.</span><span class="sxs-lookup"><span data-stu-id="9a941-370">Check the size of an uploaded file.</span></span> <span data-ttu-id="9a941-371">Nastavte limit maximální velikosti, aby se zabránilo velkým nahrávání.&dagger;</span><span class="sxs-lookup"><span data-stu-id="9a941-371">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="9a941-372">Pokud by soubory neměly být přepsány nahraným souborem se stejným názvem, před nahráním souboru ověřte název souboru proti databázi nebo fyzickému úložišti.</span><span class="sxs-lookup"><span data-stu-id="9a941-372">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="9a941-373">**Před uložením souboru spusťte v nahraném obsahu skener virů nebo malwaru.**</span><span class="sxs-lookup"><span data-stu-id="9a941-373">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="9a941-374">&dagger;ukázková aplikace ukazuje přístup, který splňuje kritéria.</span><span class="sxs-lookup"><span data-stu-id="9a941-374">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="9a941-375">Nahrávání škodlivého kódu do systému je často prvním krokem ke spuštění kódu, který může:</span><span class="sxs-lookup"><span data-stu-id="9a941-375">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="9a941-376">Zcela získá kontrolu nad systémem.</span><span class="sxs-lookup"><span data-stu-id="9a941-376">Completely gain control of a system.</span></span>
> * <span data-ttu-id="9a941-377">Přetížit systém s výsledkem, že dojde k chybě systému.</span><span class="sxs-lookup"><span data-stu-id="9a941-377">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="9a941-378">Napadnout data uživatelů nebo systémových dat.</span><span class="sxs-lookup"><span data-stu-id="9a941-378">Compromise user or system data.</span></span>
> * <span data-ttu-id="9a941-379">Použijte graffiti pro veřejné uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="9a941-379">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="9a941-380">Informace o omezení oblasti útoku při přijímání souborů uživateli najdete v následujících zdrojích informací:</span><span class="sxs-lookup"><span data-stu-id="9a941-380">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="9a941-381">Neomezená nahrávání souboru</span><span class="sxs-lookup"><span data-stu-id="9a941-381">Unrestricted File Upload</span></span>](https://www.owasp.org/index.php/Unrestricted_File_Upload)
> * [<span data-ttu-id="9a941-382">Zabezpečení Azure: Ujistěte se, že jsou při přijímání souborů od uživatelů k dismístě správné ovládací prvky.</span><span class="sxs-lookup"><span data-stu-id="9a941-382">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="9a941-383">Další informace o implementaci bezpečnostních opatření, včetně příkladů z ukázkové aplikace, najdete v části [ověření](#validation) .</span><span class="sxs-lookup"><span data-stu-id="9a941-383">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="9a941-384">Scénáře úložiště</span><span class="sxs-lookup"><span data-stu-id="9a941-384">Storage scenarios</span></span>

<span data-ttu-id="9a941-385">Mezi běžné možnosti úložiště pro soubory patří:</span><span class="sxs-lookup"><span data-stu-id="9a941-385">Common storage options for files include:</span></span>

* <span data-ttu-id="9a941-386">Databáze</span><span class="sxs-lookup"><span data-stu-id="9a941-386">Database</span></span>

  * <span data-ttu-id="9a941-387">U malých nahrávání souborů je databáze často rychlejší než možnosti fyzického úložiště (systému souborů nebo síťového sdílení).</span><span class="sxs-lookup"><span data-stu-id="9a941-387">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="9a941-388">Databáze je často pohodlnější než možnosti fyzického úložiště, protože načtení záznamu databáze pro uživatelská data může současně poskytovat obsah souboru (například obrázek miniatury).</span><span class="sxs-lookup"><span data-stu-id="9a941-388">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="9a941-389">Databáze je potenciálně levnější než použití služby úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="9a941-389">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="9a941-390">Fyzické úložiště (systém souborů nebo síťová sdílená složka)</span><span class="sxs-lookup"><span data-stu-id="9a941-390">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="9a941-391">Pro nahrávání velkých souborů:</span><span class="sxs-lookup"><span data-stu-id="9a941-391">For large file uploads:</span></span>
    * <span data-ttu-id="9a941-392">Omezení databáze mohou omezit velikost nahrávání.</span><span class="sxs-lookup"><span data-stu-id="9a941-392">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="9a941-393">Fyzické úložiště je často méně hospodárné než úložiště v databázi.</span><span class="sxs-lookup"><span data-stu-id="9a941-393">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="9a941-394">Fyzické úložiště je potenciálně levnější než použití služby úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="9a941-394">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="9a941-395">Proces aplikace musí mít oprávnění ke čtení a zápisu do umístění úložiště.</span><span class="sxs-lookup"><span data-stu-id="9a941-395">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="9a941-396">**Nikdy neudělujte oprávnění EXECUTE.**</span><span class="sxs-lookup"><span data-stu-id="9a941-396">**Never grant execute permission.**</span></span>

* <span data-ttu-id="9a941-397">Služba úložiště dat (například [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="9a941-397">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="9a941-398">Služby obvykle nabízejí vylepšenou škálovatelnost a odolnost proti místním řešením, které obvykle podléhají jednomu bodu selhání.</span><span class="sxs-lookup"><span data-stu-id="9a941-398">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="9a941-399">Služby jsou potenciálně nižší náklady ve scénářích infrastruktury velkých úložišť.</span><span class="sxs-lookup"><span data-stu-id="9a941-399">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="9a941-400">Další informace najdete v tématu [rychlý Start: použití .NET k vytvoření objektu BLOB v úložišti objektů](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="9a941-400">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="9a941-401">Téma ukazuje <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, ale <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> lze použít k uložení <xref:System.IO.FileStream> do úložiště objektů BLOB při práci s <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="9a941-401">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="9a941-402">Scénáře nahrávání souborů</span><span class="sxs-lookup"><span data-stu-id="9a941-402">File upload scenarios</span></span>

<span data-ttu-id="9a941-403">Dva obecné přístupy k nahrávání souborů jsou ukládání do vyrovnávací paměti a streamování.</span><span class="sxs-lookup"><span data-stu-id="9a941-403">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="9a941-404">**Do vyrovnávací paměti**</span><span class="sxs-lookup"><span data-stu-id="9a941-404">**Buffering**</span></span>

<span data-ttu-id="9a941-405">Celý soubor je čten do <xref:Microsoft.AspNetCore.Http.IFormFile>, což je C# reprezentace souboru používaného ke zpracování nebo uložení souboru.</span><span class="sxs-lookup"><span data-stu-id="9a941-405">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="9a941-406">Prostředky (disk, paměť) používané při nahrávání souborů závisí na počtu a velikosti souběžných nahrávání souborů.</span><span class="sxs-lookup"><span data-stu-id="9a941-406">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="9a941-407">Pokud se aplikace pokusí do vyrovnávací paměti příliš mnoho nahrávání, dojde k selhání lokality, když dojde k vynechání paměti nebo místa na disku.</span><span class="sxs-lookup"><span data-stu-id="9a941-407">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="9a941-408">Pokud velikost nebo frekvence nahrávání souborů vyčerpá prostředky aplikace, použijte streamování.</span><span class="sxs-lookup"><span data-stu-id="9a941-408">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="9a941-409">Z paměti do dočasného souboru na disku se přesune libovolný soubor s vyrovnávací pamětí větší než 64 KB.</span><span class="sxs-lookup"><span data-stu-id="9a941-409">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="9a941-410">Ukládání malých souborů do vyrovnávací paměti je popsáno v následujících částech tohoto tématu:</span><span class="sxs-lookup"><span data-stu-id="9a941-410">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="9a941-411">Fyzické úložiště</span><span class="sxs-lookup"><span data-stu-id="9a941-411">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="9a941-412">Databáze</span><span class="sxs-lookup"><span data-stu-id="9a941-412">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="9a941-413">**Streamování**</span><span class="sxs-lookup"><span data-stu-id="9a941-413">**Streaming**</span></span>

<span data-ttu-id="9a941-414">Soubor se přijímá z požadavku na více částí a přímo se zpracovává nebo ukládá v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9a941-414">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="9a941-415">Streamování nijak významně nezvyšuje výkon.</span><span class="sxs-lookup"><span data-stu-id="9a941-415">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="9a941-416">Streamování snižuje nároky na paměť nebo místo na disku při nahrávání souborů.</span><span class="sxs-lookup"><span data-stu-id="9a941-416">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="9a941-417">Streamování velkých souborů je zahrnuté v části [nahrávání velkých souborů pomocí streamování](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="9a941-417">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="9a941-418">Nahrávání malých souborů s vazbou modelu ve vyrovnávací paměti na fyzické úložiště</span><span class="sxs-lookup"><span data-stu-id="9a941-418">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="9a941-419">Pro nahrání malých souborů použijte formulář s více částmi nebo sestavte požadavek POST pomocí JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="9a941-419">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="9a941-420">Následující příklad ukazuje použití formuláře Razor Pages k nahrání jednoho souboru (*Pages/BufferedSingleFileUploadPhysical. cshtml* do ukázkové aplikace):</span><span class="sxs-lookup"><span data-stu-id="9a941-420">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="9a941-421">Následující příklad je podobný předchozímu příkladu s tím rozdílem, že:</span><span class="sxs-lookup"><span data-stu-id="9a941-421">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="9a941-422">K odeslání dat formuláře se používá JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)).</span><span class="sxs-lookup"><span data-stu-id="9a941-422">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="9a941-423">Neexistuje žádné ověření.</span><span class="sxs-lookup"><span data-stu-id="9a941-423">There's no validation.</span></span>

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

<span data-ttu-id="9a941-424">Chcete-li provést příspěvek formuláře v jazyce JavaScript pro klienty, kteří [nepodporují rozhraní API pro načítání](https://caniuse.com/#feat=fetch), použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="9a941-424">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="9a941-425">Použijte načtenou výplň (například [window. Fetch Fill (GitHub/Fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="9a941-425">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="9a941-426">Použijte `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="9a941-426">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="9a941-427">Příklad:</span><span class="sxs-lookup"><span data-stu-id="9a941-427">For example:</span></span>

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

<span data-ttu-id="9a941-428">Aby bylo možné podporovat nahrávání souborů, musí formuláře HTML určovat typ kódování (`enctype`) `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="9a941-428">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="9a941-429">Pro `files` vstupní prvek, který podporuje nahrávání více souborů, poskytněte `multiple` atribut `<input>` elementu:</span><span class="sxs-lookup"><span data-stu-id="9a941-429">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="9a941-430">Jednotlivé soubory nahrané na server jsou k dispozici prostřednictvím [vazby modelu](xref:mvc/models/model-binding) pomocí <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="9a941-430">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="9a941-431">Ukázková aplikace ukazuje více ukládání souborů do vyrovnávací paměti pro scénáře databáze a fyzických úložišť.</span><span class="sxs-lookup"><span data-stu-id="9a941-431">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="9a941-432">Nepoužívejte **vlastnost** `FileName` <xref:Microsoft.AspNetCore.Http.IFormFile> jinou než pro zobrazení a protokolování.</span><span class="sxs-lookup"><span data-stu-id="9a941-432">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="9a941-433">Při zobrazení nebo protokolování je název souboru kódován HTML.</span><span class="sxs-lookup"><span data-stu-id="9a941-433">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="9a941-434">Útočník může poskytnout škodlivý název souboru, včetně úplných cest nebo relativních cest.</span><span class="sxs-lookup"><span data-stu-id="9a941-434">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="9a941-435">Aplikace by měly:</span><span class="sxs-lookup"><span data-stu-id="9a941-435">Applications should:</span></span>
>
> * <span data-ttu-id="9a941-436">Odeberte cestu z názvu souboru zadaného uživatelem.</span><span class="sxs-lookup"><span data-stu-id="9a941-436">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="9a941-437">Uložte název souboru s příponou PATH s kódováním HTML pro uživatelské rozhraní nebo protokolování.</span><span class="sxs-lookup"><span data-stu-id="9a941-437">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="9a941-438">Vygenerujte nový náhodný název souboru pro úložiště.</span><span class="sxs-lookup"><span data-stu-id="9a941-438">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="9a941-439">Následující kód odstraní cestu z názvu souboru:</span><span class="sxs-lookup"><span data-stu-id="9a941-439">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="9a941-440">Zde uvedené příklady neberou ohled na zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="9a941-440">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="9a941-441">Další informace jsou k dispozici v následujících částech a [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="9a941-441">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="9a941-442">Důležité informace o zabezpečení</span><span class="sxs-lookup"><span data-stu-id="9a941-442">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="9a941-443">Ověřování</span><span class="sxs-lookup"><span data-stu-id="9a941-443">Validation</span></span>](#validation)

<span data-ttu-id="9a941-444">Při nahrávání souborů pomocí vazeb modelů a <xref:Microsoft.AspNetCore.Http.IFormFile>může metoda Action přijmout:</span><span class="sxs-lookup"><span data-stu-id="9a941-444">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="9a941-445">Jeden <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="9a941-445">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="9a941-446">Kterákoli z následujících kolekcí, které reprezentují několik souborů:</span><span class="sxs-lookup"><span data-stu-id="9a941-446">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="9a941-447">[Seznam](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="9a941-447">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="9a941-448">Vazba odpovídá souborům formuláře podle názvu.</span><span class="sxs-lookup"><span data-stu-id="9a941-448">Binding matches form files by name.</span></span> <span data-ttu-id="9a941-449">Například hodnota `name` HTML v `<input type="file" name="formFile">` musí odpovídat vázanému C# parametru nebo vlastnosti (`FormFile`).</span><span class="sxs-lookup"><span data-stu-id="9a941-449">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="9a941-450">Další informace naleznete v části [název atributu matched na název parametru metody post](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="9a941-450">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="9a941-451">Následující příklad:</span><span class="sxs-lookup"><span data-stu-id="9a941-451">The following example:</span></span>

* <span data-ttu-id="9a941-452">Projde jedním nebo více nahranými soubory.</span><span class="sxs-lookup"><span data-stu-id="9a941-452">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="9a941-453">Pomocí [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) vrátí úplnou cestu k souboru, včetně názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="9a941-453">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="9a941-454">Uloží soubory do místního systému souborů pomocí názvu souboru generovaného aplikací.</span><span class="sxs-lookup"><span data-stu-id="9a941-454">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="9a941-455">Vrátí celkový počet nahraných souborů a jejich velikost.</span><span class="sxs-lookup"><span data-stu-id="9a941-455">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="9a941-456">K vygenerování názvu souboru bez cesty použijte `Path.GetRandomFileName`.</span><span class="sxs-lookup"><span data-stu-id="9a941-456">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="9a941-457">V následujícím příkladu je cesta získána z konfigurace:</span><span class="sxs-lookup"><span data-stu-id="9a941-457">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="9a941-458">Cesta předaná <xref:System.IO.FileStream> *musí* zahrnovat název souboru.</span><span class="sxs-lookup"><span data-stu-id="9a941-458">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="9a941-459">Pokud není zadán název souboru, <xref:System.UnauthorizedAccessException> je vyvolána za běhu.</span><span class="sxs-lookup"><span data-stu-id="9a941-459">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="9a941-460">Soubory odeslané pomocí <xref:Microsoft.AspNetCore.Http.IFormFile> techniky jsou uloženy do vyrovnávací paměti nebo na disku na serveru před zpracováním.</span><span class="sxs-lookup"><span data-stu-id="9a941-460">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="9a941-461">V rámci metody Action je obsah <xref:Microsoft.AspNetCore.Http.IFormFile> přístupný jako <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="9a941-461">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="9a941-462">Kromě místního systému souborů je možné soubory ukládat do síťové sdílené složky nebo do služby úložiště souborů, jako je [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="9a941-462">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="9a941-463">Další příklad, který projde několik souborů pro nahrání a používá bezpečné názvy souborů, najdete v ukázkové aplikaci v části *Pages/BufferedMultipleFileUploadPhysical. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="9a941-463">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="9a941-464">[Cesta. GetTempFileName](xref:System.IO.Path.GetTempFileName*) vyvolá <xref:System.IO.IOException>, pokud je vytvořeno více než 65 535 souborů, aniž by bylo nutné odstraňovat předchozí dočasné soubory.</span><span class="sxs-lookup"><span data-stu-id="9a941-464">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="9a941-465">Limit 65 535 souborů je omezen na server.</span><span class="sxs-lookup"><span data-stu-id="9a941-465">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="9a941-466">Další informace o tomto limitu pro operační systém Windows najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="9a941-466">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="9a941-467">GetTempFileNameA – funkce</span><span class="sxs-lookup"><span data-stu-id="9a941-467">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="9a941-468">Nahrávání malých souborů s vazbou modelu s vyrovnávací pamětí do databáze</span><span class="sxs-lookup"><span data-stu-id="9a941-468">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="9a941-469">Chcete-li uložit data binárního souboru do databáze pomocí [Entity Framework](/ef/core/index), definujte v entitě vlastnost <xref:System.Byte> pole:</span><span class="sxs-lookup"><span data-stu-id="9a941-469">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="9a941-470">Zadejte vlastnost modelu stránky pro třídu, která obsahuje <xref:Microsoft.AspNetCore.Http.IFormFile>:</span><span class="sxs-lookup"><span data-stu-id="9a941-470">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="9a941-471"><xref:Microsoft.AspNetCore.Http.IFormFile> lze použít přímo jako parametr metody akce nebo jako vlastnost vázaného modelu.</span><span class="sxs-lookup"><span data-stu-id="9a941-471"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="9a941-472">Předchozí příklad používá vlastnost vázaného modelu.</span><span class="sxs-lookup"><span data-stu-id="9a941-472">The prior example uses a bound model property.</span></span>

<span data-ttu-id="9a941-473">`FileUpload` se používá ve formuláři Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="9a941-473">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="9a941-474">Když je formulář publikovaný na serveru, zkopírujte <xref:Microsoft.AspNetCore.Http.IFormFile> do datového proudu a uložte ho jako pole bajtů v databázi.</span><span class="sxs-lookup"><span data-stu-id="9a941-474">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="9a941-475">V následujícím příkladu `_dbContext` ukládá kontext databáze aplikace:</span><span class="sxs-lookup"><span data-stu-id="9a941-475">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="9a941-476">Předchozí příklad je podobný scénáři, který je znázorněný v ukázkové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="9a941-476">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="9a941-477">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="9a941-477">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="9a941-478">*Pages/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="9a941-478">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="9a941-479">Při ukládání binárních dat do relačních databází buďte opatrní, protože to může mít nepříznivý vliv na výkon.</span><span class="sxs-lookup"><span data-stu-id="9a941-479">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="9a941-480">Nespoléhá se na nebo důvěřujete vlastnosti `FileName` <xref:Microsoft.AspNetCore.Http.IFormFile> bez ověření.</span><span class="sxs-lookup"><span data-stu-id="9a941-480">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="9a941-481">Vlastnost `FileName` by měla být použita pouze pro účely zobrazení a pouze po kódování HTML.</span><span class="sxs-lookup"><span data-stu-id="9a941-481">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="9a941-482">Uvedené příklady nevezmou ohled na zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="9a941-482">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="9a941-483">Další informace jsou k dispozici v následujících částech a [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="9a941-483">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="9a941-484">Důležité informace o zabezpečení</span><span class="sxs-lookup"><span data-stu-id="9a941-484">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="9a941-485">Ověřování</span><span class="sxs-lookup"><span data-stu-id="9a941-485">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="9a941-486">Nahrávání velkých souborů pomocí streamování</span><span class="sxs-lookup"><span data-stu-id="9a941-486">Upload large files with streaming</span></span>

<span data-ttu-id="9a941-487">Následující příklad ukazuje, jak použít JavaScript ke streamování souboru do akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="9a941-487">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="9a941-488">Token proti padělání souboru se generuje pomocí vlastního atributu filtru a předává se do hlaviček protokolu HTTP klienta místo v textu žádosti.</span><span class="sxs-lookup"><span data-stu-id="9a941-488">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="9a941-489">Vzhledem k tomu, že metoda akce zpracovává nahraná data přímo, vazba modelu formuláře je zakázána jiným vlastním filtrem.</span><span class="sxs-lookup"><span data-stu-id="9a941-489">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="9a941-490">V rámci akce je obsah formuláře čten pomocí `MultipartReader`, který čte jednotlivé `MultipartSection`y, zpracovává soubor nebo ukládá obsah podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="9a941-490">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="9a941-491">Po načtení oddílů s více částmi provede akce vlastní vazbu modelu.</span><span class="sxs-lookup"><span data-stu-id="9a941-491">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="9a941-492">Počáteční odpověď stránky načte formulář a uloží token proti padělání do souboru cookie (prostřednictvím atributu `GenerateAntiforgeryTokenCookieAttribute`).</span><span class="sxs-lookup"><span data-stu-id="9a941-492">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="9a941-493">Atribut používá ASP.NET Core integrovanou [podporu proti padělání](xref:security/anti-request-forgery) pro nastavení souboru cookie s tokenem žádosti:</span><span class="sxs-lookup"><span data-stu-id="9a941-493">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="9a941-494">`DisableFormValueModelBindingAttribute` slouží k zakázání vazby modelu:</span><span class="sxs-lookup"><span data-stu-id="9a941-494">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="9a941-495">V ukázkové aplikaci jsou `GenerateAntiforgeryTokenCookieAttribute` a `DisableFormValueModelBindingAttribute` použity jako filtry pro modely stránky aplikace `/StreamedSingleFileUploadDb` a `/StreamedSingleFileUploadPhysical` v `Startup.ConfigureServices` pomocí [Razor Pagesch konvencí](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="9a941-495">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="9a941-496">Vzhledem k tomu, že vazba modelu nepřečte formulář, parametry, které jsou svázané z formuláře, se nezobrazují (budou pokračovat v práci s dotazem, trasou a hlavičkou).</span><span class="sxs-lookup"><span data-stu-id="9a941-496">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="9a941-497">Metoda Action pracuje přímo s vlastností `Request`.</span><span class="sxs-lookup"><span data-stu-id="9a941-497">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="9a941-498">Pro čtení jednotlivých oddílů se používá `MultipartReader`.</span><span class="sxs-lookup"><span data-stu-id="9a941-498">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="9a941-499">Data klíč/hodnota se ukládají do `KeyValueAccumulator`.</span><span class="sxs-lookup"><span data-stu-id="9a941-499">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="9a941-500">Po načtení oddílů s více částmi se obsah `KeyValueAccumulator` používá k vytvoření vazby dat formuláře k typu modelu.</span><span class="sxs-lookup"><span data-stu-id="9a941-500">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="9a941-501">Kompletní metoda `StreamingController.UploadDatabase` pro streamování do databáze pomocí EF Core:</span><span class="sxs-lookup"><span data-stu-id="9a941-501">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="9a941-502">`MultipartRequestHelper` (*nástroje/MultipartRequestHelper. cs*):</span><span class="sxs-lookup"><span data-stu-id="9a941-502">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="9a941-503">Kompletní metoda `StreamingController.UploadPhysical` pro streamování do fyzického umístění:</span><span class="sxs-lookup"><span data-stu-id="9a941-503">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="9a941-504">V ukázkové aplikaci jsou kontroly ověřování zpracovávány `FileHelpers.ProcessStreamedFile`.</span><span class="sxs-lookup"><span data-stu-id="9a941-504">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="9a941-505">Ověřování</span><span class="sxs-lookup"><span data-stu-id="9a941-505">Validation</span></span>

<span data-ttu-id="9a941-506">Třída `FileHelpers` ukázkové aplikace ukazuje několik kontrol <xref:Microsoft.AspNetCore.Http.IFormFile> vyrovnávací paměti a nahrávání souborů v datových proudech.</span><span class="sxs-lookup"><span data-stu-id="9a941-506">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="9a941-507">Pro zpracování <xref:Microsoft.AspNetCore.Http.IFormFile> ukládání souborů do vyrovnávací paměti v ukázkové aplikaci si přečtěte část `ProcessFormFile` v souboru *. cs nástrojů Utilities/App.*</span><span class="sxs-lookup"><span data-stu-id="9a941-507">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="9a941-508">Pro zpracování streamované soubory si přečtěte část `ProcessStreamedFile` metoda ve stejném souboru.</span><span class="sxs-lookup"><span data-stu-id="9a941-508">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="9a941-509">Metody zpracování ověřování, které jsou znázorněné v ukázkové aplikaci, nekontrolují obsah nahraných souborů.</span><span class="sxs-lookup"><span data-stu-id="9a941-509">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="9a941-510">Ve většině produkčních scénářů se v souboru používá rozhraní API pro skenování virů nebo malwaru, než je soubor dostupný uživatelům nebo jiným systémům.</span><span class="sxs-lookup"><span data-stu-id="9a941-510">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="9a941-511">I když ukázka tématu poskytuje pracovní příklad technik ověřování, Neimplementujte třídu `FileHelpers` v produkční aplikaci, pokud:</span><span class="sxs-lookup"><span data-stu-id="9a941-511">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="9a941-512">Plně rozumíte implementaci.</span><span class="sxs-lookup"><span data-stu-id="9a941-512">Fully understand the implementation.</span></span>
> * <span data-ttu-id="9a941-513">Upravte implementaci podle potřeby pro prostředí a specifikace aplikace.</span><span class="sxs-lookup"><span data-stu-id="9a941-513">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="9a941-514">**Nikdy nepoužívejte nerozlišený kód zabezpečení v aplikaci bez nutnosti řešit tyto požadavky.**</span><span class="sxs-lookup"><span data-stu-id="9a941-514">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="9a941-515">Ověření obsahu</span><span class="sxs-lookup"><span data-stu-id="9a941-515">Content validation</span></span>

<span data-ttu-id="9a941-516">**Pro nahraný obsah použijte rozhraní API pro kontrolu virů a malwaru třetí strany.**</span><span class="sxs-lookup"><span data-stu-id="9a941-516">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="9a941-517">Prohledávání souborů je náročné na prostředky serveru ve scénářích s vysokým objemem.</span><span class="sxs-lookup"><span data-stu-id="9a941-517">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="9a941-518">Pokud dojde ke snížení výkonu zpracování požadavků z důvodu kontroly souborů, zvažte přesměrování práce skenování na službu na [pozadí](xref:fundamentals/host/hosted-services), případně služby spuštěné na serveru, který se liší od serveru aplikace.</span><span class="sxs-lookup"><span data-stu-id="9a941-518">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="9a941-519">Nahrané soubory se obvykle uchovávají v oblasti v karanténě, dokud je kontrola virů na pozadí nevrátí.</span><span class="sxs-lookup"><span data-stu-id="9a941-519">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="9a941-520">Když soubor projde, soubor se přesune do normálního umístění úložiště souborů.</span><span class="sxs-lookup"><span data-stu-id="9a941-520">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="9a941-521">Tyto kroky se obvykle provádí ve spojení s databázovým záznamem, který indikuje stav kontroly souboru.</span><span class="sxs-lookup"><span data-stu-id="9a941-521">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="9a941-522">Při použití takového přístupu zůstane aplikace a Server App zaměřené na reakci na požadavky.</span><span class="sxs-lookup"><span data-stu-id="9a941-522">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="9a941-523">Ověření přípony souboru</span><span class="sxs-lookup"><span data-stu-id="9a941-523">File extension validation</span></span>

<span data-ttu-id="9a941-524">Přípona nahraného souboru by měla být zaškrtnutá na seznamu povolených rozšíření.</span><span class="sxs-lookup"><span data-stu-id="9a941-524">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="9a941-525">Příklad:</span><span class="sxs-lookup"><span data-stu-id="9a941-525">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="9a941-526">Ověření podpisu souboru</span><span class="sxs-lookup"><span data-stu-id="9a941-526">File signature validation</span></span>

<span data-ttu-id="9a941-527">Podpis souboru se určuje na prvních několika bajtech na začátku souboru.</span><span class="sxs-lookup"><span data-stu-id="9a941-527">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="9a941-528">Tyto bajty lze použít k určení, zda přípona odpovídá obsahu souboru.</span><span class="sxs-lookup"><span data-stu-id="9a941-528">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="9a941-529">Ukázková aplikace zkontroluje podpisy souborů pro několik běžných typů souborů.</span><span class="sxs-lookup"><span data-stu-id="9a941-529">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="9a941-530">V následujícím příkladu se pro tento soubor kontroluje podpis souboru obrázku JPEG:</span><span class="sxs-lookup"><span data-stu-id="9a941-530">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="9a941-531">Další signatury souborů získáte v dokumentaci [signatury souborů](https://www.filesignatures.net/) a oficiálních souborů.</span><span class="sxs-lookup"><span data-stu-id="9a941-531">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="9a941-532">Zabezpečení názvu souboru</span><span class="sxs-lookup"><span data-stu-id="9a941-532">File name security</span></span>

<span data-ttu-id="9a941-533">Nikdy nepoužívejte název souboru dodaný klientem pro uložení souboru do fyzického úložiště.</span><span class="sxs-lookup"><span data-stu-id="9a941-533">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="9a941-534">Vytvořte bezpečný název souboru pro soubor pomocí [cesty. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) nebo [cesty. GetTempFileName](xref:System.IO.Path.GetTempFileName*) a vytvořte úplnou cestu (včetně názvu souboru) pro dočasné úložiště.</span><span class="sxs-lookup"><span data-stu-id="9a941-534">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="9a941-535">Razor automaticky kóduje hodnoty vlastností pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="9a941-535">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="9a941-536">Následující kód je bezpečné použít:</span><span class="sxs-lookup"><span data-stu-id="9a941-536">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="9a941-537">Mimo Razor je vždy <xref:System.Net.WebUtility.HtmlEncode*> obsahu názvu souboru z požadavku uživatele.</span><span class="sxs-lookup"><span data-stu-id="9a941-537">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="9a941-538">Mnoho implementací musí zahrnovat kontrolu, že soubor existuje. v opačném případě je soubor přepsán souborem se stejným názvem.</span><span class="sxs-lookup"><span data-stu-id="9a941-538">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="9a941-539">Poskytněte další logiku pro splnění specifikací vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="9a941-539">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="9a941-540">Ověřování velikosti</span><span class="sxs-lookup"><span data-stu-id="9a941-540">Size validation</span></span>

<span data-ttu-id="9a941-541">Omezte velikost nahraných souborů.</span><span class="sxs-lookup"><span data-stu-id="9a941-541">Limit the size of uploaded files.</span></span>

<span data-ttu-id="9a941-542">V ukázkové aplikaci je velikost souboru omezená na 2 MB (uvedené v bajtech).</span><span class="sxs-lookup"><span data-stu-id="9a941-542">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="9a941-543">Limit je zadán prostřednictvím [Konfigurace](xref:fundamentals/configuration/index) ze souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="9a941-543">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="9a941-544">`FileSizeLimit` je vložen do tříd `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="9a941-544">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="9a941-545">Když velikost souboru překročí limit, soubor se odmítne:</span><span class="sxs-lookup"><span data-stu-id="9a941-545">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="9a941-546">Porovnává hodnotu atributu name s parametrem název metody POST</span><span class="sxs-lookup"><span data-stu-id="9a941-546">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="9a941-547">V nestandardních formulářích, které PUBLIKují data formuláře nebo přímo používají `FormData` JavaScriptu, musí název zadaný v prvku formuláře nebo `FormData` odpovídat názvu parametru v akci kontroleru.</span><span class="sxs-lookup"><span data-stu-id="9a941-547">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="9a941-548">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="9a941-548">In the following example:</span></span>

* <span data-ttu-id="9a941-549">Při použití prvku `<input>` je atribut `name` nastaven na hodnotu `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="9a941-549">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="9a941-550">Při použití `FormData` v jazyce JavaScript je název nastaven na hodnotu `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="9a941-550">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="9a941-551">Použijte pro parametr C# metody (`battlePlans`) shodný název:</span><span class="sxs-lookup"><span data-stu-id="9a941-551">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="9a941-552">Pro Razor Pages metodu obslužné rutiny stránky s názvem `Upload`:</span><span class="sxs-lookup"><span data-stu-id="9a941-552">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="9a941-553">Pro metodu akce po kontroléru MVC:</span><span class="sxs-lookup"><span data-stu-id="9a941-553">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="9a941-554">Konfigurace serveru a aplikace</span><span class="sxs-lookup"><span data-stu-id="9a941-554">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="9a941-555">Omezení délky těla částí</span><span class="sxs-lookup"><span data-stu-id="9a941-555">Multipart body length limit</span></span>

<span data-ttu-id="9a941-556"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> nastaví limit délky jednotlivých částí části.</span><span class="sxs-lookup"><span data-stu-id="9a941-556"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="9a941-557">Oddíly formuláře, které překračují toto omezení, vyvolávají při analýze <xref:System.IO.InvalidDataException>.</span><span class="sxs-lookup"><span data-stu-id="9a941-557">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="9a941-558">Výchozí hodnota je 134 217 728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="9a941-558">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="9a941-559">Upravte limit pomocí <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> nastavení v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9a941-559">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="9a941-560"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> slouží k nastavení <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> pro jednu stránku nebo akci.</span><span class="sxs-lookup"><span data-stu-id="9a941-560"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="9a941-561">V aplikaci Razor Pages použijte filtr s [konvencí](xref:razor-pages/razor-pages-conventions) v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9a941-561">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="9a941-562">V aplikaci Razor Pages nebo aplikaci MVC použijte filtr na model stránky nebo metodu akce:</span><span class="sxs-lookup"><span data-stu-id="9a941-562">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="9a941-563">Kestrel maximální velikost textu požadavku</span><span class="sxs-lookup"><span data-stu-id="9a941-563">Kestrel maximum request body size</span></span>

<span data-ttu-id="9a941-564">Pro aplikace hostované v Kestrel je výchozí maximální velikost textu požadavku 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="9a941-564">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="9a941-565">Přizpůsobte limit pomocí možnosti serveru [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:</span><span class="sxs-lookup"><span data-stu-id="9a941-565">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="9a941-566"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> slouží k nastavení [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) pro jednu stránku nebo akci.</span><span class="sxs-lookup"><span data-stu-id="9a941-566"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="9a941-567">V aplikaci Razor Pages použijte filtr s [konvencí](xref:razor-pages/razor-pages-conventions) v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9a941-567">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="9a941-568">V aplikaci se stránkami Razor nebo v aplikaci MVC použijte filtr na třídu obslužné rutiny stránky nebo na metodu akce:</span><span class="sxs-lookup"><span data-stu-id="9a941-568">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="9a941-569">Další omezení Kestrel</span><span class="sxs-lookup"><span data-stu-id="9a941-569">Other Kestrel limits</span></span>

<span data-ttu-id="9a941-570">Pro aplikace hostované v Kestrel se můžou vztahovat další omezení Kestrel:</span><span class="sxs-lookup"><span data-stu-id="9a941-570">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="9a941-571">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="9a941-571">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="9a941-572">Sazby za data požadavků a odpovědí</span><span class="sxs-lookup"><span data-stu-id="9a941-572">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="9a941-573">Omezení délky obsahu služby IIS</span><span class="sxs-lookup"><span data-stu-id="9a941-573">IIS content length limit</span></span>

<span data-ttu-id="9a941-574">Výchozí limit počtu požadavků (`maxAllowedContentLength`) je 30 000 000 bajtů, což je přibližně 28.6 MB.</span><span class="sxs-lookup"><span data-stu-id="9a941-574">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="9a941-575">Upravte limit v souboru *Web. config* :</span><span class="sxs-lookup"><span data-stu-id="9a941-575">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="9a941-576">Toto nastavení platí pouze pro službu IIS.</span><span class="sxs-lookup"><span data-stu-id="9a941-576">This setting only applies to IIS.</span></span> <span data-ttu-id="9a941-577">K tomuto chování nedochází ve výchozím nastavení při hostování v Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9a941-577">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="9a941-578">Další informace najdete v tématu [omezení požadavků \<requestLimits >](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="9a941-578">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="9a941-579">Omezení v modulu ASP.NET Core nebo přítomnosti modulu filtrování požadavků služby IIS mohou omezit nahrávání na 2 nebo 4 GB.</span><span class="sxs-lookup"><span data-stu-id="9a941-579">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="9a941-580">Další informace najdete v tématu [nelze odeslat soubor o velikosti větší než 2 GB (ASPNET/AspNetCore #2711)](https://github.com/aspnet/AspNetCore/issues/2711).</span><span class="sxs-lookup"><span data-stu-id="9a941-580">For more information, see [Unable to upload file greater than 2GB in size (aspnet/AspNetCore #2711)](https://github.com/aspnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="9a941-581">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="9a941-581">Troubleshoot</span></span>

<span data-ttu-id="9a941-582">Níže jsou uvedeny některé běžné problémy, které se vyskytly při práci s nahráváním souborů a jejich možnými řešeními.</span><span class="sxs-lookup"><span data-stu-id="9a941-582">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="9a941-583">Při nasazení na server služby IIS se nenašla chyba.</span><span class="sxs-lookup"><span data-stu-id="9a941-583">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="9a941-584">Následující chyba znamená, že nahraný soubor překračuje délku nakonfigurovaného obsahu serveru:</span><span class="sxs-lookup"><span data-stu-id="9a941-584">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="9a941-585">Další informace o zvýšení limitu najdete v části [omezení délky obsahu služby IIS](#iis-content-length-limit) .</span><span class="sxs-lookup"><span data-stu-id="9a941-585">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="9a941-586">Chyba připojení</span><span class="sxs-lookup"><span data-stu-id="9a941-586">Connection failure</span></span>

<span data-ttu-id="9a941-587">Chyba připojení a připojení k serveru pro resetování pravděpodobně znamená, že nahraný soubor překračuje maximální velikost textu požadavku Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9a941-587">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="9a941-588">Další informace najdete v části [Kestrel maximální velikost textu požadavku](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="9a941-588">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="9a941-589">Omezení připojení klientů Kestrel mohou také vyžadovat úpravu.</span><span class="sxs-lookup"><span data-stu-id="9a941-589">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="9a941-590">Výjimka odkazu s hodnotou null s IFormFile</span><span class="sxs-lookup"><span data-stu-id="9a941-590">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="9a941-591">Pokud kontroler přijímá odeslané soubory pomocí <xref:Microsoft.AspNetCore.Http.IFormFile>, ale hodnota je `null`, potvrďte, že formulář HTML určuje `enctype` hodnotu `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="9a941-591">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="9a941-592">Pokud tento atribut není nastaven u prvku `<form>`, k odeslání souboru nedochází a všechny vázané argumenty <xref:Microsoft.AspNetCore.Http.IFormFile> jsou `null`.</span><span class="sxs-lookup"><span data-stu-id="9a941-592">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="9a941-593">Ujistěte se také, že [nahrávání názvů v datech formuláře odpovídá pojmenování aplikace](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="9a941-593">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="9a941-594">Proud je příliš dlouhý.</span><span class="sxs-lookup"><span data-stu-id="9a941-594">Stream was too long</span></span>

<span data-ttu-id="9a941-595">Příklady v tomto tématu se spoléhají na <xref:System.IO.MemoryStream> pro uložení obsahu nahraného souboru.</span><span class="sxs-lookup"><span data-stu-id="9a941-595">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="9a941-596">Omezení velikosti `MemoryStream` je `int.MaxValue`.</span><span class="sxs-lookup"><span data-stu-id="9a941-596">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="9a941-597">Pokud scénář nahrávání souborů aplikace vyžaduje, aby obsah souboru byl větší než 50 MB, použijte alternativní přístup, který nespoléhá na jednu `MemoryStream` pro uchovávání obsahu nahraného souboru.</span><span class="sxs-lookup"><span data-stu-id="9a941-597">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="9a941-598">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="9a941-598">Additional resources</span></span>

* [<span data-ttu-id="9a941-599">Neomezená nahrávání souboru</span><span class="sxs-lookup"><span data-stu-id="9a941-599">Unrestricted File Upload</span></span>](https://www.owasp.org/index.php/Unrestricted_File_Upload)
* [<span data-ttu-id="9a941-600">Zabezpečení Azure: rámec zabezpečení: ověření vstupu | Hrozeb</span><span class="sxs-lookup"><span data-stu-id="9a941-600">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="9a941-601">Vzory návrhu cloudu Azure: vzor osobního Key</span><span class="sxs-lookup"><span data-stu-id="9a941-601">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
