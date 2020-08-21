---
title: Nahrání souborů v ASP.NET Core
author: rick-anderson
description: Jak používat vazbu modelu a streamování k nahrávání souborů v ASP.NET Core MVC
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/21/2020
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
uid: mvc/models/file-uploads
ms.openlocfilehash: 6ff78b26e8e2363cf6c54ebb2a392f390fb2995c
ms.sourcegitcommit: cd412a44f26cb416ceb348fc0a1ccc9a6e9ca73e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2020
ms.locfileid: "88720276"
---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="e721a-103">Nahrání souborů v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e721a-103">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="e721a-104">[Steve Smith](https://ardalis.com/) a [Rutgerá](https://github.com/rutix) zaplavení</span><span class="sxs-lookup"><span data-stu-id="e721a-104">By [Steve Smith](https://ardalis.com/) and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="e721a-105">ASP.NET Core podporuje nahrávání jednoho nebo více souborů pomocí vazby modelu ve vyrovnávací paměti pro menší soubory a streamování bez vyrovnávací paměti pro větší soubory.</span><span class="sxs-lookup"><span data-stu-id="e721a-105">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="e721a-106">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e721a-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="e721a-107">Důležité informace o zabezpečení</span><span class="sxs-lookup"><span data-stu-id="e721a-107">Security considerations</span></span>

<span data-ttu-id="e721a-108">Pokud chcete uživatelům poskytnout možnost nahrávat soubory na server, buďte opatrní.</span><span class="sxs-lookup"><span data-stu-id="e721a-108">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="e721a-109">Útočníci se můžou pokusit:</span><span class="sxs-lookup"><span data-stu-id="e721a-109">Attackers may attempt to:</span></span>

* <span data-ttu-id="e721a-110">Vykoná útok [DOS (Denial of Service](/windows-hardware/drivers/ifs/denial-of-service) ).</span><span class="sxs-lookup"><span data-stu-id="e721a-110">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="e721a-111">Nahrání virů nebo malwaru</span><span class="sxs-lookup"><span data-stu-id="e721a-111">Upload viruses or malware.</span></span>
* <span data-ttu-id="e721a-112">Narušit sítě a servery jinými způsoby.</span><span class="sxs-lookup"><span data-stu-id="e721a-112">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="e721a-113">Bezpečnostní kroky, které snižují pravděpodobnost úspěšného útoku, jsou:</span><span class="sxs-lookup"><span data-stu-id="e721a-113">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="e721a-114">Nahrajte soubory do vyhrazené oblasti pro nahrávání souborů, nejlépe do nesystémové jednotky.</span><span class="sxs-lookup"><span data-stu-id="e721a-114">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="e721a-115">Vyhrazené umístění usnadňuje omezení zabezpečení pro nahrané soubory.</span><span class="sxs-lookup"><span data-stu-id="e721a-115">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="e721a-116">Zakažte oprávnění EXECUTE pro umístění pro nahrání souboru.&dagger;</span><span class="sxs-lookup"><span data-stu-id="e721a-116">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="e721a-117">Neuchovávat nahrané soubory ve stejném stromu adresářů jako aplikace. **not**&dagger;</span><span class="sxs-lookup"><span data-stu-id="e721a-117">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="e721a-118">Použijte název bezpečného souboru určený aplikací.</span><span class="sxs-lookup"><span data-stu-id="e721a-118">Use a safe file name determined by the app.</span></span> <span data-ttu-id="e721a-119">Nepoužívejte název souboru poskytnutý uživatelem nebo nedůvěryhodného názvu nahraného souboru. &dagger; HTML při zobrazení kódování názvu nedůvěryhodného souboru.</span><span class="sxs-lookup"><span data-stu-id="e721a-119">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="e721a-120">Například protokolování názvu souboru nebo zobrazení v uživatelském rozhraní ( Razor Automatické kódování HTML kódování).</span><span class="sxs-lookup"><span data-stu-id="e721a-120">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="e721a-121">Povolte pro specifikaci návrhu aplikace jenom schválené přípony souborů.&dagger;</span><span class="sxs-lookup"><span data-stu-id="e721a-121">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="e721a-122">Ověřte, zda jsou na serveru provedeny kontroly na straně klienta. &dagger; Kontroly na straně klienta je snadné obejít.</span><span class="sxs-lookup"><span data-stu-id="e721a-122">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="e721a-123">Ověřte velikost nahraného souboru.</span><span class="sxs-lookup"><span data-stu-id="e721a-123">Check the size of an uploaded file.</span></span> <span data-ttu-id="e721a-124">Nastavte limit maximální velikosti, aby se zabránilo velkým nahrávání.&dagger;</span><span class="sxs-lookup"><span data-stu-id="e721a-124">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="e721a-125">Pokud by soubory neměly být přepsány nahraným souborem se stejným názvem, před nahráním souboru ověřte název souboru proti databázi nebo fyzickému úložišti.</span><span class="sxs-lookup"><span data-stu-id="e721a-125">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="e721a-126">**Před uložením souboru spusťte v nahraném obsahu skener virů nebo malwaru.**</span><span class="sxs-lookup"><span data-stu-id="e721a-126">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="e721a-127">&dagger;Ukázková aplikace předvádí přístup, který splňuje kritéria.</span><span class="sxs-lookup"><span data-stu-id="e721a-127">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="e721a-128">Nahrávání škodlivého kódu do systému je často prvním krokem ke spuštění kódu, který může:</span><span class="sxs-lookup"><span data-stu-id="e721a-128">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="e721a-129">Zcela získá kontrolu nad systémem.</span><span class="sxs-lookup"><span data-stu-id="e721a-129">Completely gain control of a system.</span></span>
> * <span data-ttu-id="e721a-130">Přetížit systém s výsledkem, že dojde k chybě systému.</span><span class="sxs-lookup"><span data-stu-id="e721a-130">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="e721a-131">Napadnout data uživatelů nebo systémových dat.</span><span class="sxs-lookup"><span data-stu-id="e721a-131">Compromise user or system data.</span></span>
> * <span data-ttu-id="e721a-132">Použijte graffiti pro veřejné uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e721a-132">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="e721a-133">Informace o omezení oblasti útoku při přijímání souborů uživateli najdete v následujících zdrojích informací:</span><span class="sxs-lookup"><span data-stu-id="e721a-133">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="e721a-134">Neomezená nahrávání souboru</span><span class="sxs-lookup"><span data-stu-id="e721a-134">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="e721a-135">Zabezpečení Azure: Ujistěte se, že jsou při přijímání souborů od uživatelů k dismístě správné ovládací prvky.</span><span class="sxs-lookup"><span data-stu-id="e721a-135">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="e721a-136">Další informace o implementaci bezpečnostních opatření, včetně příkladů z ukázkové aplikace, najdete v části [ověření](#validation) .</span><span class="sxs-lookup"><span data-stu-id="e721a-136">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="e721a-137">Scénáře úložiště</span><span class="sxs-lookup"><span data-stu-id="e721a-137">Storage scenarios</span></span>

<span data-ttu-id="e721a-138">Mezi běžné možnosti úložiště pro soubory patří:</span><span class="sxs-lookup"><span data-stu-id="e721a-138">Common storage options for files include:</span></span>

* <span data-ttu-id="e721a-139">Databáze</span><span class="sxs-lookup"><span data-stu-id="e721a-139">Database</span></span>

  * <span data-ttu-id="e721a-140">U malých nahrávání souborů je databáze často rychlejší než možnosti fyzického úložiště (systému souborů nebo síťového sdílení).</span><span class="sxs-lookup"><span data-stu-id="e721a-140">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="e721a-141">Databáze je často pohodlnější než možnosti fyzického úložiště, protože načtení záznamu databáze pro uživatelská data může současně poskytovat obsah souboru (například obrázek miniatury).</span><span class="sxs-lookup"><span data-stu-id="e721a-141">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="e721a-142">Databáze je potenciálně levnější než použití služby úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="e721a-142">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="e721a-143">Fyzické úložiště (systém souborů nebo síťová sdílená složka)</span><span class="sxs-lookup"><span data-stu-id="e721a-143">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="e721a-144">Pro nahrávání velkých souborů:</span><span class="sxs-lookup"><span data-stu-id="e721a-144">For large file uploads:</span></span>
    * <span data-ttu-id="e721a-145">Omezení databáze mohou omezit velikost nahrávání.</span><span class="sxs-lookup"><span data-stu-id="e721a-145">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="e721a-146">Fyzické úložiště je často méně hospodárné než úložiště v databázi.</span><span class="sxs-lookup"><span data-stu-id="e721a-146">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="e721a-147">Fyzické úložiště je potenciálně levnější než použití služby úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="e721a-147">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="e721a-148">Proces aplikace musí mít oprávnění ke čtení a zápisu do umístění úložiště.</span><span class="sxs-lookup"><span data-stu-id="e721a-148">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="e721a-149">**Nikdy neudělujte oprávnění EXECUTE.**</span><span class="sxs-lookup"><span data-stu-id="e721a-149">**Never grant execute permission.**</span></span>

* <span data-ttu-id="e721a-150">Služba úložiště dat (například [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="e721a-150">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="e721a-151">Služby obvykle nabízejí vylepšenou škálovatelnost a odolnost proti místním řešením, které obvykle podléhají jednomu bodu selhání.</span><span class="sxs-lookup"><span data-stu-id="e721a-151">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="e721a-152">Služby jsou potenciálně nižší náklady ve scénářích infrastruktury velkých úložišť.</span><span class="sxs-lookup"><span data-stu-id="e721a-152">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="e721a-153">Další informace najdete v tématu [rychlý Start: použití .NET k vytvoření objektu BLOB v úložišti objektů](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="e721a-153">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="e721a-154">Scénáře nahrávání souborů</span><span class="sxs-lookup"><span data-stu-id="e721a-154">File upload scenarios</span></span>

<span data-ttu-id="e721a-155">Dva obecné přístupy k nahrávání souborů jsou ukládání do vyrovnávací paměti a streamování.</span><span class="sxs-lookup"><span data-stu-id="e721a-155">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="e721a-156">**Do vyrovnávací paměti**</span><span class="sxs-lookup"><span data-stu-id="e721a-156">**Buffering**</span></span>

<span data-ttu-id="e721a-157">Celý soubor je načten do <xref:Microsoft.AspNetCore.Http.IFormFile> , což je reprezentace souboru, který se používá ke zpracování nebo uložení souboru v jazyce C#.</span><span class="sxs-lookup"><span data-stu-id="e721a-157">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="e721a-158">Prostředky (disk, paměť) používané při nahrávání souborů závisí na počtu a velikosti souběžných nahrávání souborů.</span><span class="sxs-lookup"><span data-stu-id="e721a-158">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="e721a-159">Pokud se aplikace pokusí do vyrovnávací paměti příliš mnoho nahrávání, dojde k selhání lokality, když dojde k vynechání paměti nebo místa na disku.</span><span class="sxs-lookup"><span data-stu-id="e721a-159">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="e721a-160">Pokud velikost nebo frekvence nahrávání souborů vyčerpá prostředky aplikace, použijte streamování.</span><span class="sxs-lookup"><span data-stu-id="e721a-160">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="e721a-161">Z paměti do dočasného souboru na disku se přesune libovolný soubor s vyrovnávací pamětí větší než 64 KB.</span><span class="sxs-lookup"><span data-stu-id="e721a-161">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="e721a-162">Ukládání malých souborů do vyrovnávací paměti je popsáno v následujících částech tohoto tématu:</span><span class="sxs-lookup"><span data-stu-id="e721a-162">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="e721a-163">Fyzické úložiště</span><span class="sxs-lookup"><span data-stu-id="e721a-163">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="e721a-164">Databáze</span><span class="sxs-lookup"><span data-stu-id="e721a-164">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="e721a-165">**Streamování**</span><span class="sxs-lookup"><span data-stu-id="e721a-165">**Streaming**</span></span>

<span data-ttu-id="e721a-166">Soubor se přijímá z požadavku na více částí a přímo se zpracovává nebo ukládá v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e721a-166">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="e721a-167">Streamování nijak významně nezvyšuje výkon.</span><span class="sxs-lookup"><span data-stu-id="e721a-167">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="e721a-168">Streamování snižuje nároky na paměť nebo místo na disku při nahrávání souborů.</span><span class="sxs-lookup"><span data-stu-id="e721a-168">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="e721a-169">Streamování velkých souborů je zahrnuté v části [nahrávání velkých souborů pomocí streamování](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="e721a-169">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="e721a-170">Nahrávání malých souborů s vazbou modelu ve vyrovnávací paměti na fyzické úložiště</span><span class="sxs-lookup"><span data-stu-id="e721a-170">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="e721a-171">Pro nahrání malých souborů použijte formulář s více částmi nebo sestavte požadavek POST pomocí JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="e721a-171">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="e721a-172">Následující příklad ukazuje použití Razor formuláře stránky k nahrání jednoho souboru (*Pages/BufferedSingleFileUploadPhysical. cshtml* do ukázkové aplikace):</span><span class="sxs-lookup"><span data-stu-id="e721a-172">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="e721a-173">Následující příklad je podobný předchozímu příkladu s tím rozdílem, že:</span><span class="sxs-lookup"><span data-stu-id="e721a-173">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="e721a-174">K odeslání dat formuláře se používá JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)).</span><span class="sxs-lookup"><span data-stu-id="e721a-174">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="e721a-175">Neexistuje žádné ověření.</span><span class="sxs-lookup"><span data-stu-id="e721a-175">There's no validation.</span></span>

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

<span data-ttu-id="e721a-176">Chcete-li provést příspěvek formuláře v jazyce JavaScript pro klienty, kteří [nepodporují rozhraní API pro načítání](https://caniuse.com/#feat=fetch), použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="e721a-176">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="e721a-177">Použijte načtenou výplň (například [window. Fetch Fill (GitHub/Fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="e721a-177">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="e721a-178">Použijte `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="e721a-178">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="e721a-179">Příklad:</span><span class="sxs-lookup"><span data-stu-id="e721a-179">For example:</span></span>

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

<span data-ttu-id="e721a-180">Aby bylo možné podporovat nahrávání souborů, musí formuláře HTML určovat typ kódování ( `enctype` ) `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="e721a-180">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="e721a-181">Pro `files` vstupní element, který podporuje nahrávání více souborů, poskytněte `multiple` atribut `<input>` elementu:</span><span class="sxs-lookup"><span data-stu-id="e721a-181">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="e721a-182">Jednotlivé soubory nahrané na server jsou k dispozici prostřednictvím [vazby modelu](xref:mvc/models/model-binding) pomocí <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="e721a-182">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="e721a-183">Ukázková aplikace ukazuje více ukládání souborů do vyrovnávací paměti pro scénáře databáze a fyzických úložišť.</span><span class="sxs-lookup"><span data-stu-id="e721a-183">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="e721a-184">Nepoužívejte **not** `FileName` vlastnost <xref:Microsoft.AspNetCore.Http.IFormFile> jinou než pro zobrazení a protokolování.</span><span class="sxs-lookup"><span data-stu-id="e721a-184">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="e721a-185">Při zobrazení nebo protokolování je název souboru kódován HTML.</span><span class="sxs-lookup"><span data-stu-id="e721a-185">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="e721a-186">Útočník může poskytnout škodlivý název souboru, včetně úplných cest nebo relativních cest.</span><span class="sxs-lookup"><span data-stu-id="e721a-186">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="e721a-187">Aplikace by měly:</span><span class="sxs-lookup"><span data-stu-id="e721a-187">Applications should:</span></span>
>
> * <span data-ttu-id="e721a-188">Odeberte cestu z názvu souboru zadaného uživatelem.</span><span class="sxs-lookup"><span data-stu-id="e721a-188">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="e721a-189">Uložte název souboru s příponou PATH s kódováním HTML pro uživatelské rozhraní nebo protokolování.</span><span class="sxs-lookup"><span data-stu-id="e721a-189">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="e721a-190">Vygenerujte nový náhodný název souboru pro úložiště.</span><span class="sxs-lookup"><span data-stu-id="e721a-190">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="e721a-191">Následující kód odstraní cestu z názvu souboru:</span><span class="sxs-lookup"><span data-stu-id="e721a-191">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="e721a-192">Zde uvedené příklady neberou ohled na zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="e721a-192">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="e721a-193">Další informace jsou k dispozici v následujících částech a [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="e721a-193">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="e721a-194">Důležité informace o zabezpečení</span><span class="sxs-lookup"><span data-stu-id="e721a-194">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="e721a-195">Ověřování</span><span class="sxs-lookup"><span data-stu-id="e721a-195">Validation</span></span>](#validation)

<span data-ttu-id="e721a-196">Při nahrávání souborů pomocí vazby modelu a <xref:Microsoft.AspNetCore.Http.IFormFile> může metoda Action přijmout:</span><span class="sxs-lookup"><span data-stu-id="e721a-196">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="e721a-197">Jedna <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="e721a-197">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="e721a-198">Kterákoli z následujících kolekcí, které reprezentují několik souborů:</span><span class="sxs-lookup"><span data-stu-id="e721a-198">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="e721a-199">[Seznamu](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="e721a-199">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="e721a-200">Vazba odpovídá souborům formuláře podle názvu.</span><span class="sxs-lookup"><span data-stu-id="e721a-200">Binding matches form files by name.</span></span> <span data-ttu-id="e721a-201">Například hodnota HTML v se `name` `<input type="file" name="formFile">` musí shodovat s parametrem nebo vazbou vlastnosti jazyka C# ( `FormFile` ).</span><span class="sxs-lookup"><span data-stu-id="e721a-201">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="e721a-202">Další informace naleznete v části [název atributu matched na název parametru metody post](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="e721a-202">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="e721a-203">Následující příklad:</span><span class="sxs-lookup"><span data-stu-id="e721a-203">The following example:</span></span>

* <span data-ttu-id="e721a-204">Projde jedním nebo více nahranými soubory.</span><span class="sxs-lookup"><span data-stu-id="e721a-204">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="e721a-205">Pomocí [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) vrátí úplnou cestu k souboru, včetně názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="e721a-205">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="e721a-206">Uloží soubory do místního systému souborů pomocí názvu souboru generovaného aplikací.</span><span class="sxs-lookup"><span data-stu-id="e721a-206">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="e721a-207">Vrátí celkový počet nahraných souborů a jejich velikost.</span><span class="sxs-lookup"><span data-stu-id="e721a-207">Returns the total number and size of files uploaded.</span></span>

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

    return Ok(new { count = files.Count, size });
}
```

<span data-ttu-id="e721a-208">Slouží `Path.GetRandomFileName` k vygenerování názvu souboru bez cesty.</span><span class="sxs-lookup"><span data-stu-id="e721a-208">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="e721a-209">V následujícím příkladu je cesta získána z konfigurace:</span><span class="sxs-lookup"><span data-stu-id="e721a-209">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="e721a-210">Cesta předaná do <xref:System.IO.FileStream> *musí* zahrnovat název souboru.</span><span class="sxs-lookup"><span data-stu-id="e721a-210">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="e721a-211">Pokud není zadán název souboru, <xref:System.UnauthorizedAccessException> je vyvolána za běhu.</span><span class="sxs-lookup"><span data-stu-id="e721a-211">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="e721a-212">Soubory odeslané pomocí <xref:Microsoft.AspNetCore.Http.IFormFile> techniky jsou v paměti nebo na disku na serveru před zpracováním uloženy do vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="e721a-212">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="e721a-213">V rámci metody Action je <xref:Microsoft.AspNetCore.Http.IFormFile> obsah přístupný jako <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="e721a-213">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="e721a-214">Kromě místního systému souborů je možné soubory ukládat do síťové sdílené složky nebo do služby úložiště souborů, jako je [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="e721a-214">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="e721a-215">Další příklad, který projde několik souborů pro nahrání a používá bezpečné názvy souborů, najdete v ukázkové aplikaci v části *Pages/BufferedMultipleFileUploadPhysical. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="e721a-215">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="e721a-216">[Cesta. GetTempFileName](xref:System.IO.Path.GetTempFileName*) vyvolá výjimku, <xref:System.IO.IOException> Pokud jsou vytvořeny více než 65 535 souborů bez odstranění předchozích dočasných souborů.</span><span class="sxs-lookup"><span data-stu-id="e721a-216">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="e721a-217">Limit 65 535 souborů je omezen na server.</span><span class="sxs-lookup"><span data-stu-id="e721a-217">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="e721a-218">Další informace o tomto limitu pro operační systém Windows najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="e721a-218">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="e721a-219">GetTempFileNameA – funkce</span><span class="sxs-lookup"><span data-stu-id="e721a-219">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="e721a-220">Nahrávání malých souborů s vazbou modelu s vyrovnávací pamětí do databáze</span><span class="sxs-lookup"><span data-stu-id="e721a-220">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="e721a-221">Chcete-li uložit data binárního souboru do databáze pomocí [Entity Framework](/ef/core/index), definujte v <xref:System.Byte> entitě vlastnost Array:</span><span class="sxs-lookup"><span data-stu-id="e721a-221">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="e721a-222">Zadejte vlastnost modelu stránky pro třídu, která obsahuje <xref:Microsoft.AspNetCore.Http.IFormFile> :</span><span class="sxs-lookup"><span data-stu-id="e721a-222">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="e721a-223"><xref:Microsoft.AspNetCore.Http.IFormFile> lze ji použít přímo jako parametr metody akce nebo jako vlastnost vázaného modelu.</span><span class="sxs-lookup"><span data-stu-id="e721a-223"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="e721a-224">Předchozí příklad používá vlastnost vázaného modelu.</span><span class="sxs-lookup"><span data-stu-id="e721a-224">The prior example uses a bound model property.</span></span>

<span data-ttu-id="e721a-225">`FileUpload`Je použit ve Razor formuláři stránky:</span><span class="sxs-lookup"><span data-stu-id="e721a-225">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="e721a-226">Když je formulář publikovaný na serveru, zkopírujte <xref:Microsoft.AspNetCore.Http.IFormFile> ho do datového proudu a uložte ho jako pole bajtů v databázi.</span><span class="sxs-lookup"><span data-stu-id="e721a-226">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="e721a-227">V následujícím příkladu `_dbContext` ukládá kontext databáze aplikace:</span><span class="sxs-lookup"><span data-stu-id="e721a-227">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="e721a-228">Předchozí příklad je podobný scénáři, který je znázorněný v ukázkové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="e721a-228">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="e721a-229">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="e721a-229">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="e721a-230">*Pages/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="e721a-230">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="e721a-231">Při ukládání binárních dat do relačních databází buďte opatrní, protože to může mít nepříznivý vliv na výkon.</span><span class="sxs-lookup"><span data-stu-id="e721a-231">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="e721a-232">Nespoléhá se na nebo důvěřujete `FileName` vlastnosti <xref:Microsoft.AspNetCore.Http.IFormFile> bez ověření.</span><span class="sxs-lookup"><span data-stu-id="e721a-232">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="e721a-233">`FileName`Vlastnost by měla být použita pouze pro účely zobrazení a pouze po kódování HTML.</span><span class="sxs-lookup"><span data-stu-id="e721a-233">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="e721a-234">Uvedené příklady nevezmou ohled na zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="e721a-234">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="e721a-235">Další informace jsou k dispozici v následujících částech a [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="e721a-235">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="e721a-236">Důležité informace o zabezpečení</span><span class="sxs-lookup"><span data-stu-id="e721a-236">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="e721a-237">Ověřování</span><span class="sxs-lookup"><span data-stu-id="e721a-237">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="e721a-238">Nahrávání velkých souborů pomocí streamování</span><span class="sxs-lookup"><span data-stu-id="e721a-238">Upload large files with streaming</span></span>

<span data-ttu-id="e721a-239">Následující příklad ukazuje, jak použít JavaScript ke streamování souboru do akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="e721a-239">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="e721a-240">Token proti padělání souboru se generuje pomocí vlastního atributu filtru a předává se do hlaviček protokolu HTTP klienta místo v textu žádosti.</span><span class="sxs-lookup"><span data-stu-id="e721a-240">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="e721a-241">Vzhledem k tomu, že metoda akce zpracovává nahraná data přímo, vazba modelu formuláře je zakázána jiným vlastním filtrem.</span><span class="sxs-lookup"><span data-stu-id="e721a-241">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="e721a-242">V rámci akce je obsah formuláře čten pomocí `MultipartReader` , který čte každou jednotlivou osobu `MultipartSection` , zpracovává soubor nebo ukládá obsah podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="e721a-242">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="e721a-243">Po načtení oddílů s více částmi provede akce vlastní vazbu modelu.</span><span class="sxs-lookup"><span data-stu-id="e721a-243">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="e721a-244">Počáteční odpověď stránky načte formulář a uloží token proti padělání do cookie (prostřednictvím `GenerateAntiforgeryTokenCookieAttribute` atributu).</span><span class="sxs-lookup"><span data-stu-id="e721a-244">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="e721a-245">Atribut používá ASP.NET Core integrovanou [podporu proti padělání](xref:security/anti-request-forgery) pro nastavení cookie s tokenem požadavku:</span><span class="sxs-lookup"><span data-stu-id="e721a-245">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="e721a-246">`DisableFormValueModelBindingAttribute`Slouží k zakázání vazby modelu:</span><span class="sxs-lookup"><span data-stu-id="e721a-246">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="e721a-247">V ukázkové aplikaci a používá `GenerateAntiforgeryTokenCookieAttribute` se `DisableFormValueModelBindingAttribute` jako filtr pro modelové aplikace stránky `/StreamedSingleFileUploadDb` a `/StreamedSingleFileUploadPhysical` v `Startup.ConfigureServices` [ Razor konvencích použití stránek](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="e721a-247">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

<span data-ttu-id="e721a-248">Vzhledem k tomu, že vazba modelu nepřečte formulář, parametry, které jsou svázané z formuláře, se nezobrazují (budou pokračovat v práci s dotazem, trasou a hlavičkou).</span><span class="sxs-lookup"><span data-stu-id="e721a-248">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="e721a-249">Metoda Action pracuje přímo s `Request` vlastností.</span><span class="sxs-lookup"><span data-stu-id="e721a-249">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="e721a-250">`MultipartReader`Slouží ke čtení jednotlivých oddílů.</span><span class="sxs-lookup"><span data-stu-id="e721a-250">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="e721a-251">Data klíč/hodnota jsou uložena v `KeyValueAccumulator` .</span><span class="sxs-lookup"><span data-stu-id="e721a-251">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="e721a-252">Po načtení oddílů s více částmi se obsah `KeyValueAccumulator` používá pro svázání dat formuláře s typem modelu.</span><span class="sxs-lookup"><span data-stu-id="e721a-252">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="e721a-253">Úplná `StreamingController.UploadDatabase` metoda pro streamování do databáze s EF Core:</span><span class="sxs-lookup"><span data-stu-id="e721a-253">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="e721a-254">`MultipartRequestHelper` (*Nástroje/MultipartRequestHelper. cs*):</span><span class="sxs-lookup"><span data-stu-id="e721a-254">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="e721a-255">Úplná `StreamingController.UploadPhysical` metoda pro streamování do fyzického umístění:</span><span class="sxs-lookup"><span data-stu-id="e721a-255">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="e721a-256">V ukázkové aplikaci jsou kontroly ověřování zpracovávány nástrojem `FileHelpers.ProcessStreamedFile` .</span><span class="sxs-lookup"><span data-stu-id="e721a-256">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="e721a-257">Ověřování</span><span class="sxs-lookup"><span data-stu-id="e721a-257">Validation</span></span>

<span data-ttu-id="e721a-258">Třída ukázkové aplikace `FileHelpers` ukazuje několik kontrol ukládání souborů do vyrovnávací paměti <xref:Microsoft.AspNetCore.Http.IFormFile> a datových proudů při nahrávání.</span><span class="sxs-lookup"><span data-stu-id="e721a-258">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="e721a-259">Informace o zpracování <xref:Microsoft.AspNetCore.Http.IFormFile> ukládání souborů do vyrovnávací paměti v ukázkové aplikaci naleznete v `ProcessFormFile` metodě v souboru *Utilities/Helper. cs* .</span><span class="sxs-lookup"><span data-stu-id="e721a-259">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="e721a-260">Pro zpracování streamované soubory se podívejte na `ProcessStreamedFile` metodu ve stejném souboru.</span><span class="sxs-lookup"><span data-stu-id="e721a-260">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="e721a-261">Metody zpracování ověřování, které jsou znázorněné v ukázkové aplikaci, nekontrolují obsah nahraných souborů.</span><span class="sxs-lookup"><span data-stu-id="e721a-261">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="e721a-262">Ve většině produkčních scénářů se v souboru používá rozhraní API pro skenování virů nebo malwaru, než je soubor dostupný uživatelům nebo jiným systémům.</span><span class="sxs-lookup"><span data-stu-id="e721a-262">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="e721a-263">I když ukázka tématu poskytuje pracovní příklad technik ověřování, Neimplementujte `FileHelpers` třídu v produkční aplikaci, pokud:</span><span class="sxs-lookup"><span data-stu-id="e721a-263">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="e721a-264">Plně rozumíte implementaci.</span><span class="sxs-lookup"><span data-stu-id="e721a-264">Fully understand the implementation.</span></span>
> * <span data-ttu-id="e721a-265">Upravte implementaci podle potřeby pro prostředí a specifikace aplikace.</span><span class="sxs-lookup"><span data-stu-id="e721a-265">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="e721a-266">**Nikdy nepoužívejte nerozlišený kód zabezpečení v aplikaci bez nutnosti řešit tyto požadavky.**</span><span class="sxs-lookup"><span data-stu-id="e721a-266">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="e721a-267">Ověření obsahu</span><span class="sxs-lookup"><span data-stu-id="e721a-267">Content validation</span></span>

<span data-ttu-id="e721a-268">**Pro nahraný obsah použijte rozhraní API pro kontrolu virů a malwaru třetí strany.**</span><span class="sxs-lookup"><span data-stu-id="e721a-268">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="e721a-269">Prohledávání souborů je náročné na prostředky serveru ve scénářích s vysokým objemem.</span><span class="sxs-lookup"><span data-stu-id="e721a-269">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="e721a-270">Pokud dojde ke snížení výkonu zpracování požadavků z důvodu kontroly souborů, zvažte přesměrování práce skenování na službu na [pozadí](xref:fundamentals/host/hosted-services), případně služby spuštěné na serveru, který se liší od serveru aplikace.</span><span class="sxs-lookup"><span data-stu-id="e721a-270">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="e721a-271">Nahrané soubory se obvykle uchovávají v oblasti v karanténě, dokud je kontrola virů na pozadí nevrátí.</span><span class="sxs-lookup"><span data-stu-id="e721a-271">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="e721a-272">Když soubor projde, soubor se přesune do normálního umístění úložiště souborů.</span><span class="sxs-lookup"><span data-stu-id="e721a-272">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="e721a-273">Tyto kroky se obvykle provádí ve spojení s databázovým záznamem, který indikuje stav kontroly souboru.</span><span class="sxs-lookup"><span data-stu-id="e721a-273">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="e721a-274">Při použití takového přístupu zůstane aplikace a Server App zaměřené na reakci na požadavky.</span><span class="sxs-lookup"><span data-stu-id="e721a-274">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="e721a-275">Ověření přípony souboru</span><span class="sxs-lookup"><span data-stu-id="e721a-275">File extension validation</span></span>

<span data-ttu-id="e721a-276">Přípona nahraného souboru by měla být zaškrtnutá na seznamu povolených rozšíření.</span><span class="sxs-lookup"><span data-stu-id="e721a-276">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="e721a-277">Příklad:</span><span class="sxs-lookup"><span data-stu-id="e721a-277">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="e721a-278">Ověření podpisu souboru</span><span class="sxs-lookup"><span data-stu-id="e721a-278">File signature validation</span></span>

<span data-ttu-id="e721a-279">Podpis souboru se určuje na prvních několika bajtech na začátku souboru.</span><span class="sxs-lookup"><span data-stu-id="e721a-279">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="e721a-280">Tyto bajty lze použít k určení, zda přípona odpovídá obsahu souboru.</span><span class="sxs-lookup"><span data-stu-id="e721a-280">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="e721a-281">Ukázková aplikace zkontroluje podpisy souborů pro několik běžných typů souborů.</span><span class="sxs-lookup"><span data-stu-id="e721a-281">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="e721a-282">V následujícím příkladu se pro tento soubor kontroluje podpis souboru obrázku JPEG:</span><span class="sxs-lookup"><span data-stu-id="e721a-282">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="e721a-283">Další signatury souborů získáte v dokumentaci [signatury souborů](https://www.filesignatures.net/) a oficiálních souborů.</span><span class="sxs-lookup"><span data-stu-id="e721a-283">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="e721a-284">Zabezpečení názvu souboru</span><span class="sxs-lookup"><span data-stu-id="e721a-284">File name security</span></span>

<span data-ttu-id="e721a-285">Nikdy nepoužívejte název souboru dodaný klientem pro uložení souboru do fyzického úložiště.</span><span class="sxs-lookup"><span data-stu-id="e721a-285">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="e721a-286">Vytvořte bezpečný název souboru pro soubor pomocí [cesty. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) nebo [cesty. GetTempFileName](xref:System.IO.Path.GetTempFileName*) a vytvořte úplnou cestu (včetně názvu souboru) pro dočasné úložiště.</span><span class="sxs-lookup"><span data-stu-id="e721a-286">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="e721a-287">Razor Automatické kódování HTML kóduje hodnoty vlastností pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="e721a-287">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="e721a-288">Následující kód je bezpečné použít:</span><span class="sxs-lookup"><span data-stu-id="e721a-288">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="e721a-289">Mimo Razor , vždy <xref:System.Net.WebUtility.HtmlEncode*> obsah názvu souboru z požadavku uživatele.</span><span class="sxs-lookup"><span data-stu-id="e721a-289">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="e721a-290">Mnoho implementací musí zahrnovat kontrolu, že soubor existuje. v opačném případě je soubor přepsán souborem se stejným názvem.</span><span class="sxs-lookup"><span data-stu-id="e721a-290">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="e721a-291">Poskytněte další logiku pro splnění specifikací vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="e721a-291">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="e721a-292">Ověřování velikosti</span><span class="sxs-lookup"><span data-stu-id="e721a-292">Size validation</span></span>

<span data-ttu-id="e721a-293">Omezte velikost nahraných souborů.</span><span class="sxs-lookup"><span data-stu-id="e721a-293">Limit the size of uploaded files.</span></span>

<span data-ttu-id="e721a-294">V ukázkové aplikaci je velikost souboru omezená na 2 MB (uvedené v bajtech).</span><span class="sxs-lookup"><span data-stu-id="e721a-294">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="e721a-295">Limit se poskytuje prostřednictvím [Konfigurace](xref:fundamentals/configuration/index) z *appsettings.jsv* souboru:</span><span class="sxs-lookup"><span data-stu-id="e721a-295">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="e721a-296">`FileSizeLimit`Třída je vložena do `PageModel` tříd:</span><span class="sxs-lookup"><span data-stu-id="e721a-296">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="e721a-297">Když velikost souboru překročí limit, soubor se odmítne:</span><span class="sxs-lookup"><span data-stu-id="e721a-297">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="e721a-298">Porovnává hodnotu atributu name s parametrem název metody POST</span><span class="sxs-lookup"><span data-stu-id="e721a-298">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="e721a-299">V Razor neformulářích, které publikují data formuláře nebo přímo využívají JavaScript `FormData` , název zadaný v prvku formuláře nebo `FormData` musí odpovídat názvu parametru v akci kontroleru.</span><span class="sxs-lookup"><span data-stu-id="e721a-299">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="e721a-300">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="e721a-300">In the following example:</span></span>

* <span data-ttu-id="e721a-301">Při použití `<input>` elementu `name` je atribut nastaven na hodnotu `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="e721a-301">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="e721a-302">Při použití `FormData` v jazyce JavaScript je název nastaven na hodnotu `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="e721a-302">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="e721a-303">Pro parametr metody jazyka C# () použijte stejný název `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="e721a-303">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="e721a-304">Pro Razor metodu obslužné rutiny stránky stránky s názvem `Upload` :</span><span class="sxs-lookup"><span data-stu-id="e721a-304">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="e721a-305">Pro metodu akce po kontroléru MVC:</span><span class="sxs-lookup"><span data-stu-id="e721a-305">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="e721a-306">Konfigurace serveru a aplikace</span><span class="sxs-lookup"><span data-stu-id="e721a-306">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="e721a-307">Omezení délky těla částí</span><span class="sxs-lookup"><span data-stu-id="e721a-307">Multipart body length limit</span></span>

<span data-ttu-id="e721a-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> nastaví limit délky jednotlivých částí části.</span><span class="sxs-lookup"><span data-stu-id="e721a-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="e721a-309">Oddíly formuláře, které překračují toto omezení, vyvolávají <xref:System.IO.InvalidDataException> při analýze.</span><span class="sxs-lookup"><span data-stu-id="e721a-309">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="e721a-310">Výchozí hodnota je 134 217 728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="e721a-310">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="e721a-311">Upravte limit pomocí <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> nastavení v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e721a-311">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="e721a-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> slouží k nastavení <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> pro jednu stránku nebo akci.</span><span class="sxs-lookup"><span data-stu-id="e721a-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="e721a-313">V Razor aplikaci Pages použijte filtr s [konvencí](xref:razor-pages/razor-pages-conventions) v nástroji `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e721a-313">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages(options =>
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

<span data-ttu-id="e721a-314">V Razor aplikaci Pages nebo aplikaci MVC použijte filtr na model stránky nebo metodu akce:</span><span class="sxs-lookup"><span data-stu-id="e721a-314">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="e721a-315">Kestrel maximální velikost textu požadavku</span><span class="sxs-lookup"><span data-stu-id="e721a-315">Kestrel maximum request body size</span></span>

<span data-ttu-id="e721a-316">Pro aplikace hostované v Kestrel je výchozí maximální velikost textu požadavku 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="e721a-316">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="e721a-317">Přizpůsobte limit pomocí možnosti serveru [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:</span><span class="sxs-lookup"><span data-stu-id="e721a-317">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel((context, options) =>
            {
                // Handle requests up to 50 MB
                options.Limits.MaxRequestBodySize = 52428800;
            })
            .UseStartup<Startup>();
        });
```

<span data-ttu-id="e721a-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> slouží k nastavení [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) pro jednu stránku nebo akci.</span><span class="sxs-lookup"><span data-stu-id="e721a-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="e721a-319">V Razor aplikaci Pages použijte filtr s [konvencí](xref:razor-pages/razor-pages-conventions) v nástroji `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e721a-319">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages(options =>
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

<span data-ttu-id="e721a-320">V Razor aplikaci Pages nebo aplikaci MVC použijte filtr na třídu obslužné rutiny stránky nebo na metodu akce:</span><span class="sxs-lookup"><span data-stu-id="e721a-320">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="e721a-321">`RequestSizeLimitAttribute`Lze také použít pomocí [`@attribute`](xref:mvc/views/razor#attribute) Razor direktivy:</span><span class="sxs-lookup"><span data-stu-id="e721a-321">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="e721a-322">Další omezení Kestrel</span><span class="sxs-lookup"><span data-stu-id="e721a-322">Other Kestrel limits</span></span>

<span data-ttu-id="e721a-323">Pro aplikace hostované v Kestrel se můžou vztahovat další omezení Kestrel:</span><span class="sxs-lookup"><span data-stu-id="e721a-323">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="e721a-324">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="e721a-324">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="e721a-325">Sazby za data požadavků a odpovědí</span><span class="sxs-lookup"><span data-stu-id="e721a-325">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a><span data-ttu-id="e721a-326">IIS</span><span class="sxs-lookup"><span data-stu-id="e721a-326">IIS</span></span>

<span data-ttu-id="e721a-327">Výchozí limit počtu požadavků ( `maxAllowedContentLength` ) je 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="e721a-327">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="e721a-328">Přizpůsobte limit v `web.config` souboru.</span><span class="sxs-lookup"><span data-stu-id="e721a-328">Customize the limit in the `web.config` file.</span></span> <span data-ttu-id="e721a-329">V následujícím příkladu je limit nastaven na 50 MB (52 428 800 bajtů):</span><span class="sxs-lookup"><span data-stu-id="e721a-329">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="e721a-330">`maxAllowedContentLength`Nastavení platí pouze pro službu IIS.</span><span class="sxs-lookup"><span data-stu-id="e721a-330">The `maxAllowedContentLength` setting only applies to IIS.</span></span> <span data-ttu-id="e721a-331">Další informace najdete v tématu [omezení `<requestLimits>` požadavků ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="e721a-331">For more information, see [Request Limits `<requestLimits>`](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="e721a-332">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="e721a-332">Troubleshoot</span></span>

<span data-ttu-id="e721a-333">Níže jsou uvedeny některé běžné problémy, které se vyskytly při práci s nahráváním souborů a jejich možnými řešeními.</span><span class="sxs-lookup"><span data-stu-id="e721a-333">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="e721a-334">Při nasazení na server služby IIS se nenašla chyba.</span><span class="sxs-lookup"><span data-stu-id="e721a-334">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="e721a-335">Následující chyba znamená, že nahraný soubor překračuje délku nakonfigurovaného obsahu serveru:</span><span class="sxs-lookup"><span data-stu-id="e721a-335">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="e721a-336">Další informace najdete v části [IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="e721a-336">For more information, see the [IIS](#iis) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="e721a-337">Chyba připojení</span><span class="sxs-lookup"><span data-stu-id="e721a-337">Connection failure</span></span>

<span data-ttu-id="e721a-338">Chyba připojení a připojení k serveru pro resetování pravděpodobně znamená, že nahraný soubor překračuje maximální velikost textu požadavku Kestrel.</span><span class="sxs-lookup"><span data-stu-id="e721a-338">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="e721a-339">Další informace najdete v části [Kestrel maximální velikost textu požadavku](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="e721a-339">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="e721a-340">Omezení připojení klientů Kestrel mohou také vyžadovat úpravu.</span><span class="sxs-lookup"><span data-stu-id="e721a-340">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="e721a-341">Výjimka odkazu s hodnotou null s IFormFile</span><span class="sxs-lookup"><span data-stu-id="e721a-341">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="e721a-342">Pokud kontroler přijímá odeslané soubory pomocí <xref:Microsoft.AspNetCore.Http.IFormFile> , ale hodnota je `null` , potvrďte, že formulář HTML určuje `enctype` hodnotu `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="e721a-342">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="e721a-343">Pokud tento atribut není nastaven na `<form>` elementu, odeslání souboru neproběhne a jakékoli vázané <xref:Microsoft.AspNetCore.Http.IFormFile> argumenty jsou `null` .</span><span class="sxs-lookup"><span data-stu-id="e721a-343">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="e721a-344">Ujistěte se také, že [nahrávání názvů v datech formuláře odpovídá pojmenování aplikace](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="e721a-344">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="e721a-345">Proud je příliš dlouhý.</span><span class="sxs-lookup"><span data-stu-id="e721a-345">Stream was too long</span></span>

<span data-ttu-id="e721a-346">Příklady v tomto tématu <xref:System.IO.MemoryStream> se spoléhají na uchovávání obsahu nahraného souboru.</span><span class="sxs-lookup"><span data-stu-id="e721a-346">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="e721a-347">Omezení velikosti `MemoryStream` je `int.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="e721a-347">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="e721a-348">Pokud scénář nahrávání souborů aplikace vyžaduje, aby byl obsah souboru větší než 50 MB, použijte alternativní přístup, který nespoléhá na jednu z nich `MemoryStream` pro uchovávání obsahu nahraného souboru.</span><span class="sxs-lookup"><span data-stu-id="e721a-348">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="e721a-349">ASP.NET Core podporuje nahrávání jednoho nebo více souborů pomocí vazby modelu ve vyrovnávací paměti pro menší soubory a streamování bez vyrovnávací paměti pro větší soubory.</span><span class="sxs-lookup"><span data-stu-id="e721a-349">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="e721a-350">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e721a-350">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="e721a-351">Důležité informace o zabezpečení</span><span class="sxs-lookup"><span data-stu-id="e721a-351">Security considerations</span></span>

<span data-ttu-id="e721a-352">Pokud chcete uživatelům poskytnout možnost nahrávat soubory na server, buďte opatrní.</span><span class="sxs-lookup"><span data-stu-id="e721a-352">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="e721a-353">Útočníci se můžou pokusit:</span><span class="sxs-lookup"><span data-stu-id="e721a-353">Attackers may attempt to:</span></span>

* <span data-ttu-id="e721a-354">Vykoná útok [DOS (Denial of Service](/windows-hardware/drivers/ifs/denial-of-service) ).</span><span class="sxs-lookup"><span data-stu-id="e721a-354">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="e721a-355">Nahrání virů nebo malwaru</span><span class="sxs-lookup"><span data-stu-id="e721a-355">Upload viruses or malware.</span></span>
* <span data-ttu-id="e721a-356">Narušit sítě a servery jinými způsoby.</span><span class="sxs-lookup"><span data-stu-id="e721a-356">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="e721a-357">Bezpečnostní kroky, které snižují pravděpodobnost úspěšného útoku, jsou:</span><span class="sxs-lookup"><span data-stu-id="e721a-357">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="e721a-358">Nahrajte soubory do vyhrazené oblasti pro nahrávání souborů, nejlépe do nesystémové jednotky.</span><span class="sxs-lookup"><span data-stu-id="e721a-358">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="e721a-359">Vyhrazené umístění usnadňuje omezení zabezpečení pro nahrané soubory.</span><span class="sxs-lookup"><span data-stu-id="e721a-359">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="e721a-360">Zakažte oprávnění EXECUTE pro umístění pro nahrání souboru.&dagger;</span><span class="sxs-lookup"><span data-stu-id="e721a-360">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="e721a-361">Neuchovávat nahrané soubory ve stejném stromu adresářů jako aplikace. **not**&dagger;</span><span class="sxs-lookup"><span data-stu-id="e721a-361">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="e721a-362">Použijte název bezpečného souboru určený aplikací.</span><span class="sxs-lookup"><span data-stu-id="e721a-362">Use a safe file name determined by the app.</span></span> <span data-ttu-id="e721a-363">Nepoužívejte název souboru poskytnutý uživatelem nebo nedůvěryhodného názvu nahraného souboru. &dagger; HTML při zobrazení kódování názvu nedůvěryhodného souboru.</span><span class="sxs-lookup"><span data-stu-id="e721a-363">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="e721a-364">Například protokolování názvu souboru nebo zobrazení v uživatelském rozhraní ( Razor Automatické kódování HTML kódování).</span><span class="sxs-lookup"><span data-stu-id="e721a-364">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="e721a-365">Povolte pro specifikaci návrhu aplikace jenom schválené přípony souborů.&dagger;</span><span class="sxs-lookup"><span data-stu-id="e721a-365">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="e721a-366">Ověřte, zda jsou na serveru provedeny kontroly na straně klienta. &dagger; Kontroly na straně klienta je snadné obejít.</span><span class="sxs-lookup"><span data-stu-id="e721a-366">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="e721a-367">Ověřte velikost nahraného souboru.</span><span class="sxs-lookup"><span data-stu-id="e721a-367">Check the size of an uploaded file.</span></span> <span data-ttu-id="e721a-368">Nastavte limit maximální velikosti, aby se zabránilo velkým nahrávání.&dagger;</span><span class="sxs-lookup"><span data-stu-id="e721a-368">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="e721a-369">Pokud by soubory neměly být přepsány nahraným souborem se stejným názvem, před nahráním souboru ověřte název souboru proti databázi nebo fyzickému úložišti.</span><span class="sxs-lookup"><span data-stu-id="e721a-369">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="e721a-370">**Před uložením souboru spusťte v nahraném obsahu skener virů nebo malwaru.**</span><span class="sxs-lookup"><span data-stu-id="e721a-370">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="e721a-371">&dagger;Ukázková aplikace předvádí přístup, který splňuje kritéria.</span><span class="sxs-lookup"><span data-stu-id="e721a-371">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="e721a-372">Nahrávání škodlivého kódu do systému je často prvním krokem ke spuštění kódu, který může:</span><span class="sxs-lookup"><span data-stu-id="e721a-372">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="e721a-373">Zcela získá kontrolu nad systémem.</span><span class="sxs-lookup"><span data-stu-id="e721a-373">Completely gain control of a system.</span></span>
> * <span data-ttu-id="e721a-374">Přetížit systém s výsledkem, že dojde k chybě systému.</span><span class="sxs-lookup"><span data-stu-id="e721a-374">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="e721a-375">Napadnout data uživatelů nebo systémových dat.</span><span class="sxs-lookup"><span data-stu-id="e721a-375">Compromise user or system data.</span></span>
> * <span data-ttu-id="e721a-376">Použijte graffiti pro veřejné uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e721a-376">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="e721a-377">Informace o omezení oblasti útoku při přijímání souborů uživateli najdete v následujících zdrojích informací:</span><span class="sxs-lookup"><span data-stu-id="e721a-377">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="e721a-378">Neomezená nahrávání souboru</span><span class="sxs-lookup"><span data-stu-id="e721a-378">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="e721a-379">Zabezpečení Azure: Ujistěte se, že jsou při přijímání souborů od uživatelů k dismístě správné ovládací prvky.</span><span class="sxs-lookup"><span data-stu-id="e721a-379">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="e721a-380">Další informace o implementaci bezpečnostních opatření, včetně příkladů z ukázkové aplikace, najdete v části [ověření](#validation) .</span><span class="sxs-lookup"><span data-stu-id="e721a-380">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="e721a-381">Scénáře úložiště</span><span class="sxs-lookup"><span data-stu-id="e721a-381">Storage scenarios</span></span>

<span data-ttu-id="e721a-382">Mezi běžné možnosti úložiště pro soubory patří:</span><span class="sxs-lookup"><span data-stu-id="e721a-382">Common storage options for files include:</span></span>

* <span data-ttu-id="e721a-383">Databáze</span><span class="sxs-lookup"><span data-stu-id="e721a-383">Database</span></span>

  * <span data-ttu-id="e721a-384">U malých nahrávání souborů je databáze často rychlejší než možnosti fyzického úložiště (systému souborů nebo síťového sdílení).</span><span class="sxs-lookup"><span data-stu-id="e721a-384">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="e721a-385">Databáze je často pohodlnější než možnosti fyzického úložiště, protože načtení záznamu databáze pro uživatelská data může současně poskytovat obsah souboru (například obrázek miniatury).</span><span class="sxs-lookup"><span data-stu-id="e721a-385">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="e721a-386">Databáze je potenciálně levnější než použití služby úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="e721a-386">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="e721a-387">Fyzické úložiště (systém souborů nebo síťová sdílená složka)</span><span class="sxs-lookup"><span data-stu-id="e721a-387">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="e721a-388">Pro nahrávání velkých souborů:</span><span class="sxs-lookup"><span data-stu-id="e721a-388">For large file uploads:</span></span>
    * <span data-ttu-id="e721a-389">Omezení databáze mohou omezit velikost nahrávání.</span><span class="sxs-lookup"><span data-stu-id="e721a-389">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="e721a-390">Fyzické úložiště je často méně hospodárné než úložiště v databázi.</span><span class="sxs-lookup"><span data-stu-id="e721a-390">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="e721a-391">Fyzické úložiště je potenciálně levnější než použití služby úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="e721a-391">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="e721a-392">Proces aplikace musí mít oprávnění ke čtení a zápisu do umístění úložiště.</span><span class="sxs-lookup"><span data-stu-id="e721a-392">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="e721a-393">**Nikdy neudělujte oprávnění EXECUTE.**</span><span class="sxs-lookup"><span data-stu-id="e721a-393">**Never grant execute permission.**</span></span>

* <span data-ttu-id="e721a-394">Služba úložiště dat (například [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="e721a-394">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="e721a-395">Služby obvykle nabízejí vylepšenou škálovatelnost a odolnost proti místním řešením, které obvykle podléhají jednomu bodu selhání.</span><span class="sxs-lookup"><span data-stu-id="e721a-395">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="e721a-396">Služby jsou potenciálně nižší náklady ve scénářích infrastruktury velkých úložišť.</span><span class="sxs-lookup"><span data-stu-id="e721a-396">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="e721a-397">Další informace najdete v tématu [rychlý Start: použití .NET k vytvoření objektu BLOB v úložišti objektů](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="e721a-397">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="e721a-398">Scénáře nahrávání souborů</span><span class="sxs-lookup"><span data-stu-id="e721a-398">File upload scenarios</span></span>

<span data-ttu-id="e721a-399">Dva obecné přístupy k nahrávání souborů jsou ukládání do vyrovnávací paměti a streamování.</span><span class="sxs-lookup"><span data-stu-id="e721a-399">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="e721a-400">**Do vyrovnávací paměti**</span><span class="sxs-lookup"><span data-stu-id="e721a-400">**Buffering**</span></span>

<span data-ttu-id="e721a-401">Celý soubor je načten do <xref:Microsoft.AspNetCore.Http.IFormFile> , což je reprezentace souboru, který se používá ke zpracování nebo uložení souboru v jazyce C#.</span><span class="sxs-lookup"><span data-stu-id="e721a-401">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="e721a-402">Prostředky (disk, paměť) používané při nahrávání souborů závisí na počtu a velikosti souběžných nahrávání souborů.</span><span class="sxs-lookup"><span data-stu-id="e721a-402">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="e721a-403">Pokud se aplikace pokusí do vyrovnávací paměti příliš mnoho nahrávání, dojde k selhání lokality, když dojde k vynechání paměti nebo místa na disku.</span><span class="sxs-lookup"><span data-stu-id="e721a-403">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="e721a-404">Pokud velikost nebo frekvence nahrávání souborů vyčerpá prostředky aplikace, použijte streamování.</span><span class="sxs-lookup"><span data-stu-id="e721a-404">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="e721a-405">Z paměti do dočasného souboru na disku se přesune libovolný soubor s vyrovnávací pamětí větší než 64 KB.</span><span class="sxs-lookup"><span data-stu-id="e721a-405">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="e721a-406">Ukládání malých souborů do vyrovnávací paměti je popsáno v následujících částech tohoto tématu:</span><span class="sxs-lookup"><span data-stu-id="e721a-406">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="e721a-407">Fyzické úložiště</span><span class="sxs-lookup"><span data-stu-id="e721a-407">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="e721a-408">Databáze</span><span class="sxs-lookup"><span data-stu-id="e721a-408">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="e721a-409">**Streamování**</span><span class="sxs-lookup"><span data-stu-id="e721a-409">**Streaming**</span></span>

<span data-ttu-id="e721a-410">Soubor se přijímá z požadavku na více částí a přímo se zpracovává nebo ukládá v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e721a-410">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="e721a-411">Streamování nijak významně nezvyšuje výkon.</span><span class="sxs-lookup"><span data-stu-id="e721a-411">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="e721a-412">Streamování snižuje nároky na paměť nebo místo na disku při nahrávání souborů.</span><span class="sxs-lookup"><span data-stu-id="e721a-412">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="e721a-413">Streamování velkých souborů je zahrnuté v části [nahrávání velkých souborů pomocí streamování](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="e721a-413">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="e721a-414">Nahrávání malých souborů s vazbou modelu ve vyrovnávací paměti na fyzické úložiště</span><span class="sxs-lookup"><span data-stu-id="e721a-414">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="e721a-415">Pro nahrání malých souborů použijte formulář s více částmi nebo sestavte požadavek POST pomocí JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="e721a-415">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="e721a-416">Následující příklad ukazuje použití Razor formuláře stránky k nahrání jednoho souboru (*Pages/BufferedSingleFileUploadPhysical. cshtml* do ukázkové aplikace):</span><span class="sxs-lookup"><span data-stu-id="e721a-416">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="e721a-417">Následující příklad je podobný předchozímu příkladu s tím rozdílem, že:</span><span class="sxs-lookup"><span data-stu-id="e721a-417">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="e721a-418">K odeslání dat formuláře se používá JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)).</span><span class="sxs-lookup"><span data-stu-id="e721a-418">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="e721a-419">Neexistuje žádné ověření.</span><span class="sxs-lookup"><span data-stu-id="e721a-419">There's no validation.</span></span>

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

<span data-ttu-id="e721a-420">Chcete-li provést příspěvek formuláře v jazyce JavaScript pro klienty, kteří [nepodporují rozhraní API pro načítání](https://caniuse.com/#feat=fetch), použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="e721a-420">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="e721a-421">Použijte načtenou výplň (například [window. Fetch Fill (GitHub/Fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="e721a-421">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="e721a-422">Použijte `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="e721a-422">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="e721a-423">Příklad:</span><span class="sxs-lookup"><span data-stu-id="e721a-423">For example:</span></span>

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

<span data-ttu-id="e721a-424">Aby bylo možné podporovat nahrávání souborů, musí formuláře HTML určovat typ kódování ( `enctype` ) `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="e721a-424">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="e721a-425">Pro `files` vstupní element, který podporuje nahrávání více souborů, poskytněte `multiple` atribut `<input>` elementu:</span><span class="sxs-lookup"><span data-stu-id="e721a-425">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="e721a-426">Jednotlivé soubory nahrané na server jsou k dispozici prostřednictvím [vazby modelu](xref:mvc/models/model-binding) pomocí <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="e721a-426">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="e721a-427">Ukázková aplikace ukazuje více ukládání souborů do vyrovnávací paměti pro scénáře databáze a fyzických úložišť.</span><span class="sxs-lookup"><span data-stu-id="e721a-427">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="e721a-428">Nepoužívejte **not** `FileName` vlastnost <xref:Microsoft.AspNetCore.Http.IFormFile> jinou než pro zobrazení a protokolování.</span><span class="sxs-lookup"><span data-stu-id="e721a-428">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="e721a-429">Při zobrazení nebo protokolování je název souboru kódován HTML.</span><span class="sxs-lookup"><span data-stu-id="e721a-429">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="e721a-430">Útočník může poskytnout škodlivý název souboru, včetně úplných cest nebo relativních cest.</span><span class="sxs-lookup"><span data-stu-id="e721a-430">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="e721a-431">Aplikace by měly:</span><span class="sxs-lookup"><span data-stu-id="e721a-431">Applications should:</span></span>
>
> * <span data-ttu-id="e721a-432">Odeberte cestu z názvu souboru zadaného uživatelem.</span><span class="sxs-lookup"><span data-stu-id="e721a-432">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="e721a-433">Uložte název souboru s příponou PATH s kódováním HTML pro uživatelské rozhraní nebo protokolování.</span><span class="sxs-lookup"><span data-stu-id="e721a-433">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="e721a-434">Vygenerujte nový náhodný název souboru pro úložiště.</span><span class="sxs-lookup"><span data-stu-id="e721a-434">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="e721a-435">Následující kód odstraní cestu z názvu souboru:</span><span class="sxs-lookup"><span data-stu-id="e721a-435">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="e721a-436">Zde uvedené příklady neberou ohled na zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="e721a-436">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="e721a-437">Další informace jsou k dispozici v následujících částech a [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="e721a-437">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="e721a-438">Důležité informace o zabezpečení</span><span class="sxs-lookup"><span data-stu-id="e721a-438">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="e721a-439">Ověřování</span><span class="sxs-lookup"><span data-stu-id="e721a-439">Validation</span></span>](#validation)

<span data-ttu-id="e721a-440">Při nahrávání souborů pomocí vazby modelu a <xref:Microsoft.AspNetCore.Http.IFormFile> může metoda Action přijmout:</span><span class="sxs-lookup"><span data-stu-id="e721a-440">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="e721a-441">Jedna <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="e721a-441">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="e721a-442">Kterákoli z následujících kolekcí, které reprezentují několik souborů:</span><span class="sxs-lookup"><span data-stu-id="e721a-442">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="e721a-443">[Seznamu](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="e721a-443">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="e721a-444">Vazba odpovídá souborům formuláře podle názvu.</span><span class="sxs-lookup"><span data-stu-id="e721a-444">Binding matches form files by name.</span></span> <span data-ttu-id="e721a-445">Například hodnota HTML v se `name` `<input type="file" name="formFile">` musí shodovat s parametrem nebo vazbou vlastnosti jazyka C# ( `FormFile` ).</span><span class="sxs-lookup"><span data-stu-id="e721a-445">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="e721a-446">Další informace naleznete v části [název atributu matched na název parametru metody post](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="e721a-446">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="e721a-447">Následující příklad:</span><span class="sxs-lookup"><span data-stu-id="e721a-447">The following example:</span></span>

* <span data-ttu-id="e721a-448">Projde jedním nebo více nahranými soubory.</span><span class="sxs-lookup"><span data-stu-id="e721a-448">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="e721a-449">Pomocí [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) vrátí úplnou cestu k souboru, včetně názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="e721a-449">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="e721a-450">Uloží soubory do místního systému souborů pomocí názvu souboru generovaného aplikací.</span><span class="sxs-lookup"><span data-stu-id="e721a-450">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="e721a-451">Vrátí celkový počet nahraných souborů a jejich velikost.</span><span class="sxs-lookup"><span data-stu-id="e721a-451">Returns the total number and size of files uploaded.</span></span>

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

    return Ok(new { count = files.Count, size });
}
```

<span data-ttu-id="e721a-452">Slouží `Path.GetRandomFileName` k vygenerování názvu souboru bez cesty.</span><span class="sxs-lookup"><span data-stu-id="e721a-452">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="e721a-453">V následujícím příkladu je cesta získána z konfigurace:</span><span class="sxs-lookup"><span data-stu-id="e721a-453">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="e721a-454">Cesta předaná do <xref:System.IO.FileStream> *musí* zahrnovat název souboru.</span><span class="sxs-lookup"><span data-stu-id="e721a-454">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="e721a-455">Pokud není zadán název souboru, <xref:System.UnauthorizedAccessException> je vyvolána za běhu.</span><span class="sxs-lookup"><span data-stu-id="e721a-455">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="e721a-456">Soubory odeslané pomocí <xref:Microsoft.AspNetCore.Http.IFormFile> techniky jsou v paměti nebo na disku na serveru před zpracováním uloženy do vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="e721a-456">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="e721a-457">V rámci metody Action je <xref:Microsoft.AspNetCore.Http.IFormFile> obsah přístupný jako <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="e721a-457">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="e721a-458">Kromě místního systému souborů je možné soubory ukládat do síťové sdílené složky nebo do služby úložiště souborů, jako je [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="e721a-458">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="e721a-459">Další příklad, který projde několik souborů pro nahrání a používá bezpečné názvy souborů, najdete v ukázkové aplikaci v části *Pages/BufferedMultipleFileUploadPhysical. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="e721a-459">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="e721a-460">[Cesta. GetTempFileName](xref:System.IO.Path.GetTempFileName*) vyvolá výjimku, <xref:System.IO.IOException> Pokud jsou vytvořeny více než 65 535 souborů bez odstranění předchozích dočasných souborů.</span><span class="sxs-lookup"><span data-stu-id="e721a-460">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="e721a-461">Limit 65 535 souborů je omezen na server.</span><span class="sxs-lookup"><span data-stu-id="e721a-461">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="e721a-462">Další informace o tomto limitu pro operační systém Windows najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="e721a-462">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="e721a-463">GetTempFileNameA – funkce</span><span class="sxs-lookup"><span data-stu-id="e721a-463">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="e721a-464">Nahrávání malých souborů s vazbou modelu s vyrovnávací pamětí do databáze</span><span class="sxs-lookup"><span data-stu-id="e721a-464">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="e721a-465">Chcete-li uložit data binárního souboru do databáze pomocí [Entity Framework](/ef/core/index), definujte v <xref:System.Byte> entitě vlastnost Array:</span><span class="sxs-lookup"><span data-stu-id="e721a-465">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="e721a-466">Zadejte vlastnost modelu stránky pro třídu, která obsahuje <xref:Microsoft.AspNetCore.Http.IFormFile> :</span><span class="sxs-lookup"><span data-stu-id="e721a-466">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="e721a-467"><xref:Microsoft.AspNetCore.Http.IFormFile> lze ji použít přímo jako parametr metody akce nebo jako vlastnost vázaného modelu.</span><span class="sxs-lookup"><span data-stu-id="e721a-467"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="e721a-468">Předchozí příklad používá vlastnost vázaného modelu.</span><span class="sxs-lookup"><span data-stu-id="e721a-468">The prior example uses a bound model property.</span></span>

<span data-ttu-id="e721a-469">`FileUpload`Je použit ve Razor formuláři stránky:</span><span class="sxs-lookup"><span data-stu-id="e721a-469">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="e721a-470">Když je formulář publikovaný na serveru, zkopírujte <xref:Microsoft.AspNetCore.Http.IFormFile> ho do datového proudu a uložte ho jako pole bajtů v databázi.</span><span class="sxs-lookup"><span data-stu-id="e721a-470">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="e721a-471">V následujícím příkladu `_dbContext` ukládá kontext databáze aplikace:</span><span class="sxs-lookup"><span data-stu-id="e721a-471">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="e721a-472">Předchozí příklad je podobný scénáři, který je znázorněný v ukázkové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="e721a-472">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="e721a-473">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="e721a-473">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="e721a-474">*Pages/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="e721a-474">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="e721a-475">Při ukládání binárních dat do relačních databází buďte opatrní, protože to může mít nepříznivý vliv na výkon.</span><span class="sxs-lookup"><span data-stu-id="e721a-475">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="e721a-476">Nespoléhá se na nebo důvěřujete `FileName` vlastnosti <xref:Microsoft.AspNetCore.Http.IFormFile> bez ověření.</span><span class="sxs-lookup"><span data-stu-id="e721a-476">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="e721a-477">`FileName`Vlastnost by měla být použita pouze pro účely zobrazení a pouze po kódování HTML.</span><span class="sxs-lookup"><span data-stu-id="e721a-477">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="e721a-478">Uvedené příklady nevezmou ohled na zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="e721a-478">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="e721a-479">Další informace jsou k dispozici v následujících částech a [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="e721a-479">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="e721a-480">Důležité informace o zabezpečení</span><span class="sxs-lookup"><span data-stu-id="e721a-480">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="e721a-481">Ověřování</span><span class="sxs-lookup"><span data-stu-id="e721a-481">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="e721a-482">Nahrávání velkých souborů pomocí streamování</span><span class="sxs-lookup"><span data-stu-id="e721a-482">Upload large files with streaming</span></span>

<span data-ttu-id="e721a-483">Následující příklad ukazuje, jak použít JavaScript ke streamování souboru do akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="e721a-483">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="e721a-484">Token proti padělání souboru se generuje pomocí vlastního atributu filtru a předává se do hlaviček protokolu HTTP klienta místo v textu žádosti.</span><span class="sxs-lookup"><span data-stu-id="e721a-484">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="e721a-485">Vzhledem k tomu, že metoda akce zpracovává nahraná data přímo, vazba modelu formuláře je zakázána jiným vlastním filtrem.</span><span class="sxs-lookup"><span data-stu-id="e721a-485">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="e721a-486">V rámci akce je obsah formuláře čten pomocí `MultipartReader` , který čte každou jednotlivou osobu `MultipartSection` , zpracovává soubor nebo ukládá obsah podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="e721a-486">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="e721a-487">Po načtení oddílů s více částmi provede akce vlastní vazbu modelu.</span><span class="sxs-lookup"><span data-stu-id="e721a-487">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="e721a-488">Počáteční odpověď stránky načte formulář a uloží token proti padělání do cookie (prostřednictvím `GenerateAntiforgeryTokenCookieAttribute` atributu).</span><span class="sxs-lookup"><span data-stu-id="e721a-488">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="e721a-489">Atribut používá ASP.NET Core integrovanou [podporu proti padělání](xref:security/anti-request-forgery) pro nastavení cookie s tokenem požadavku:</span><span class="sxs-lookup"><span data-stu-id="e721a-489">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="e721a-490">`DisableFormValueModelBindingAttribute`Slouží k zakázání vazby modelu:</span><span class="sxs-lookup"><span data-stu-id="e721a-490">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="e721a-491">V ukázkové aplikaci a používá `GenerateAntiforgeryTokenCookieAttribute` se `DisableFormValueModelBindingAttribute` jako filtr pro modelové aplikace stránky `/StreamedSingleFileUploadDb` a `/StreamedSingleFileUploadPhysical` v `Startup.ConfigureServices` [ Razor konvencích použití stránek](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="e721a-491">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

<span data-ttu-id="e721a-492">Vzhledem k tomu, že vazba modelu nepřečte formulář, parametry, které jsou svázané z formuláře, se nezobrazují (budou pokračovat v práci s dotazem, trasou a hlavičkou).</span><span class="sxs-lookup"><span data-stu-id="e721a-492">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="e721a-493">Metoda Action pracuje přímo s `Request` vlastností.</span><span class="sxs-lookup"><span data-stu-id="e721a-493">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="e721a-494">`MultipartReader`Slouží ke čtení jednotlivých oddílů.</span><span class="sxs-lookup"><span data-stu-id="e721a-494">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="e721a-495">Data klíč/hodnota jsou uložena v `KeyValueAccumulator` .</span><span class="sxs-lookup"><span data-stu-id="e721a-495">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="e721a-496">Po načtení oddílů s více částmi se obsah `KeyValueAccumulator` používá pro svázání dat formuláře s typem modelu.</span><span class="sxs-lookup"><span data-stu-id="e721a-496">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="e721a-497">Úplná `StreamingController.UploadDatabase` metoda pro streamování do databáze s EF Core:</span><span class="sxs-lookup"><span data-stu-id="e721a-497">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="e721a-498">`MultipartRequestHelper` (*Nástroje/MultipartRequestHelper. cs*):</span><span class="sxs-lookup"><span data-stu-id="e721a-498">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="e721a-499">Úplná `StreamingController.UploadPhysical` metoda pro streamování do fyzického umístění:</span><span class="sxs-lookup"><span data-stu-id="e721a-499">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="e721a-500">V ukázkové aplikaci jsou kontroly ověřování zpracovávány nástrojem `FileHelpers.ProcessStreamedFile` .</span><span class="sxs-lookup"><span data-stu-id="e721a-500">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="e721a-501">Ověřování</span><span class="sxs-lookup"><span data-stu-id="e721a-501">Validation</span></span>

<span data-ttu-id="e721a-502">Třída ukázkové aplikace `FileHelpers` ukazuje několik kontrol ukládání souborů do vyrovnávací paměti <xref:Microsoft.AspNetCore.Http.IFormFile> a datových proudů při nahrávání.</span><span class="sxs-lookup"><span data-stu-id="e721a-502">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="e721a-503">Informace o zpracování <xref:Microsoft.AspNetCore.Http.IFormFile> ukládání souborů do vyrovnávací paměti v ukázkové aplikaci naleznete v `ProcessFormFile` metodě v souboru *Utilities/Helper. cs* .</span><span class="sxs-lookup"><span data-stu-id="e721a-503">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="e721a-504">Pro zpracování streamované soubory se podívejte na `ProcessStreamedFile` metodu ve stejném souboru.</span><span class="sxs-lookup"><span data-stu-id="e721a-504">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="e721a-505">Metody zpracování ověřování, které jsou znázorněné v ukázkové aplikaci, nekontrolují obsah nahraných souborů.</span><span class="sxs-lookup"><span data-stu-id="e721a-505">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="e721a-506">Ve většině produkčních scénářů se v souboru používá rozhraní API pro skenování virů nebo malwaru, než je soubor dostupný uživatelům nebo jiným systémům.</span><span class="sxs-lookup"><span data-stu-id="e721a-506">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="e721a-507">I když ukázka tématu poskytuje pracovní příklad technik ověřování, Neimplementujte `FileHelpers` třídu v produkční aplikaci, pokud:</span><span class="sxs-lookup"><span data-stu-id="e721a-507">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="e721a-508">Plně rozumíte implementaci.</span><span class="sxs-lookup"><span data-stu-id="e721a-508">Fully understand the implementation.</span></span>
> * <span data-ttu-id="e721a-509">Upravte implementaci podle potřeby pro prostředí a specifikace aplikace.</span><span class="sxs-lookup"><span data-stu-id="e721a-509">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="e721a-510">**Nikdy nepoužívejte nerozlišený kód zabezpečení v aplikaci bez nutnosti řešit tyto požadavky.**</span><span class="sxs-lookup"><span data-stu-id="e721a-510">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="e721a-511">Ověření obsahu</span><span class="sxs-lookup"><span data-stu-id="e721a-511">Content validation</span></span>

<span data-ttu-id="e721a-512">**Pro nahraný obsah použijte rozhraní API pro kontrolu virů a malwaru třetí strany.**</span><span class="sxs-lookup"><span data-stu-id="e721a-512">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="e721a-513">Prohledávání souborů je náročné na prostředky serveru ve scénářích s vysokým objemem.</span><span class="sxs-lookup"><span data-stu-id="e721a-513">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="e721a-514">Pokud dojde ke snížení výkonu zpracování požadavků z důvodu kontroly souborů, zvažte přesměrování práce skenování na službu na [pozadí](xref:fundamentals/host/hosted-services), případně služby spuštěné na serveru, který se liší od serveru aplikace.</span><span class="sxs-lookup"><span data-stu-id="e721a-514">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="e721a-515">Nahrané soubory se obvykle uchovávají v oblasti v karanténě, dokud je kontrola virů na pozadí nevrátí.</span><span class="sxs-lookup"><span data-stu-id="e721a-515">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="e721a-516">Když soubor projde, soubor se přesune do normálního umístění úložiště souborů.</span><span class="sxs-lookup"><span data-stu-id="e721a-516">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="e721a-517">Tyto kroky se obvykle provádí ve spojení s databázovým záznamem, který indikuje stav kontroly souboru.</span><span class="sxs-lookup"><span data-stu-id="e721a-517">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="e721a-518">Při použití takového přístupu zůstane aplikace a Server App zaměřené na reakci na požadavky.</span><span class="sxs-lookup"><span data-stu-id="e721a-518">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="e721a-519">Ověření přípony souboru</span><span class="sxs-lookup"><span data-stu-id="e721a-519">File extension validation</span></span>

<span data-ttu-id="e721a-520">Přípona nahraného souboru by měla být zaškrtnutá na seznamu povolených rozšíření.</span><span class="sxs-lookup"><span data-stu-id="e721a-520">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="e721a-521">Příklad:</span><span class="sxs-lookup"><span data-stu-id="e721a-521">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="e721a-522">Ověření podpisu souboru</span><span class="sxs-lookup"><span data-stu-id="e721a-522">File signature validation</span></span>

<span data-ttu-id="e721a-523">Podpis souboru se určuje na prvních několika bajtech na začátku souboru.</span><span class="sxs-lookup"><span data-stu-id="e721a-523">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="e721a-524">Tyto bajty lze použít k určení, zda přípona odpovídá obsahu souboru.</span><span class="sxs-lookup"><span data-stu-id="e721a-524">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="e721a-525">Ukázková aplikace zkontroluje podpisy souborů pro několik běžných typů souborů.</span><span class="sxs-lookup"><span data-stu-id="e721a-525">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="e721a-526">V následujícím příkladu se pro tento soubor kontroluje podpis souboru obrázku JPEG:</span><span class="sxs-lookup"><span data-stu-id="e721a-526">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="e721a-527">Další signatury souborů získáte v dokumentaci [signatury souborů](https://www.filesignatures.net/) a oficiálních souborů.</span><span class="sxs-lookup"><span data-stu-id="e721a-527">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="e721a-528">Zabezpečení názvu souboru</span><span class="sxs-lookup"><span data-stu-id="e721a-528">File name security</span></span>

<span data-ttu-id="e721a-529">Nikdy nepoužívejte název souboru dodaný klientem pro uložení souboru do fyzického úložiště.</span><span class="sxs-lookup"><span data-stu-id="e721a-529">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="e721a-530">Vytvořte bezpečný název souboru pro soubor pomocí [cesty. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) nebo [cesty. GetTempFileName](xref:System.IO.Path.GetTempFileName*) a vytvořte úplnou cestu (včetně názvu souboru) pro dočasné úložiště.</span><span class="sxs-lookup"><span data-stu-id="e721a-530">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="e721a-531">Razor Automatické kódování HTML kóduje hodnoty vlastností pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="e721a-531">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="e721a-532">Následující kód je bezpečné použít:</span><span class="sxs-lookup"><span data-stu-id="e721a-532">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="e721a-533">Mimo Razor , vždy <xref:System.Net.WebUtility.HtmlEncode*> obsah názvu souboru z požadavku uživatele.</span><span class="sxs-lookup"><span data-stu-id="e721a-533">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="e721a-534">Mnoho implementací musí zahrnovat kontrolu, že soubor existuje. v opačném případě je soubor přepsán souborem se stejným názvem.</span><span class="sxs-lookup"><span data-stu-id="e721a-534">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="e721a-535">Poskytněte další logiku pro splnění specifikací vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="e721a-535">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="e721a-536">Ověřování velikosti</span><span class="sxs-lookup"><span data-stu-id="e721a-536">Size validation</span></span>

<span data-ttu-id="e721a-537">Omezte velikost nahraných souborů.</span><span class="sxs-lookup"><span data-stu-id="e721a-537">Limit the size of uploaded files.</span></span>

<span data-ttu-id="e721a-538">V ukázkové aplikaci je velikost souboru omezená na 2 MB (uvedené v bajtech).</span><span class="sxs-lookup"><span data-stu-id="e721a-538">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="e721a-539">Limit se poskytuje prostřednictvím [Konfigurace](xref:fundamentals/configuration/index) z *appsettings.jsv* souboru:</span><span class="sxs-lookup"><span data-stu-id="e721a-539">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="e721a-540">`FileSizeLimit`Třída je vložena do `PageModel` tříd:</span><span class="sxs-lookup"><span data-stu-id="e721a-540">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="e721a-541">Když velikost souboru překročí limit, soubor se odmítne:</span><span class="sxs-lookup"><span data-stu-id="e721a-541">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="e721a-542">Porovnává hodnotu atributu name s parametrem název metody POST</span><span class="sxs-lookup"><span data-stu-id="e721a-542">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="e721a-543">V Razor neformulářích, které publikují data formuláře nebo přímo využívají JavaScript `FormData` , název zadaný v prvku formuláře nebo `FormData` musí odpovídat názvu parametru v akci kontroleru.</span><span class="sxs-lookup"><span data-stu-id="e721a-543">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="e721a-544">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="e721a-544">In the following example:</span></span>

* <span data-ttu-id="e721a-545">Při použití `<input>` elementu `name` je atribut nastaven na hodnotu `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="e721a-545">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="e721a-546">Při použití `FormData` v jazyce JavaScript je název nastaven na hodnotu `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="e721a-546">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="e721a-547">Pro parametr metody jazyka C# () použijte stejný název `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="e721a-547">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="e721a-548">Pro Razor metodu obslužné rutiny stránky stránky s názvem `Upload` :</span><span class="sxs-lookup"><span data-stu-id="e721a-548">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="e721a-549">Pro metodu akce po kontroléru MVC:</span><span class="sxs-lookup"><span data-stu-id="e721a-549">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="e721a-550">Konfigurace serveru a aplikace</span><span class="sxs-lookup"><span data-stu-id="e721a-550">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="e721a-551">Omezení délky těla částí</span><span class="sxs-lookup"><span data-stu-id="e721a-551">Multipart body length limit</span></span>

<span data-ttu-id="e721a-552"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> nastaví limit délky jednotlivých částí části.</span><span class="sxs-lookup"><span data-stu-id="e721a-552"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="e721a-553">Oddíly formuláře, které překračují toto omezení, vyvolávají <xref:System.IO.InvalidDataException> při analýze.</span><span class="sxs-lookup"><span data-stu-id="e721a-553">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="e721a-554">Výchozí hodnota je 134 217 728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="e721a-554">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="e721a-555">Upravte limit pomocí <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> nastavení v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e721a-555">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="e721a-556"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> slouží k nastavení <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> pro jednu stránku nebo akci.</span><span class="sxs-lookup"><span data-stu-id="e721a-556"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="e721a-557">V Razor aplikaci Pages použijte filtr s [konvencí](xref:razor-pages/razor-pages-conventions) v nástroji `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e721a-557">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages(options =>
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

<span data-ttu-id="e721a-558">V Razor aplikaci Pages nebo aplikaci MVC použijte filtr na model stránky nebo metodu akce:</span><span class="sxs-lookup"><span data-stu-id="e721a-558">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="e721a-559">Kestrel maximální velikost textu požadavku</span><span class="sxs-lookup"><span data-stu-id="e721a-559">Kestrel maximum request body size</span></span>

<span data-ttu-id="e721a-560">Pro aplikace hostované v Kestrel je výchozí maximální velikost textu požadavku 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="e721a-560">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="e721a-561">Přizpůsobte limit pomocí možnosti serveru [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:</span><span class="sxs-lookup"><span data-stu-id="e721a-561">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel((context, options) =>
            {
                // Handle requests up to 50 MB
                options.Limits.MaxRequestBodySize = 52428800;
            })
            .UseStartup<Startup>();
        });
```

<span data-ttu-id="e721a-562"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> slouží k nastavení [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) pro jednu stránku nebo akci.</span><span class="sxs-lookup"><span data-stu-id="e721a-562"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="e721a-563">V Razor aplikaci Pages použijte filtr s [konvencí](xref:razor-pages/razor-pages-conventions) v nástroji `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e721a-563">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages(options =>
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

<span data-ttu-id="e721a-564">V Razor aplikaci Pages nebo aplikaci MVC použijte filtr na třídu obslužné rutiny stránky nebo na metodu akce:</span><span class="sxs-lookup"><span data-stu-id="e721a-564">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="e721a-565">`RequestSizeLimitAttribute`Lze také použít pomocí [`@attribute`](xref:mvc/views/razor#attribute) Razor direktivy:</span><span class="sxs-lookup"><span data-stu-id="e721a-565">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="e721a-566">Další omezení Kestrel</span><span class="sxs-lookup"><span data-stu-id="e721a-566">Other Kestrel limits</span></span>

<span data-ttu-id="e721a-567">Pro aplikace hostované v Kestrel se můžou vztahovat další omezení Kestrel:</span><span class="sxs-lookup"><span data-stu-id="e721a-567">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="e721a-568">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="e721a-568">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="e721a-569">Sazby za data požadavků a odpovědí</span><span class="sxs-lookup"><span data-stu-id="e721a-569">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a><span data-ttu-id="e721a-570">IIS</span><span class="sxs-lookup"><span data-stu-id="e721a-570">IIS</span></span>

<span data-ttu-id="e721a-571">Výchozí limit počtu požadavků ( `maxAllowedContentLength` ) je 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="e721a-571">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="e721a-572">Přizpůsobte limit v `web.config` souboru.</span><span class="sxs-lookup"><span data-stu-id="e721a-572">Customize the limit in the `web.config` file.</span></span> <span data-ttu-id="e721a-573">V následujícím příkladu je limit nastaven na 50 MB (52 428 800 bajtů):</span><span class="sxs-lookup"><span data-stu-id="e721a-573">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="e721a-574">`maxAllowedContentLength`Nastavení platí pouze pro službu IIS.</span><span class="sxs-lookup"><span data-stu-id="e721a-574">The `maxAllowedContentLength` setting only applies to IIS.</span></span> <span data-ttu-id="e721a-575">Další informace najdete v tématu [omezení `<requestLimits>` požadavků ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="e721a-575">For more information, see [Request Limits `<requestLimits>`](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="e721a-576">Zvyšte maximální velikost textu požadavku pro požadavek HTTP nastavením <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="e721a-576">Increase the maximum request body size for the HTTP request by setting <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e721a-577">V následujícím příkladu je limit nastaven na 50 MB (52 428 800 bajtů):</span><span class="sxs-lookup"><span data-stu-id="e721a-577">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

<span data-ttu-id="e721a-578">Další informace naleznete v tématu <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="e721a-578">For more information, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="e721a-579">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="e721a-579">Troubleshoot</span></span>

<span data-ttu-id="e721a-580">Níže jsou uvedeny některé běžné problémy, které se vyskytly při práci s nahráváním souborů a jejich možnými řešeními.</span><span class="sxs-lookup"><span data-stu-id="e721a-580">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="e721a-581">Při nasazení na server služby IIS se nenašla chyba.</span><span class="sxs-lookup"><span data-stu-id="e721a-581">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="e721a-582">Následující chyba znamená, že nahraný soubor překračuje délku nakonfigurovaného obsahu serveru:</span><span class="sxs-lookup"><span data-stu-id="e721a-582">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="e721a-583">Další informace najdete v části [IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="e721a-583">For more information, see the [IIS](#iis) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="e721a-584">Chyba připojení</span><span class="sxs-lookup"><span data-stu-id="e721a-584">Connection failure</span></span>

<span data-ttu-id="e721a-585">Chyba připojení a připojení k serveru pro resetování pravděpodobně znamená, že nahraný soubor překračuje maximální velikost textu požadavku Kestrel.</span><span class="sxs-lookup"><span data-stu-id="e721a-585">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="e721a-586">Další informace najdete v části [Kestrel maximální velikost textu požadavku](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="e721a-586">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="e721a-587">Omezení připojení klientů Kestrel mohou také vyžadovat úpravu.</span><span class="sxs-lookup"><span data-stu-id="e721a-587">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="e721a-588">Výjimka odkazu s hodnotou null s IFormFile</span><span class="sxs-lookup"><span data-stu-id="e721a-588">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="e721a-589">Pokud kontroler přijímá odeslané soubory pomocí <xref:Microsoft.AspNetCore.Http.IFormFile> , ale hodnota je `null` , potvrďte, že formulář HTML určuje `enctype` hodnotu `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="e721a-589">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="e721a-590">Pokud tento atribut není nastaven na `<form>` elementu, odeslání souboru neproběhne a jakékoli vázané <xref:Microsoft.AspNetCore.Http.IFormFile> argumenty jsou `null` .</span><span class="sxs-lookup"><span data-stu-id="e721a-590">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="e721a-591">Ujistěte se také, že [nahrávání názvů v datech formuláře odpovídá pojmenování aplikace](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="e721a-591">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="e721a-592">Proud je příliš dlouhý.</span><span class="sxs-lookup"><span data-stu-id="e721a-592">Stream was too long</span></span>

<span data-ttu-id="e721a-593">Příklady v tomto tématu <xref:System.IO.MemoryStream> se spoléhají na uchovávání obsahu nahraného souboru.</span><span class="sxs-lookup"><span data-stu-id="e721a-593">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="e721a-594">Omezení velikosti `MemoryStream` je `int.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="e721a-594">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="e721a-595">Pokud scénář nahrávání souborů aplikace vyžaduje, aby byl obsah souboru větší než 50 MB, použijte alternativní přístup, který nespoléhá na jednu z nich `MemoryStream` pro uchovávání obsahu nahraného souboru.</span><span class="sxs-lookup"><span data-stu-id="e721a-595">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e721a-596">ASP.NET Core podporuje nahrávání jednoho nebo více souborů pomocí vazby modelu ve vyrovnávací paměti pro menší soubory a streamování bez vyrovnávací paměti pro větší soubory.</span><span class="sxs-lookup"><span data-stu-id="e721a-596">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="e721a-597">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e721a-597">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="e721a-598">Důležité informace o zabezpečení</span><span class="sxs-lookup"><span data-stu-id="e721a-598">Security considerations</span></span>

<span data-ttu-id="e721a-599">Pokud chcete uživatelům poskytnout možnost nahrávat soubory na server, buďte opatrní.</span><span class="sxs-lookup"><span data-stu-id="e721a-599">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="e721a-600">Útočníci se můžou pokusit:</span><span class="sxs-lookup"><span data-stu-id="e721a-600">Attackers may attempt to:</span></span>

* <span data-ttu-id="e721a-601">Vykoná útok [DOS (Denial of Service](/windows-hardware/drivers/ifs/denial-of-service) ).</span><span class="sxs-lookup"><span data-stu-id="e721a-601">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="e721a-602">Nahrání virů nebo malwaru</span><span class="sxs-lookup"><span data-stu-id="e721a-602">Upload viruses or malware.</span></span>
* <span data-ttu-id="e721a-603">Narušit sítě a servery jinými způsoby.</span><span class="sxs-lookup"><span data-stu-id="e721a-603">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="e721a-604">Bezpečnostní kroky, které snižují pravděpodobnost úspěšného útoku, jsou:</span><span class="sxs-lookup"><span data-stu-id="e721a-604">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="e721a-605">Nahrajte soubory do vyhrazené oblasti pro nahrávání souborů, nejlépe do nesystémové jednotky.</span><span class="sxs-lookup"><span data-stu-id="e721a-605">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="e721a-606">Vyhrazené umístění usnadňuje omezení zabezpečení pro nahrané soubory.</span><span class="sxs-lookup"><span data-stu-id="e721a-606">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="e721a-607">Zakažte oprávnění EXECUTE pro umístění pro nahrání souboru.&dagger;</span><span class="sxs-lookup"><span data-stu-id="e721a-607">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="e721a-608">Neuchovávat nahrané soubory ve stejném stromu adresářů jako aplikace. **not**&dagger;</span><span class="sxs-lookup"><span data-stu-id="e721a-608">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="e721a-609">Použijte název bezpečného souboru určený aplikací.</span><span class="sxs-lookup"><span data-stu-id="e721a-609">Use a safe file name determined by the app.</span></span> <span data-ttu-id="e721a-610">Nepoužívejte název souboru poskytnutý uživatelem nebo nedůvěryhodného názvu nahraného souboru. &dagger; HTML při zobrazení kódování názvu nedůvěryhodného souboru.</span><span class="sxs-lookup"><span data-stu-id="e721a-610">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="e721a-611">Například protokolování názvu souboru nebo zobrazení v uživatelském rozhraní ( Razor Automatické kódování HTML kódování).</span><span class="sxs-lookup"><span data-stu-id="e721a-611">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="e721a-612">Povolte pro specifikaci návrhu aplikace jenom schválené přípony souborů.&dagger;</span><span class="sxs-lookup"><span data-stu-id="e721a-612">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="e721a-613">Ověřte, zda jsou na serveru provedeny kontroly na straně klienta. &dagger; Kontroly na straně klienta je snadné obejít.</span><span class="sxs-lookup"><span data-stu-id="e721a-613">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="e721a-614">Ověřte velikost nahraného souboru.</span><span class="sxs-lookup"><span data-stu-id="e721a-614">Check the size of an uploaded file.</span></span> <span data-ttu-id="e721a-615">Nastavte limit maximální velikosti, aby se zabránilo velkým nahrávání.&dagger;</span><span class="sxs-lookup"><span data-stu-id="e721a-615">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="e721a-616">Pokud by soubory neměly být přepsány nahraným souborem se stejným názvem, před nahráním souboru ověřte název souboru proti databázi nebo fyzickému úložišti.</span><span class="sxs-lookup"><span data-stu-id="e721a-616">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="e721a-617">**Před uložením souboru spusťte v nahraném obsahu skener virů nebo malwaru.**</span><span class="sxs-lookup"><span data-stu-id="e721a-617">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="e721a-618">&dagger;Ukázková aplikace předvádí přístup, který splňuje kritéria.</span><span class="sxs-lookup"><span data-stu-id="e721a-618">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="e721a-619">Nahrávání škodlivého kódu do systému je často prvním krokem ke spuštění kódu, který může:</span><span class="sxs-lookup"><span data-stu-id="e721a-619">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="e721a-620">Zcela získá kontrolu nad systémem.</span><span class="sxs-lookup"><span data-stu-id="e721a-620">Completely gain control of a system.</span></span>
> * <span data-ttu-id="e721a-621">Přetížit systém s výsledkem, že dojde k chybě systému.</span><span class="sxs-lookup"><span data-stu-id="e721a-621">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="e721a-622">Napadnout data uživatelů nebo systémových dat.</span><span class="sxs-lookup"><span data-stu-id="e721a-622">Compromise user or system data.</span></span>
> * <span data-ttu-id="e721a-623">Použijte graffiti pro veřejné uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e721a-623">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="e721a-624">Informace o omezení oblasti útoku při přijímání souborů uživateli najdete v následujících zdrojích informací:</span><span class="sxs-lookup"><span data-stu-id="e721a-624">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="e721a-625">Neomezená nahrávání souboru</span><span class="sxs-lookup"><span data-stu-id="e721a-625">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="e721a-626">Zabezpečení Azure: Ujistěte se, že jsou při přijímání souborů od uživatelů k dismístě správné ovládací prvky.</span><span class="sxs-lookup"><span data-stu-id="e721a-626">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="e721a-627">Další informace o implementaci bezpečnostních opatření, včetně příkladů z ukázkové aplikace, najdete v části [ověření](#validation) .</span><span class="sxs-lookup"><span data-stu-id="e721a-627">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="e721a-628">Scénáře úložiště</span><span class="sxs-lookup"><span data-stu-id="e721a-628">Storage scenarios</span></span>

<span data-ttu-id="e721a-629">Mezi běžné možnosti úložiště pro soubory patří:</span><span class="sxs-lookup"><span data-stu-id="e721a-629">Common storage options for files include:</span></span>

* <span data-ttu-id="e721a-630">Databáze</span><span class="sxs-lookup"><span data-stu-id="e721a-630">Database</span></span>

  * <span data-ttu-id="e721a-631">U malých nahrávání souborů je databáze často rychlejší než možnosti fyzického úložiště (systému souborů nebo síťového sdílení).</span><span class="sxs-lookup"><span data-stu-id="e721a-631">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="e721a-632">Databáze je často pohodlnější než možnosti fyzického úložiště, protože načtení záznamu databáze pro uživatelská data může současně poskytovat obsah souboru (například obrázek miniatury).</span><span class="sxs-lookup"><span data-stu-id="e721a-632">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="e721a-633">Databáze je potenciálně levnější než použití služby úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="e721a-633">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="e721a-634">Fyzické úložiště (systém souborů nebo síťová sdílená složka)</span><span class="sxs-lookup"><span data-stu-id="e721a-634">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="e721a-635">Pro nahrávání velkých souborů:</span><span class="sxs-lookup"><span data-stu-id="e721a-635">For large file uploads:</span></span>
    * <span data-ttu-id="e721a-636">Omezení databáze mohou omezit velikost nahrávání.</span><span class="sxs-lookup"><span data-stu-id="e721a-636">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="e721a-637">Fyzické úložiště je často méně hospodárné než úložiště v databázi.</span><span class="sxs-lookup"><span data-stu-id="e721a-637">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="e721a-638">Fyzické úložiště je potenciálně levnější než použití služby úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="e721a-638">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="e721a-639">Proces aplikace musí mít oprávnění ke čtení a zápisu do umístění úložiště.</span><span class="sxs-lookup"><span data-stu-id="e721a-639">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="e721a-640">**Nikdy neudělujte oprávnění EXECUTE.**</span><span class="sxs-lookup"><span data-stu-id="e721a-640">**Never grant execute permission.**</span></span>

* <span data-ttu-id="e721a-641">Služba úložiště dat (například [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="e721a-641">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="e721a-642">Služby obvykle nabízejí vylepšenou škálovatelnost a odolnost proti místním řešením, které obvykle podléhají jednomu bodu selhání.</span><span class="sxs-lookup"><span data-stu-id="e721a-642">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="e721a-643">Služby jsou potenciálně nižší náklady ve scénářích infrastruktury velkých úložišť.</span><span class="sxs-lookup"><span data-stu-id="e721a-643">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="e721a-644">Další informace najdete v tématu [rychlý Start: použití .NET k vytvoření objektu BLOB v úložišti objektů](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="e721a-644">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="e721a-645">Téma ukazuje <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*> , ale <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> dá se použít k uložení do <xref:System.IO.FileStream> úložiště objektů BLOB při práci s <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="e721a-645">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="e721a-646">Scénáře nahrávání souborů</span><span class="sxs-lookup"><span data-stu-id="e721a-646">File upload scenarios</span></span>

<span data-ttu-id="e721a-647">Dva obecné přístupy k nahrávání souborů jsou ukládání do vyrovnávací paměti a streamování.</span><span class="sxs-lookup"><span data-stu-id="e721a-647">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="e721a-648">**Do vyrovnávací paměti**</span><span class="sxs-lookup"><span data-stu-id="e721a-648">**Buffering**</span></span>

<span data-ttu-id="e721a-649">Celý soubor je načten do <xref:Microsoft.AspNetCore.Http.IFormFile> , což je reprezentace souboru, který se používá ke zpracování nebo uložení souboru v jazyce C#.</span><span class="sxs-lookup"><span data-stu-id="e721a-649">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="e721a-650">Prostředky (disk, paměť) používané při nahrávání souborů závisí na počtu a velikosti souběžných nahrávání souborů.</span><span class="sxs-lookup"><span data-stu-id="e721a-650">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="e721a-651">Pokud se aplikace pokusí do vyrovnávací paměti příliš mnoho nahrávání, dojde k selhání lokality, když dojde k vynechání paměti nebo místa na disku.</span><span class="sxs-lookup"><span data-stu-id="e721a-651">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="e721a-652">Pokud velikost nebo frekvence nahrávání souborů vyčerpá prostředky aplikace, použijte streamování.</span><span class="sxs-lookup"><span data-stu-id="e721a-652">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="e721a-653">Z paměti do dočasného souboru na disku se přesune libovolný soubor s vyrovnávací pamětí větší než 64 KB.</span><span class="sxs-lookup"><span data-stu-id="e721a-653">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="e721a-654">Ukládání malých souborů do vyrovnávací paměti je popsáno v následujících částech tohoto tématu:</span><span class="sxs-lookup"><span data-stu-id="e721a-654">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="e721a-655">Fyzické úložiště</span><span class="sxs-lookup"><span data-stu-id="e721a-655">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="e721a-656">Databáze</span><span class="sxs-lookup"><span data-stu-id="e721a-656">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="e721a-657">**Streamování**</span><span class="sxs-lookup"><span data-stu-id="e721a-657">**Streaming**</span></span>

<span data-ttu-id="e721a-658">Soubor se přijímá z požadavku na více částí a přímo se zpracovává nebo ukládá v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e721a-658">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="e721a-659">Streamování nijak významně nezvyšuje výkon.</span><span class="sxs-lookup"><span data-stu-id="e721a-659">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="e721a-660">Streamování snižuje nároky na paměť nebo místo na disku při nahrávání souborů.</span><span class="sxs-lookup"><span data-stu-id="e721a-660">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="e721a-661">Streamování velkých souborů je zahrnuté v části [nahrávání velkých souborů pomocí streamování](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="e721a-661">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="e721a-662">Nahrávání malých souborů s vazbou modelu ve vyrovnávací paměti na fyzické úložiště</span><span class="sxs-lookup"><span data-stu-id="e721a-662">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="e721a-663">Pro nahrání malých souborů použijte formulář s více částmi nebo sestavte požadavek POST pomocí JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="e721a-663">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="e721a-664">Následující příklad ukazuje použití Razor formuláře stránky k nahrání jednoho souboru (*Pages/BufferedSingleFileUploadPhysical. cshtml* do ukázkové aplikace):</span><span class="sxs-lookup"><span data-stu-id="e721a-664">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="e721a-665">Následující příklad je podobný předchozímu příkladu s tím rozdílem, že:</span><span class="sxs-lookup"><span data-stu-id="e721a-665">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="e721a-666">K odeslání dat formuláře se používá JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)).</span><span class="sxs-lookup"><span data-stu-id="e721a-666">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="e721a-667">Neexistuje žádné ověření.</span><span class="sxs-lookup"><span data-stu-id="e721a-667">There's no validation.</span></span>

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

<span data-ttu-id="e721a-668">Chcete-li provést příspěvek formuláře v jazyce JavaScript pro klienty, kteří [nepodporují rozhraní API pro načítání](https://caniuse.com/#feat=fetch), použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="e721a-668">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="e721a-669">Použijte načtenou výplň (například [window. Fetch Fill (GitHub/Fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="e721a-669">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="e721a-670">Použijte `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="e721a-670">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="e721a-671">Příklad:</span><span class="sxs-lookup"><span data-stu-id="e721a-671">For example:</span></span>

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

<span data-ttu-id="e721a-672">Aby bylo možné podporovat nahrávání souborů, musí formuláře HTML určovat typ kódování ( `enctype` ) `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="e721a-672">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="e721a-673">Pro `files` vstupní element, který podporuje nahrávání více souborů, poskytněte `multiple` atribut `<input>` elementu:</span><span class="sxs-lookup"><span data-stu-id="e721a-673">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="e721a-674">Jednotlivé soubory nahrané na server jsou k dispozici prostřednictvím [vazby modelu](xref:mvc/models/model-binding) pomocí <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="e721a-674">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="e721a-675">Ukázková aplikace ukazuje více ukládání souborů do vyrovnávací paměti pro scénáře databáze a fyzických úložišť.</span><span class="sxs-lookup"><span data-stu-id="e721a-675">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="e721a-676">Nepoužívejte **not** `FileName` vlastnost <xref:Microsoft.AspNetCore.Http.IFormFile> jinou než pro zobrazení a protokolování.</span><span class="sxs-lookup"><span data-stu-id="e721a-676">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="e721a-677">Při zobrazení nebo protokolování je název souboru kódován HTML.</span><span class="sxs-lookup"><span data-stu-id="e721a-677">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="e721a-678">Útočník může poskytnout škodlivý název souboru, včetně úplných cest nebo relativních cest.</span><span class="sxs-lookup"><span data-stu-id="e721a-678">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="e721a-679">Aplikace by měly:</span><span class="sxs-lookup"><span data-stu-id="e721a-679">Applications should:</span></span>
>
> * <span data-ttu-id="e721a-680">Odeberte cestu z názvu souboru zadaného uživatelem.</span><span class="sxs-lookup"><span data-stu-id="e721a-680">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="e721a-681">Uložte název souboru s příponou PATH s kódováním HTML pro uživatelské rozhraní nebo protokolování.</span><span class="sxs-lookup"><span data-stu-id="e721a-681">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="e721a-682">Vygenerujte nový náhodný název souboru pro úložiště.</span><span class="sxs-lookup"><span data-stu-id="e721a-682">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="e721a-683">Následující kód odstraní cestu z názvu souboru:</span><span class="sxs-lookup"><span data-stu-id="e721a-683">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="e721a-684">Zde uvedené příklady neberou ohled na zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="e721a-684">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="e721a-685">Další informace jsou k dispozici v následujících částech a [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="e721a-685">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="e721a-686">Důležité informace o zabezpečení</span><span class="sxs-lookup"><span data-stu-id="e721a-686">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="e721a-687">Ověřování</span><span class="sxs-lookup"><span data-stu-id="e721a-687">Validation</span></span>](#validation)

<span data-ttu-id="e721a-688">Při nahrávání souborů pomocí vazby modelu a <xref:Microsoft.AspNetCore.Http.IFormFile> může metoda Action přijmout:</span><span class="sxs-lookup"><span data-stu-id="e721a-688">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="e721a-689">Jedna <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="e721a-689">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="e721a-690">Kterákoli z následujících kolekcí, které reprezentují několik souborů:</span><span class="sxs-lookup"><span data-stu-id="e721a-690">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="e721a-691">[Seznamu](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="e721a-691">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="e721a-692">Vazba odpovídá souborům formuláře podle názvu.</span><span class="sxs-lookup"><span data-stu-id="e721a-692">Binding matches form files by name.</span></span> <span data-ttu-id="e721a-693">Například hodnota HTML v se `name` `<input type="file" name="formFile">` musí shodovat s parametrem nebo vazbou vlastnosti jazyka C# ( `FormFile` ).</span><span class="sxs-lookup"><span data-stu-id="e721a-693">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="e721a-694">Další informace naleznete v části [název atributu matched na název parametru metody post](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="e721a-694">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="e721a-695">Následující příklad:</span><span class="sxs-lookup"><span data-stu-id="e721a-695">The following example:</span></span>

* <span data-ttu-id="e721a-696">Projde jedním nebo více nahranými soubory.</span><span class="sxs-lookup"><span data-stu-id="e721a-696">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="e721a-697">Pomocí [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) vrátí úplnou cestu k souboru, včetně názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="e721a-697">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="e721a-698">Uloží soubory do místního systému souborů pomocí názvu souboru generovaného aplikací.</span><span class="sxs-lookup"><span data-stu-id="e721a-698">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="e721a-699">Vrátí celkový počet nahraných souborů a jejich velikost.</span><span class="sxs-lookup"><span data-stu-id="e721a-699">Returns the total number and size of files uploaded.</span></span>

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

    return Ok(new { count = files.Count, size });
}
```

<span data-ttu-id="e721a-700">Slouží `Path.GetRandomFileName` k vygenerování názvu souboru bez cesty.</span><span class="sxs-lookup"><span data-stu-id="e721a-700">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="e721a-701">V následujícím příkladu je cesta získána z konfigurace:</span><span class="sxs-lookup"><span data-stu-id="e721a-701">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="e721a-702">Cesta předaná do <xref:System.IO.FileStream> *musí* zahrnovat název souboru.</span><span class="sxs-lookup"><span data-stu-id="e721a-702">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="e721a-703">Pokud není zadán název souboru, <xref:System.UnauthorizedAccessException> je vyvolána za běhu.</span><span class="sxs-lookup"><span data-stu-id="e721a-703">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="e721a-704">Soubory odeslané pomocí <xref:Microsoft.AspNetCore.Http.IFormFile> techniky jsou v paměti nebo na disku na serveru před zpracováním uloženy do vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="e721a-704">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="e721a-705">V rámci metody Action je <xref:Microsoft.AspNetCore.Http.IFormFile> obsah přístupný jako <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="e721a-705">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="e721a-706">Kromě místního systému souborů je možné soubory ukládat do síťové sdílené složky nebo do služby úložiště souborů, jako je [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="e721a-706">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="e721a-707">Další příklad, který projde několik souborů pro nahrání a používá bezpečné názvy souborů, najdete v ukázkové aplikaci v části *Pages/BufferedMultipleFileUploadPhysical. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="e721a-707">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="e721a-708">[Cesta. GetTempFileName](xref:System.IO.Path.GetTempFileName*) vyvolá výjimku, <xref:System.IO.IOException> Pokud jsou vytvořeny více než 65 535 souborů bez odstranění předchozích dočasných souborů.</span><span class="sxs-lookup"><span data-stu-id="e721a-708">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="e721a-709">Limit 65 535 souborů je omezen na server.</span><span class="sxs-lookup"><span data-stu-id="e721a-709">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="e721a-710">Další informace o tomto limitu pro operační systém Windows najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="e721a-710">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="e721a-711">GetTempFileNameA – funkce</span><span class="sxs-lookup"><span data-stu-id="e721a-711">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="e721a-712">Nahrávání malých souborů s vazbou modelu s vyrovnávací pamětí do databáze</span><span class="sxs-lookup"><span data-stu-id="e721a-712">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="e721a-713">Chcete-li uložit data binárního souboru do databáze pomocí [Entity Framework](/ef/core/index), definujte v <xref:System.Byte> entitě vlastnost Array:</span><span class="sxs-lookup"><span data-stu-id="e721a-713">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="e721a-714">Zadejte vlastnost modelu stránky pro třídu, která obsahuje <xref:Microsoft.AspNetCore.Http.IFormFile> :</span><span class="sxs-lookup"><span data-stu-id="e721a-714">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="e721a-715"><xref:Microsoft.AspNetCore.Http.IFormFile> lze ji použít přímo jako parametr metody akce nebo jako vlastnost vázaného modelu.</span><span class="sxs-lookup"><span data-stu-id="e721a-715"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="e721a-716">Předchozí příklad používá vlastnost vázaného modelu.</span><span class="sxs-lookup"><span data-stu-id="e721a-716">The prior example uses a bound model property.</span></span>

<span data-ttu-id="e721a-717">`FileUpload`Je použit ve Razor formuláři stránky:</span><span class="sxs-lookup"><span data-stu-id="e721a-717">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="e721a-718">Když je formulář publikovaný na serveru, zkopírujte <xref:Microsoft.AspNetCore.Http.IFormFile> ho do datového proudu a uložte ho jako pole bajtů v databázi.</span><span class="sxs-lookup"><span data-stu-id="e721a-718">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="e721a-719">V následujícím příkladu `_dbContext` ukládá kontext databáze aplikace:</span><span class="sxs-lookup"><span data-stu-id="e721a-719">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="e721a-720">Předchozí příklad je podobný scénáři, který je znázorněný v ukázkové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="e721a-720">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="e721a-721">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="e721a-721">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="e721a-722">*Pages/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="e721a-722">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="e721a-723">Při ukládání binárních dat do relačních databází buďte opatrní, protože to může mít nepříznivý vliv na výkon.</span><span class="sxs-lookup"><span data-stu-id="e721a-723">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="e721a-724">Nespoléhá se na nebo důvěřujete `FileName` vlastnosti <xref:Microsoft.AspNetCore.Http.IFormFile> bez ověření.</span><span class="sxs-lookup"><span data-stu-id="e721a-724">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="e721a-725">`FileName`Vlastnost by měla být použita pouze pro účely zobrazení a pouze po kódování HTML.</span><span class="sxs-lookup"><span data-stu-id="e721a-725">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="e721a-726">Uvedené příklady nevezmou ohled na zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="e721a-726">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="e721a-727">Další informace jsou k dispozici v následujících částech a [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="e721a-727">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="e721a-728">Důležité informace o zabezpečení</span><span class="sxs-lookup"><span data-stu-id="e721a-728">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="e721a-729">Ověřování</span><span class="sxs-lookup"><span data-stu-id="e721a-729">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="e721a-730">Nahrávání velkých souborů pomocí streamování</span><span class="sxs-lookup"><span data-stu-id="e721a-730">Upload large files with streaming</span></span>

<span data-ttu-id="e721a-731">Následující příklad ukazuje, jak použít JavaScript ke streamování souboru do akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="e721a-731">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="e721a-732">Token proti padělání souboru se generuje pomocí vlastního atributu filtru a předává se do hlaviček protokolu HTTP klienta místo v textu žádosti.</span><span class="sxs-lookup"><span data-stu-id="e721a-732">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="e721a-733">Vzhledem k tomu, že metoda akce zpracovává nahraná data přímo, vazba modelu formuláře je zakázána jiným vlastním filtrem.</span><span class="sxs-lookup"><span data-stu-id="e721a-733">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="e721a-734">V rámci akce je obsah formuláře čten pomocí `MultipartReader` , který čte každou jednotlivou osobu `MultipartSection` , zpracovává soubor nebo ukládá obsah podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="e721a-734">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="e721a-735">Po načtení oddílů s více částmi provede akce vlastní vazbu modelu.</span><span class="sxs-lookup"><span data-stu-id="e721a-735">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="e721a-736">Počáteční odpověď stránky načte formulář a uloží token proti padělání do cookie (prostřednictvím `GenerateAntiforgeryTokenCookieAttribute` atributu).</span><span class="sxs-lookup"><span data-stu-id="e721a-736">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="e721a-737">Atribut používá ASP.NET Core integrovanou [podporu proti padělání](xref:security/anti-request-forgery) pro nastavení cookie s tokenem požadavku:</span><span class="sxs-lookup"><span data-stu-id="e721a-737">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="e721a-738">`DisableFormValueModelBindingAttribute`Slouží k zakázání vazby modelu:</span><span class="sxs-lookup"><span data-stu-id="e721a-738">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="e721a-739">V ukázkové aplikaci a používá `GenerateAntiforgeryTokenCookieAttribute` se `DisableFormValueModelBindingAttribute` jako filtr pro modelové aplikace stránky `/StreamedSingleFileUploadDb` a `/StreamedSingleFileUploadPhysical` v `Startup.ConfigureServices` [ Razor konvencích použití stránek](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="e721a-739">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="e721a-740">Vzhledem k tomu, že vazba modelu nepřečte formulář, parametry, které jsou svázané z formuláře, se nezobrazují (budou pokračovat v práci s dotazem, trasou a hlavičkou).</span><span class="sxs-lookup"><span data-stu-id="e721a-740">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="e721a-741">Metoda Action pracuje přímo s `Request` vlastností.</span><span class="sxs-lookup"><span data-stu-id="e721a-741">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="e721a-742">`MultipartReader`Slouží ke čtení jednotlivých oddílů.</span><span class="sxs-lookup"><span data-stu-id="e721a-742">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="e721a-743">Data klíč/hodnota jsou uložena v `KeyValueAccumulator` .</span><span class="sxs-lookup"><span data-stu-id="e721a-743">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="e721a-744">Po načtení oddílů s více částmi se obsah `KeyValueAccumulator` používá pro svázání dat formuláře s typem modelu.</span><span class="sxs-lookup"><span data-stu-id="e721a-744">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="e721a-745">Úplná `StreamingController.UploadDatabase` metoda pro streamování do databáze s EF Core:</span><span class="sxs-lookup"><span data-stu-id="e721a-745">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="e721a-746">`MultipartRequestHelper` (*Nástroje/MultipartRequestHelper. cs*):</span><span class="sxs-lookup"><span data-stu-id="e721a-746">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="e721a-747">Úplná `StreamingController.UploadPhysical` metoda pro streamování do fyzického umístění:</span><span class="sxs-lookup"><span data-stu-id="e721a-747">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="e721a-748">V ukázkové aplikaci jsou kontroly ověřování zpracovávány nástrojem `FileHelpers.ProcessStreamedFile` .</span><span class="sxs-lookup"><span data-stu-id="e721a-748">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="e721a-749">Ověřování</span><span class="sxs-lookup"><span data-stu-id="e721a-749">Validation</span></span>

<span data-ttu-id="e721a-750">Třída ukázkové aplikace `FileHelpers` ukazuje několik kontrol ukládání souborů do vyrovnávací paměti <xref:Microsoft.AspNetCore.Http.IFormFile> a datových proudů při nahrávání.</span><span class="sxs-lookup"><span data-stu-id="e721a-750">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="e721a-751">Informace o zpracování <xref:Microsoft.AspNetCore.Http.IFormFile> ukládání souborů do vyrovnávací paměti v ukázkové aplikaci naleznete v `ProcessFormFile` metodě v souboru *Utilities/Helper. cs* .</span><span class="sxs-lookup"><span data-stu-id="e721a-751">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="e721a-752">Pro zpracování streamované soubory se podívejte na `ProcessStreamedFile` metodu ve stejném souboru.</span><span class="sxs-lookup"><span data-stu-id="e721a-752">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="e721a-753">Metody zpracování ověřování, které jsou znázorněné v ukázkové aplikaci, nekontrolují obsah nahraných souborů.</span><span class="sxs-lookup"><span data-stu-id="e721a-753">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="e721a-754">Ve většině produkčních scénářů se v souboru používá rozhraní API pro skenování virů nebo malwaru, než je soubor dostupný uživatelům nebo jiným systémům.</span><span class="sxs-lookup"><span data-stu-id="e721a-754">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="e721a-755">I když ukázka tématu poskytuje pracovní příklad technik ověřování, Neimplementujte `FileHelpers` třídu v produkční aplikaci, pokud:</span><span class="sxs-lookup"><span data-stu-id="e721a-755">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="e721a-756">Plně rozumíte implementaci.</span><span class="sxs-lookup"><span data-stu-id="e721a-756">Fully understand the implementation.</span></span>
> * <span data-ttu-id="e721a-757">Upravte implementaci podle potřeby pro prostředí a specifikace aplikace.</span><span class="sxs-lookup"><span data-stu-id="e721a-757">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="e721a-758">**Nikdy nepoužívejte nerozlišený kód zabezpečení v aplikaci bez nutnosti řešit tyto požadavky.**</span><span class="sxs-lookup"><span data-stu-id="e721a-758">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="e721a-759">Ověření obsahu</span><span class="sxs-lookup"><span data-stu-id="e721a-759">Content validation</span></span>

<span data-ttu-id="e721a-760">**Pro nahraný obsah použijte rozhraní API pro kontrolu virů a malwaru třetí strany.**</span><span class="sxs-lookup"><span data-stu-id="e721a-760">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="e721a-761">Prohledávání souborů je náročné na prostředky serveru ve scénářích s vysokým objemem.</span><span class="sxs-lookup"><span data-stu-id="e721a-761">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="e721a-762">Pokud dojde ke snížení výkonu zpracování požadavků z důvodu kontroly souborů, zvažte přesměrování práce skenování na službu na [pozadí](xref:fundamentals/host/hosted-services), případně služby spuštěné na serveru, který se liší od serveru aplikace.</span><span class="sxs-lookup"><span data-stu-id="e721a-762">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="e721a-763">Nahrané soubory se obvykle uchovávají v oblasti v karanténě, dokud je kontrola virů na pozadí nevrátí.</span><span class="sxs-lookup"><span data-stu-id="e721a-763">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="e721a-764">Když soubor projde, soubor se přesune do normálního umístění úložiště souborů.</span><span class="sxs-lookup"><span data-stu-id="e721a-764">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="e721a-765">Tyto kroky se obvykle provádí ve spojení s databázovým záznamem, který indikuje stav kontroly souboru.</span><span class="sxs-lookup"><span data-stu-id="e721a-765">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="e721a-766">Při použití takového přístupu zůstane aplikace a Server App zaměřené na reakci na požadavky.</span><span class="sxs-lookup"><span data-stu-id="e721a-766">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="e721a-767">Ověření přípony souboru</span><span class="sxs-lookup"><span data-stu-id="e721a-767">File extension validation</span></span>

<span data-ttu-id="e721a-768">Přípona nahraného souboru by měla být zaškrtnutá na seznamu povolených rozšíření.</span><span class="sxs-lookup"><span data-stu-id="e721a-768">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="e721a-769">Příklad:</span><span class="sxs-lookup"><span data-stu-id="e721a-769">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="e721a-770">Ověření podpisu souboru</span><span class="sxs-lookup"><span data-stu-id="e721a-770">File signature validation</span></span>

<span data-ttu-id="e721a-771">Podpis souboru se určuje na prvních několika bajtech na začátku souboru.</span><span class="sxs-lookup"><span data-stu-id="e721a-771">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="e721a-772">Tyto bajty lze použít k určení, zda přípona odpovídá obsahu souboru.</span><span class="sxs-lookup"><span data-stu-id="e721a-772">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="e721a-773">Ukázková aplikace zkontroluje podpisy souborů pro několik běžných typů souborů.</span><span class="sxs-lookup"><span data-stu-id="e721a-773">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="e721a-774">V následujícím příkladu se pro tento soubor kontroluje podpis souboru obrázku JPEG:</span><span class="sxs-lookup"><span data-stu-id="e721a-774">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="e721a-775">Další signatury souborů získáte v dokumentaci [signatury souborů](https://www.filesignatures.net/) a oficiálních souborů.</span><span class="sxs-lookup"><span data-stu-id="e721a-775">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="e721a-776">Zabezpečení názvu souboru</span><span class="sxs-lookup"><span data-stu-id="e721a-776">File name security</span></span>

<span data-ttu-id="e721a-777">Nikdy nepoužívejte název souboru dodaný klientem pro uložení souboru do fyzického úložiště.</span><span class="sxs-lookup"><span data-stu-id="e721a-777">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="e721a-778">Vytvořte bezpečný název souboru pro soubor pomocí [cesty. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) nebo [cesty. GetTempFileName](xref:System.IO.Path.GetTempFileName*) a vytvořte úplnou cestu (včetně názvu souboru) pro dočasné úložiště.</span><span class="sxs-lookup"><span data-stu-id="e721a-778">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="e721a-779">Razor Automatické kódování HTML kóduje hodnoty vlastností pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="e721a-779">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="e721a-780">Následující kód je bezpečné použít:</span><span class="sxs-lookup"><span data-stu-id="e721a-780">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="e721a-781">Mimo Razor , vždy <xref:System.Net.WebUtility.HtmlEncode*> obsah názvu souboru z požadavku uživatele.</span><span class="sxs-lookup"><span data-stu-id="e721a-781">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="e721a-782">Mnoho implementací musí zahrnovat kontrolu, že soubor existuje. v opačném případě je soubor přepsán souborem se stejným názvem.</span><span class="sxs-lookup"><span data-stu-id="e721a-782">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="e721a-783">Poskytněte další logiku pro splnění specifikací vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="e721a-783">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="e721a-784">Ověřování velikosti</span><span class="sxs-lookup"><span data-stu-id="e721a-784">Size validation</span></span>

<span data-ttu-id="e721a-785">Omezte velikost nahraných souborů.</span><span class="sxs-lookup"><span data-stu-id="e721a-785">Limit the size of uploaded files.</span></span>

<span data-ttu-id="e721a-786">V ukázkové aplikaci je velikost souboru omezená na 2 MB (uvedené v bajtech).</span><span class="sxs-lookup"><span data-stu-id="e721a-786">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="e721a-787">Limit se poskytuje prostřednictvím [Konfigurace](xref:fundamentals/configuration/index) z *appsettings.jsv* souboru:</span><span class="sxs-lookup"><span data-stu-id="e721a-787">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="e721a-788">`FileSizeLimit`Třída je vložena do `PageModel` tříd:</span><span class="sxs-lookup"><span data-stu-id="e721a-788">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="e721a-789">Když velikost souboru překročí limit, soubor se odmítne:</span><span class="sxs-lookup"><span data-stu-id="e721a-789">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="e721a-790">Porovnává hodnotu atributu name s parametrem název metody POST</span><span class="sxs-lookup"><span data-stu-id="e721a-790">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="e721a-791">V Razor neformulářích, které publikují data formuláře nebo přímo využívají JavaScript `FormData` , název zadaný v prvku formuláře nebo `FormData` musí odpovídat názvu parametru v akci kontroleru.</span><span class="sxs-lookup"><span data-stu-id="e721a-791">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="e721a-792">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="e721a-792">In the following example:</span></span>

* <span data-ttu-id="e721a-793">Při použití `<input>` elementu `name` je atribut nastaven na hodnotu `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="e721a-793">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="e721a-794">Při použití `FormData` v jazyce JavaScript je název nastaven na hodnotu `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="e721a-794">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="e721a-795">Pro parametr metody jazyka C# () použijte stejný název `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="e721a-795">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="e721a-796">Pro Razor metodu obslužné rutiny stránky stránky s názvem `Upload` :</span><span class="sxs-lookup"><span data-stu-id="e721a-796">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="e721a-797">Pro metodu akce po kontroléru MVC:</span><span class="sxs-lookup"><span data-stu-id="e721a-797">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="e721a-798">Konfigurace serveru a aplikace</span><span class="sxs-lookup"><span data-stu-id="e721a-798">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="e721a-799">Omezení délky těla částí</span><span class="sxs-lookup"><span data-stu-id="e721a-799">Multipart body length limit</span></span>

<span data-ttu-id="e721a-800"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> nastaví limit délky jednotlivých částí části.</span><span class="sxs-lookup"><span data-stu-id="e721a-800"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="e721a-801">Oddíly formuláře, které překračují toto omezení, vyvolávají <xref:System.IO.InvalidDataException> při analýze.</span><span class="sxs-lookup"><span data-stu-id="e721a-801">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="e721a-802">Výchozí hodnota je 134 217 728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="e721a-802">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="e721a-803">Upravte limit pomocí <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> nastavení v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e721a-803">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="e721a-804"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> slouží k nastavení <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> pro jednu stránku nebo akci.</span><span class="sxs-lookup"><span data-stu-id="e721a-804"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="e721a-805">V Razor aplikaci Pages použijte filtr s [konvencí](xref:razor-pages/razor-pages-conventions) v nástroji `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e721a-805">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="e721a-806">V Razor aplikaci Pages nebo aplikaci MVC použijte filtr na model stránky nebo metodu akce:</span><span class="sxs-lookup"><span data-stu-id="e721a-806">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="e721a-807">Kestrel maximální velikost textu požadavku</span><span class="sxs-lookup"><span data-stu-id="e721a-807">Kestrel maximum request body size</span></span>

<span data-ttu-id="e721a-808">Pro aplikace hostované v Kestrel je výchozí maximální velikost textu požadavku 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="e721a-808">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="e721a-809">Přizpůsobte limit pomocí možnosti serveru [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:</span><span class="sxs-lookup"><span data-stu-id="e721a-809">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="e721a-810"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> slouží k nastavení [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) pro jednu stránku nebo akci.</span><span class="sxs-lookup"><span data-stu-id="e721a-810"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="e721a-811">V Razor aplikaci Pages použijte filtr s [konvencí](xref:razor-pages/razor-pages-conventions) v nástroji `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e721a-811">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="e721a-812">V Razor aplikaci Pages nebo aplikaci MVC použijte filtr na třídu obslužné rutiny stránky nebo na metodu akce:</span><span class="sxs-lookup"><span data-stu-id="e721a-812">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="e721a-813">Další omezení Kestrel</span><span class="sxs-lookup"><span data-stu-id="e721a-813">Other Kestrel limits</span></span>

<span data-ttu-id="e721a-814">Pro aplikace hostované v Kestrel se můžou vztahovat další omezení Kestrel:</span><span class="sxs-lookup"><span data-stu-id="e721a-814">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="e721a-815">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="e721a-815">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="e721a-816">Sazby za data požadavků a odpovědí</span><span class="sxs-lookup"><span data-stu-id="e721a-816">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a><span data-ttu-id="e721a-817">IIS</span><span class="sxs-lookup"><span data-stu-id="e721a-817">IIS</span></span>

<span data-ttu-id="e721a-818">Výchozí limit počtu požadavků ( `maxAllowedContentLength` ) je 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="e721a-818">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="e721a-819">Přizpůsobte limit v `web.config` souboru.</span><span class="sxs-lookup"><span data-stu-id="e721a-819">Customize the limit in the `web.config` file.</span></span> <span data-ttu-id="e721a-820">V následujícím příkladu je limit nastaven na 50 MB (52 428 800 bajtů):</span><span class="sxs-lookup"><span data-stu-id="e721a-820">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="e721a-821">`maxAllowedContentLength`Nastavení platí pouze pro službu IIS.</span><span class="sxs-lookup"><span data-stu-id="e721a-821">The `maxAllowedContentLength` setting only applies to IIS.</span></span> <span data-ttu-id="e721a-822">Další informace najdete v tématu [omezení `<requestLimits>` požadavků ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="e721a-822">For more information, see [Request Limits `<requestLimits>`](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="e721a-823">Zvyšte maximální velikost textu požadavku pro požadavek HTTP nastavením <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="e721a-823">Increase the maximum request body size for the HTTP request by setting <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e721a-824">V následujícím příkladu je limit nastaven na 50 MB (52 428 800 bajtů):</span><span class="sxs-lookup"><span data-stu-id="e721a-824">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

<span data-ttu-id="e721a-825">Další informace naleznete v tématu <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="e721a-825">For more information, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="e721a-826">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="e721a-826">Troubleshoot</span></span>

<span data-ttu-id="e721a-827">Níže jsou uvedeny některé běžné problémy, které se vyskytly při práci s nahráváním souborů a jejich možnými řešeními.</span><span class="sxs-lookup"><span data-stu-id="e721a-827">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="e721a-828">Při nasazení na server služby IIS se nenašla chyba.</span><span class="sxs-lookup"><span data-stu-id="e721a-828">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="e721a-829">Následující chyba znamená, že nahraný soubor překračuje délku nakonfigurovaného obsahu serveru:</span><span class="sxs-lookup"><span data-stu-id="e721a-829">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="e721a-830">Další informace najdete v části [IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="e721a-830">For more information, see the [IIS](#iis) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="e721a-831">Chyba připojení</span><span class="sxs-lookup"><span data-stu-id="e721a-831">Connection failure</span></span>

<span data-ttu-id="e721a-832">Chyba připojení a připojení k serveru pro resetování pravděpodobně znamená, že nahraný soubor překračuje maximální velikost textu požadavku Kestrel.</span><span class="sxs-lookup"><span data-stu-id="e721a-832">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="e721a-833">Další informace najdete v části [Kestrel maximální velikost textu požadavku](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="e721a-833">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="e721a-834">Omezení připojení klientů Kestrel mohou také vyžadovat úpravu.</span><span class="sxs-lookup"><span data-stu-id="e721a-834">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="e721a-835">Výjimka odkazu s hodnotou null s IFormFile</span><span class="sxs-lookup"><span data-stu-id="e721a-835">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="e721a-836">Pokud kontroler přijímá odeslané soubory pomocí <xref:Microsoft.AspNetCore.Http.IFormFile> , ale hodnota je `null` , potvrďte, že formulář HTML určuje `enctype` hodnotu `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="e721a-836">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="e721a-837">Pokud tento atribut není nastaven na `<form>` elementu, odeslání souboru neproběhne a jakékoli vázané <xref:Microsoft.AspNetCore.Http.IFormFile> argumenty jsou `null` .</span><span class="sxs-lookup"><span data-stu-id="e721a-837">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="e721a-838">Ujistěte se také, že [nahrávání názvů v datech formuláře odpovídá pojmenování aplikace](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="e721a-838">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="e721a-839">Proud je příliš dlouhý.</span><span class="sxs-lookup"><span data-stu-id="e721a-839">Stream was too long</span></span>

<span data-ttu-id="e721a-840">Příklady v tomto tématu <xref:System.IO.MemoryStream> se spoléhají na uchovávání obsahu nahraného souboru.</span><span class="sxs-lookup"><span data-stu-id="e721a-840">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="e721a-841">Omezení velikosti `MemoryStream` je `int.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="e721a-841">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="e721a-842">Pokud scénář nahrávání souborů aplikace vyžaduje, aby byl obsah souboru větší než 50 MB, použijte alternativní přístup, který nespoléhá na jednu z nich `MemoryStream` pro uchovávání obsahu nahraného souboru.</span><span class="sxs-lookup"><span data-stu-id="e721a-842">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="e721a-843">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="e721a-843">Additional resources</span></span>

* [<span data-ttu-id="e721a-844">Vyprazdňování žádosti o připojení HTTP</span><span class="sxs-lookup"><span data-stu-id="e721a-844">HTTP connection request draining</span></span>](xref:fundamentals/servers/kestrel#http11-request-draining)
* [<span data-ttu-id="e721a-845">Neomezená nahrávání souboru</span><span class="sxs-lookup"><span data-stu-id="e721a-845">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
* [<span data-ttu-id="e721a-846">Zabezpečení Azure: rámec zabezpečení: ověření vstupu | Hrozeb</span><span class="sxs-lookup"><span data-stu-id="e721a-846">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="e721a-847">Vzory návrhu cloudu Azure: vzor osobního Key</span><span class="sxs-lookup"><span data-stu-id="e721a-847">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
