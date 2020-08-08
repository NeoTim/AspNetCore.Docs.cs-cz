---
title: Nahrání souborů v ASP.NET Core
author: rick-anderson
description: Jak používat vazbu modelu a streamování k nahrávání souborů v ASP.NET Core MVC
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/03/2020
no-loc:
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
ms.openlocfilehash: a11e6325143b9db57d6fbd1cd67478dc1dd6122d
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021247"
---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="fdbd3-103">Nahrání souborů v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fdbd3-103">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="fdbd3-104">[Steve Smith](https://ardalis.com/) a [Rutgerá](https://github.com/rutix) zaplavení</span><span class="sxs-lookup"><span data-stu-id="fdbd3-104">By [Steve Smith](https://ardalis.com/) and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fdbd3-105">ASP.NET Core podporuje nahrávání jednoho nebo více souborů pomocí vazby modelu ve vyrovnávací paměti pro menší soubory a streamování bez vyrovnávací paměti pro větší soubory.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-105">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="fdbd3-106">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fdbd3-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="fdbd3-107">Důležité informace o zabezpečení</span><span class="sxs-lookup"><span data-stu-id="fdbd3-107">Security considerations</span></span>

<span data-ttu-id="fdbd3-108">Pokud chcete uživatelům poskytnout možnost nahrávat soubory na server, buďte opatrní.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-108">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="fdbd3-109">Útočníci se můžou pokusit:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-109">Attackers may attempt to:</span></span>

* <span data-ttu-id="fdbd3-110">Vykoná útok [DOS (Denial of Service](/windows-hardware/drivers/ifs/denial-of-service) ).</span><span class="sxs-lookup"><span data-stu-id="fdbd3-110">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="fdbd3-111">Nahrání virů nebo malwaru</span><span class="sxs-lookup"><span data-stu-id="fdbd3-111">Upload viruses or malware.</span></span>
* <span data-ttu-id="fdbd3-112">Narušit sítě a servery jinými způsoby.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-112">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="fdbd3-113">Bezpečnostní kroky, které snižují pravděpodobnost úspěšného útoku, jsou:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-113">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="fdbd3-114">Nahrajte soubory do vyhrazené oblasti pro nahrávání souborů, nejlépe do nesystémové jednotky.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-114">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="fdbd3-115">Vyhrazené umístění usnadňuje omezení zabezpečení pro nahrané soubory.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-115">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="fdbd3-116">Zakažte oprávnění EXECUTE pro umístění pro nahrání souboru.&dagger;</span><span class="sxs-lookup"><span data-stu-id="fdbd3-116">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="fdbd3-117">Neuchovávat nahrané soubory ve stejném stromu adresářů jako aplikace. **not**&dagger;</span><span class="sxs-lookup"><span data-stu-id="fdbd3-117">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="fdbd3-118">Použijte název bezpečného souboru určený aplikací.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-118">Use a safe file name determined by the app.</span></span> <span data-ttu-id="fdbd3-119">Nepoužívejte název souboru poskytnutý uživatelem nebo nedůvěryhodného názvu nahraného souboru. &dagger; HTML při zobrazení kódování názvu nedůvěryhodného souboru.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-119">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="fdbd3-120">Například protokolování názvu souboru nebo zobrazení v uživatelském rozhraní ( Razor Automatické kódování HTML kódování).</span><span class="sxs-lookup"><span data-stu-id="fdbd3-120">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="fdbd3-121">Povolte pro specifikaci návrhu aplikace jenom schválené přípony souborů.&dagger;</span><span class="sxs-lookup"><span data-stu-id="fdbd3-121">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="fdbd3-122">Ověřte, zda jsou na serveru provedeny kontroly na straně klienta. &dagger; Kontroly na straně klienta je snadné obejít.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-122">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="fdbd3-123">Ověřte velikost nahraného souboru.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-123">Check the size of an uploaded file.</span></span> <span data-ttu-id="fdbd3-124">Nastavte limit maximální velikosti, aby se zabránilo velkým nahrávání.&dagger;</span><span class="sxs-lookup"><span data-stu-id="fdbd3-124">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="fdbd3-125">Pokud by soubory neměly být přepsány nahraným souborem se stejným názvem, před nahráním souboru ověřte název souboru proti databázi nebo fyzickému úložišti.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-125">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="fdbd3-126">**Před uložením souboru spusťte v nahraném obsahu skener virů nebo malwaru.**</span><span class="sxs-lookup"><span data-stu-id="fdbd3-126">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="fdbd3-127">&dagger;Ukázková aplikace předvádí přístup, který splňuje kritéria.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-127">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="fdbd3-128">Nahrávání škodlivého kódu do systému je často prvním krokem ke spuštění kódu, který může:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-128">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="fdbd3-129">Zcela získá kontrolu nad systémem.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-129">Completely gain control of a system.</span></span>
> * <span data-ttu-id="fdbd3-130">Přetížit systém s výsledkem, že dojde k chybě systému.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-130">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="fdbd3-131">Napadnout data uživatelů nebo systémových dat.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-131">Compromise user or system data.</span></span>
> * <span data-ttu-id="fdbd3-132">Použijte graffiti pro veřejné uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-132">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="fdbd3-133">Informace o omezení oblasti útoku při přijímání souborů uživateli najdete v následujících zdrojích informací:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-133">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="fdbd3-134">Neomezená nahrávání souboru</span><span class="sxs-lookup"><span data-stu-id="fdbd3-134">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="fdbd3-135">Zabezpečení Azure: Ujistěte se, že jsou při přijímání souborů od uživatelů k dismístě správné ovládací prvky.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-135">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="fdbd3-136">Další informace o implementaci bezpečnostních opatření, včetně příkladů z ukázkové aplikace, najdete v části [ověření](#validation) .</span><span class="sxs-lookup"><span data-stu-id="fdbd3-136">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="fdbd3-137">Scénáře úložiště</span><span class="sxs-lookup"><span data-stu-id="fdbd3-137">Storage scenarios</span></span>

<span data-ttu-id="fdbd3-138">Mezi běžné možnosti úložiště pro soubory patří:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-138">Common storage options for files include:</span></span>

* <span data-ttu-id="fdbd3-139">Databáze</span><span class="sxs-lookup"><span data-stu-id="fdbd3-139">Database</span></span>

  * <span data-ttu-id="fdbd3-140">U malých nahrávání souborů je databáze často rychlejší než možnosti fyzického úložiště (systému souborů nebo síťového sdílení).</span><span class="sxs-lookup"><span data-stu-id="fdbd3-140">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="fdbd3-141">Databáze je často pohodlnější než možnosti fyzického úložiště, protože načtení záznamu databáze pro uživatelská data může současně poskytovat obsah souboru (například obrázek miniatury).</span><span class="sxs-lookup"><span data-stu-id="fdbd3-141">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="fdbd3-142">Databáze je potenciálně levnější než použití služby úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-142">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="fdbd3-143">Fyzické úložiště (systém souborů nebo síťová sdílená složka)</span><span class="sxs-lookup"><span data-stu-id="fdbd3-143">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="fdbd3-144">Pro nahrávání velkých souborů:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-144">For large file uploads:</span></span>
    * <span data-ttu-id="fdbd3-145">Omezení databáze mohou omezit velikost nahrávání.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-145">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="fdbd3-146">Fyzické úložiště je často méně hospodárné než úložiště v databázi.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-146">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="fdbd3-147">Fyzické úložiště je potenciálně levnější než použití služby úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-147">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="fdbd3-148">Proces aplikace musí mít oprávnění ke čtení a zápisu do umístění úložiště.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-148">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="fdbd3-149">**Nikdy neudělujte oprávnění EXECUTE.**</span><span class="sxs-lookup"><span data-stu-id="fdbd3-149">**Never grant execute permission.**</span></span>

* <span data-ttu-id="fdbd3-150">Služba úložiště dat (například [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="fdbd3-150">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="fdbd3-151">Služby obvykle nabízejí vylepšenou škálovatelnost a odolnost proti místním řešením, které obvykle podléhají jednomu bodu selhání.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-151">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="fdbd3-152">Služby jsou potenciálně nižší náklady ve scénářích infrastruktury velkých úložišť.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-152">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="fdbd3-153">Další informace najdete v tématu [rychlý Start: použití .NET k vytvoření objektu BLOB v úložišti objektů](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="fdbd3-153">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="fdbd3-154">Scénáře nahrávání souborů</span><span class="sxs-lookup"><span data-stu-id="fdbd3-154">File upload scenarios</span></span>

<span data-ttu-id="fdbd3-155">Dva obecné přístupy k nahrávání souborů jsou ukládání do vyrovnávací paměti a streamování.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-155">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="fdbd3-156">**Do vyrovnávací paměti**</span><span class="sxs-lookup"><span data-stu-id="fdbd3-156">**Buffering**</span></span>

<span data-ttu-id="fdbd3-157">Celý soubor je načten do <xref:Microsoft.AspNetCore.Http.IFormFile> , což je reprezentace souboru, který se používá ke zpracování nebo uložení souboru v jazyce C#.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-157">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="fdbd3-158">Prostředky (disk, paměť) používané při nahrávání souborů závisí na počtu a velikosti souběžných nahrávání souborů.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-158">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="fdbd3-159">Pokud se aplikace pokusí do vyrovnávací paměti příliš mnoho nahrávání, dojde k selhání lokality, když dojde k vynechání paměti nebo místa na disku.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-159">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="fdbd3-160">Pokud velikost nebo frekvence nahrávání souborů vyčerpá prostředky aplikace, použijte streamování.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-160">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="fdbd3-161">Z paměti do dočasného souboru na disku se přesune libovolný soubor s vyrovnávací pamětí větší než 64 KB.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-161">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="fdbd3-162">Ukládání malých souborů do vyrovnávací paměti je popsáno v následujících částech tohoto tématu:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-162">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="fdbd3-163">Fyzické úložiště</span><span class="sxs-lookup"><span data-stu-id="fdbd3-163">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="fdbd3-164">Database</span><span class="sxs-lookup"><span data-stu-id="fdbd3-164">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="fdbd3-165">**Streamování**</span><span class="sxs-lookup"><span data-stu-id="fdbd3-165">**Streaming**</span></span>

<span data-ttu-id="fdbd3-166">Soubor se přijímá z požadavku na více částí a přímo se zpracovává nebo ukládá v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-166">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="fdbd3-167">Streamování nijak významně nezvyšuje výkon.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-167">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="fdbd3-168">Streamování snižuje nároky na paměť nebo místo na disku při nahrávání souborů.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-168">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="fdbd3-169">Streamování velkých souborů je zahrnuté v části [nahrávání velkých souborů pomocí streamování](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="fdbd3-169">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="fdbd3-170">Nahrávání malých souborů s vazbou modelu ve vyrovnávací paměti na fyzické úložiště</span><span class="sxs-lookup"><span data-stu-id="fdbd3-170">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="fdbd3-171">Pro nahrání malých souborů použijte formulář s více částmi nebo sestavte požadavek POST pomocí JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-171">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="fdbd3-172">Následující příklad ukazuje použití Razor formuláře stránky k nahrání jednoho souboru (*Pages/BufferedSingleFileUploadPhysical. cshtml* do ukázkové aplikace):</span><span class="sxs-lookup"><span data-stu-id="fdbd3-172">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="fdbd3-173">Následující příklad je podobný předchozímu příkladu s tím rozdílem, že:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-173">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="fdbd3-174">K odeslání dat formuláře se používá JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)).</span><span class="sxs-lookup"><span data-stu-id="fdbd3-174">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="fdbd3-175">Neexistuje žádné ověření.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-175">There's no validation.</span></span>

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

<span data-ttu-id="fdbd3-176">Chcete-li provést příspěvek formuláře v jazyce JavaScript pro klienty, kteří [nepodporují rozhraní API pro načítání](https://caniuse.com/#feat=fetch), použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-176">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="fdbd3-177">Použijte načtenou výplň (například [window. Fetch Fill (GitHub/Fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="fdbd3-177">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="fdbd3-178">Použijte `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-178">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="fdbd3-179">Například:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-179">For example:</span></span>

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

<span data-ttu-id="fdbd3-180">Aby bylo možné podporovat nahrávání souborů, musí formuláře HTML určovat typ kódování ( `enctype` ) `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="fdbd3-180">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="fdbd3-181">Pro `files` vstupní element, který podporuje nahrávání více souborů, poskytněte `multiple` atribut `<input>` elementu:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-181">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="fdbd3-182">Jednotlivé soubory nahrané na server jsou k dispozici prostřednictvím [vazby modelu](xref:mvc/models/model-binding) pomocí <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="fdbd3-182">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="fdbd3-183">Ukázková aplikace ukazuje více ukládání souborů do vyrovnávací paměti pro scénáře databáze a fyzických úložišť.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-183">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="fdbd3-184">Nepoužívejte **not** `FileName` vlastnost <xref:Microsoft.AspNetCore.Http.IFormFile> jinou než pro zobrazení a protokolování.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-184">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="fdbd3-185">Při zobrazení nebo protokolování je název souboru kódován HTML.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-185">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="fdbd3-186">Útočník může poskytnout škodlivý název souboru, včetně úplných cest nebo relativních cest.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-186">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="fdbd3-187">Aplikace by měly:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-187">Applications should:</span></span>
>
> * <span data-ttu-id="fdbd3-188">Odeberte cestu z názvu souboru zadaného uživatelem.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-188">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="fdbd3-189">Uložte název souboru s příponou PATH s kódováním HTML pro uživatelské rozhraní nebo protokolování.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-189">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="fdbd3-190">Vygenerujte nový náhodný název souboru pro úložiště.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-190">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="fdbd3-191">Následující kód odstraní cestu z názvu souboru:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-191">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="fdbd3-192">Zde uvedené příklady neberou ohled na zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-192">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="fdbd3-193">Další informace jsou k dispozici v následujících částech a [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="fdbd3-193">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="fdbd3-194">Důležité informace o zabezpečení</span><span class="sxs-lookup"><span data-stu-id="fdbd3-194">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="fdbd3-195">Export</span><span class="sxs-lookup"><span data-stu-id="fdbd3-195">Validation</span></span>](#validation)

<span data-ttu-id="fdbd3-196">Při nahrávání souborů pomocí vazby modelu a <xref:Microsoft.AspNetCore.Http.IFormFile> může metoda Action přijmout:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-196">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="fdbd3-197">Jedna <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="fdbd3-197">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="fdbd3-198">Kterákoli z následujících kolekcí, které reprezentují několik souborů:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-198">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="fdbd3-199">[Seznamu](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="fdbd3-199">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="fdbd3-200">Vazba odpovídá souborům formuláře podle názvu.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-200">Binding matches form files by name.</span></span> <span data-ttu-id="fdbd3-201">Například hodnota HTML v se `name` `<input type="file" name="formFile">` musí shodovat s parametrem nebo vazbou vlastnosti jazyka C# ( `FormFile` ).</span><span class="sxs-lookup"><span data-stu-id="fdbd3-201">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="fdbd3-202">Další informace naleznete v části [název atributu matched na název parametru metody post](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="fdbd3-202">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="fdbd3-203">Následující příklad:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-203">The following example:</span></span>

* <span data-ttu-id="fdbd3-204">Projde jedním nebo více nahranými soubory.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-204">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="fdbd3-205">Pomocí [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) vrátí úplnou cestu k souboru, včetně názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-205">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="fdbd3-206">Uloží soubory do místního systému souborů pomocí názvu souboru generovaného aplikací.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-206">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="fdbd3-207">Vrátí celkový počet nahraných souborů a jejich velikost.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-207">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="fdbd3-208">Slouží `Path.GetRandomFileName` k vygenerování názvu souboru bez cesty.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-208">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="fdbd3-209">V následujícím příkladu je cesta získána z konfigurace:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-209">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="fdbd3-210">Cesta předaná do <xref:System.IO.FileStream> *musí* zahrnovat název souboru.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-210">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="fdbd3-211">Pokud není zadán název souboru, <xref:System.UnauthorizedAccessException> je vyvolána za běhu.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-211">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="fdbd3-212">Soubory odeslané pomocí <xref:Microsoft.AspNetCore.Http.IFormFile> techniky jsou v paměti nebo na disku na serveru před zpracováním uloženy do vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-212">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="fdbd3-213">V rámci metody Action je <xref:Microsoft.AspNetCore.Http.IFormFile> obsah přístupný jako <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="fdbd3-213">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="fdbd3-214">Kromě místního systému souborů je možné soubory ukládat do síťové sdílené složky nebo do služby úložiště souborů, jako je [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="fdbd3-214">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="fdbd3-215">Další příklad, který projde několik souborů pro nahrání a používá bezpečné názvy souborů, najdete v ukázkové aplikaci v části *Pages/BufferedMultipleFileUploadPhysical. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="fdbd3-215">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="fdbd3-216">[Cesta. GetTempFileName](xref:System.IO.Path.GetTempFileName*) vyvolá výjimku, <xref:System.IO.IOException> Pokud jsou vytvořeny více než 65 535 souborů bez odstranění předchozích dočasných souborů.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-216">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="fdbd3-217">Limit 65 535 souborů je omezen na server.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-217">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="fdbd3-218">Další informace o tomto limitu pro operační systém Windows najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-218">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="fdbd3-219">GetTempFileNameA – funkce</span><span class="sxs-lookup"><span data-stu-id="fdbd3-219">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="fdbd3-220">Nahrávání malých souborů s vazbou modelu s vyrovnávací pamětí do databáze</span><span class="sxs-lookup"><span data-stu-id="fdbd3-220">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="fdbd3-221">Chcete-li uložit data binárního souboru do databáze pomocí [Entity Framework](/ef/core/index), definujte v <xref:System.Byte> entitě vlastnost Array:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-221">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="fdbd3-222">Zadejte vlastnost modelu stránky pro třídu, která obsahuje <xref:Microsoft.AspNetCore.Http.IFormFile> :</span><span class="sxs-lookup"><span data-stu-id="fdbd3-222">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="fdbd3-223"><xref:Microsoft.AspNetCore.Http.IFormFile>lze ji použít přímo jako parametr metody akce nebo jako vlastnost vázaného modelu.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-223"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="fdbd3-224">Předchozí příklad používá vlastnost vázaného modelu.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-224">The prior example uses a bound model property.</span></span>

<span data-ttu-id="fdbd3-225">`FileUpload`Je použit ve Razor formuláři stránky:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-225">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="fdbd3-226">Když je formulář publikovaný na serveru, zkopírujte <xref:Microsoft.AspNetCore.Http.IFormFile> ho do datového proudu a uložte ho jako pole bajtů v databázi.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-226">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="fdbd3-227">V následujícím příkladu `_dbContext` ukládá kontext databáze aplikace:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-227">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="fdbd3-228">Předchozí příklad je podobný scénáři, který je znázorněný v ukázkové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-228">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="fdbd3-229">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="fdbd3-229">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="fdbd3-230">*Pages/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="fdbd3-230">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="fdbd3-231">Při ukládání binárních dat do relačních databází buďte opatrní, protože to může mít nepříznivý vliv na výkon.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-231">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="fdbd3-232">Nespoléhá se na nebo důvěřujete `FileName` vlastnosti <xref:Microsoft.AspNetCore.Http.IFormFile> bez ověření.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-232">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="fdbd3-233">`FileName`Vlastnost by měla být použita pouze pro účely zobrazení a pouze po kódování HTML.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-233">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="fdbd3-234">Uvedené příklady nevezmou ohled na zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-234">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="fdbd3-235">Další informace jsou k dispozici v následujících částech a [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="fdbd3-235">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="fdbd3-236">Důležité informace o zabezpečení</span><span class="sxs-lookup"><span data-stu-id="fdbd3-236">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="fdbd3-237">Export</span><span class="sxs-lookup"><span data-stu-id="fdbd3-237">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="fdbd3-238">Nahrávání velkých souborů pomocí streamování</span><span class="sxs-lookup"><span data-stu-id="fdbd3-238">Upload large files with streaming</span></span>

<span data-ttu-id="fdbd3-239">Následující příklad ukazuje, jak použít JavaScript ke streamování souboru do akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-239">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="fdbd3-240">Token proti padělání souboru se generuje pomocí vlastního atributu filtru a předává se do hlaviček protokolu HTTP klienta místo v textu žádosti.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-240">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="fdbd3-241">Vzhledem k tomu, že metoda akce zpracovává nahraná data přímo, vazba modelu formuláře je zakázána jiným vlastním filtrem.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-241">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="fdbd3-242">V rámci akce je obsah formuláře čten pomocí `MultipartReader` , který čte každou jednotlivou osobu `MultipartSection` , zpracovává soubor nebo ukládá obsah podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-242">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="fdbd3-243">Po načtení oddílů s více částmi provede akce vlastní vazbu modelu.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-243">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="fdbd3-244">Počáteční odpověď stránky načte formulář a uloží token proti padělání do cookie (prostřednictvím `GenerateAntiforgeryTokenCookieAttribute` atributu).</span><span class="sxs-lookup"><span data-stu-id="fdbd3-244">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="fdbd3-245">Atribut používá ASP.NET Core integrovanou [podporu proti padělání](xref:security/anti-request-forgery) pro nastavení cookie s tokenem požadavku:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-245">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="fdbd3-246">`DisableFormValueModelBindingAttribute`Slouží k zakázání vazby modelu:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-246">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="fdbd3-247">V ukázkové aplikaci a používá `GenerateAntiforgeryTokenCookieAttribute` se `DisableFormValueModelBindingAttribute` jako filtr pro modelové aplikace stránky `/StreamedSingleFileUploadDb` a `/StreamedSingleFileUploadPhysical` v `Startup.ConfigureServices` [ Razor konvencích použití stránek](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="fdbd3-247">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

<span data-ttu-id="fdbd3-248">Vzhledem k tomu, že vazba modelu nepřečte formulář, parametry, které jsou svázané z formuláře, se nezobrazují (budou pokračovat v práci s dotazem, trasou a hlavičkou).</span><span class="sxs-lookup"><span data-stu-id="fdbd3-248">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="fdbd3-249">Metoda Action pracuje přímo s `Request` vlastností.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-249">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="fdbd3-250">`MultipartReader`Slouží ke čtení jednotlivých oddílů.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-250">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="fdbd3-251">Data klíč/hodnota jsou uložena v `KeyValueAccumulator` .</span><span class="sxs-lookup"><span data-stu-id="fdbd3-251">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="fdbd3-252">Po načtení oddílů s více částmi se obsah `KeyValueAccumulator` používá pro svázání dat formuláře s typem modelu.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-252">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="fdbd3-253">Úplná `StreamingController.UploadDatabase` metoda pro streamování do databáze s EF Core:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-253">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="fdbd3-254">`MultipartRequestHelper`(*Nástroje/MultipartRequestHelper. cs*):</span><span class="sxs-lookup"><span data-stu-id="fdbd3-254">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="fdbd3-255">Úplná `StreamingController.UploadPhysical` metoda pro streamování do fyzického umístění:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-255">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="fdbd3-256">V ukázkové aplikaci jsou kontroly ověřování zpracovávány nástrojem `FileHelpers.ProcessStreamedFile` .</span><span class="sxs-lookup"><span data-stu-id="fdbd3-256">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="fdbd3-257">Ověřování vstupů (validace)</span><span class="sxs-lookup"><span data-stu-id="fdbd3-257">Validation</span></span>

<span data-ttu-id="fdbd3-258">Třída ukázkové aplikace `FileHelpers` ukazuje několik kontrol ukládání souborů do vyrovnávací paměti <xref:Microsoft.AspNetCore.Http.IFormFile> a datových proudů při nahrávání.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-258">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="fdbd3-259">Informace o zpracování <xref:Microsoft.AspNetCore.Http.IFormFile> ukládání souborů do vyrovnávací paměti v ukázkové aplikaci naleznete v `ProcessFormFile` metodě v souboru *Utilities/Helper. cs* .</span><span class="sxs-lookup"><span data-stu-id="fdbd3-259">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="fdbd3-260">Pro zpracování streamované soubory se podívejte na `ProcessStreamedFile` metodu ve stejném souboru.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-260">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="fdbd3-261">Metody zpracování ověřování, které jsou znázorněné v ukázkové aplikaci, nekontrolují obsah nahraných souborů.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-261">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="fdbd3-262">Ve většině produkčních scénářů se v souboru používá rozhraní API pro skenování virů nebo malwaru, než je soubor dostupný uživatelům nebo jiným systémům.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-262">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="fdbd3-263">I když ukázka tématu poskytuje pracovní příklad technik ověřování, Neimplementujte `FileHelpers` třídu v produkční aplikaci, pokud:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-263">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="fdbd3-264">Plně rozumíte implementaci.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-264">Fully understand the implementation.</span></span>
> * <span data-ttu-id="fdbd3-265">Upravte implementaci podle potřeby pro prostředí a specifikace aplikace.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-265">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="fdbd3-266">**Nikdy nepoužívejte nerozlišený kód zabezpečení v aplikaci bez nutnosti řešit tyto požadavky.**</span><span class="sxs-lookup"><span data-stu-id="fdbd3-266">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="fdbd3-267">Ověření obsahu</span><span class="sxs-lookup"><span data-stu-id="fdbd3-267">Content validation</span></span>

<span data-ttu-id="fdbd3-268">**Pro nahraný obsah použijte rozhraní API pro kontrolu virů a malwaru třetí strany.**</span><span class="sxs-lookup"><span data-stu-id="fdbd3-268">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="fdbd3-269">Prohledávání souborů je náročné na prostředky serveru ve scénářích s vysokým objemem.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-269">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="fdbd3-270">Pokud dojde ke snížení výkonu zpracování požadavků z důvodu kontroly souborů, zvažte přesměrování práce skenování na službu na [pozadí](xref:fundamentals/host/hosted-services), případně služby spuštěné na serveru, který se liší od serveru aplikace.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-270">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="fdbd3-271">Nahrané soubory se obvykle uchovávají v oblasti v karanténě, dokud je kontrola virů na pozadí nevrátí.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-271">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="fdbd3-272">Když soubor projde, soubor se přesune do normálního umístění úložiště souborů.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-272">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="fdbd3-273">Tyto kroky se obvykle provádí ve spojení s databázovým záznamem, který indikuje stav kontroly souboru.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-273">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="fdbd3-274">Při použití takového přístupu zůstane aplikace a Server App zaměřené na reakci na požadavky.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-274">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="fdbd3-275">Ověření přípony souboru</span><span class="sxs-lookup"><span data-stu-id="fdbd3-275">File extension validation</span></span>

<span data-ttu-id="fdbd3-276">Přípona nahraného souboru by měla být zaškrtnutá na seznamu povolených rozšíření.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-276">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="fdbd3-277">Například:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-277">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="fdbd3-278">Ověření podpisu souboru</span><span class="sxs-lookup"><span data-stu-id="fdbd3-278">File signature validation</span></span>

<span data-ttu-id="fdbd3-279">Podpis souboru se určuje na prvních několika bajtech na začátku souboru.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-279">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="fdbd3-280">Tyto bajty lze použít k určení, zda přípona odpovídá obsahu souboru.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-280">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="fdbd3-281">Ukázková aplikace zkontroluje podpisy souborů pro několik běžných typů souborů.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-281">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="fdbd3-282">V následujícím příkladu se pro tento soubor kontroluje podpis souboru obrázku JPEG:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-282">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="fdbd3-283">Další signatury souborů získáte v dokumentaci [signatury souborů](https://www.filesignatures.net/) a oficiálních souborů.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-283">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="fdbd3-284">Zabezpečení názvu souboru</span><span class="sxs-lookup"><span data-stu-id="fdbd3-284">File name security</span></span>

<span data-ttu-id="fdbd3-285">Nikdy nepoužívejte název souboru dodaný klientem pro uložení souboru do fyzického úložiště.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-285">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="fdbd3-286">Vytvořte bezpečný název souboru pro soubor pomocí [cesty. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) nebo [cesty. GetTempFileName](xref:System.IO.Path.GetTempFileName*) a vytvořte úplnou cestu (včetně názvu souboru) pro dočasné úložiště.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-286">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="fdbd3-287">RazorAutomatické kódování HTML kóduje hodnoty vlastností pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-287">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="fdbd3-288">Následující kód je bezpečné použít:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-288">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="fdbd3-289">Mimo Razor , vždy <xref:System.Net.WebUtility.HtmlEncode*> obsah názvu souboru z požadavku uživatele.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-289">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="fdbd3-290">Mnoho implementací musí zahrnovat kontrolu, že soubor existuje. v opačném případě je soubor přepsán souborem se stejným názvem.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-290">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="fdbd3-291">Poskytněte další logiku pro splnění specifikací vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-291">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="fdbd3-292">Ověřování velikosti</span><span class="sxs-lookup"><span data-stu-id="fdbd3-292">Size validation</span></span>

<span data-ttu-id="fdbd3-293">Omezte velikost nahraných souborů.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-293">Limit the size of uploaded files.</span></span>

<span data-ttu-id="fdbd3-294">V ukázkové aplikaci je velikost souboru omezená na 2 MB (uvedené v bajtech).</span><span class="sxs-lookup"><span data-stu-id="fdbd3-294">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="fdbd3-295">Limit se poskytuje prostřednictvím [Konfigurace](xref:fundamentals/configuration/index) z *appsettings.jsv* souboru:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-295">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="fdbd3-296">`FileSizeLimit`Třída je vložena do `PageModel` tříd:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-296">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="fdbd3-297">Když velikost souboru překročí limit, soubor se odmítne:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-297">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="fdbd3-298">Porovnává hodnotu atributu name s parametrem název metody POST</span><span class="sxs-lookup"><span data-stu-id="fdbd3-298">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="fdbd3-299">V Razor neformulářích, které publikují data formuláře nebo přímo využívají JavaScript `FormData` , název zadaný v prvku formuláře nebo `FormData` musí odpovídat názvu parametru v akci kontroleru.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-299">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="fdbd3-300">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-300">In the following example:</span></span>

* <span data-ttu-id="fdbd3-301">Při použití `<input>` elementu `name` je atribut nastaven na hodnotu `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="fdbd3-301">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="fdbd3-302">Při použití `FormData` v jazyce JavaScript je název nastaven na hodnotu `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="fdbd3-302">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="fdbd3-303">Pro parametr metody jazyka C# () použijte stejný název `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="fdbd3-303">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="fdbd3-304">Pro Razor metodu obslužné rutiny stránky stránky s názvem `Upload` :</span><span class="sxs-lookup"><span data-stu-id="fdbd3-304">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="fdbd3-305">Pro metodu akce po kontroléru MVC:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-305">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="fdbd3-306">Konfigurace serveru a aplikace</span><span class="sxs-lookup"><span data-stu-id="fdbd3-306">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="fdbd3-307">Omezení délky těla částí</span><span class="sxs-lookup"><span data-stu-id="fdbd3-307">Multipart body length limit</span></span>

<span data-ttu-id="fdbd3-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>nastaví limit délky jednotlivých částí části.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="fdbd3-309">Oddíly formuláře, které překračují toto omezení, vyvolávají <xref:System.IO.InvalidDataException> při analýze.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-309">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="fdbd3-310">Výchozí hodnota je 134 217 728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="fdbd3-310">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="fdbd3-311">Upravte limit pomocí <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> nastavení v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="fdbd3-311">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="fdbd3-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>slouží k nastavení <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> pro jednu stránku nebo akci.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="fdbd3-313">V Razor aplikaci Pages použijte filtr s [konvencí](xref:razor-pages/razor-pages-conventions) v nástroji `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="fdbd3-313">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="fdbd3-314">V Razor aplikaci Pages nebo aplikaci MVC použijte filtr na model stránky nebo metodu akce:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-314">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="fdbd3-315">Kestrel maximální velikost textu požadavku</span><span class="sxs-lookup"><span data-stu-id="fdbd3-315">Kestrel maximum request body size</span></span>

<span data-ttu-id="fdbd3-316">Pro aplikace hostované v Kestrel je výchozí maximální velikost textu požadavku 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-316">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="fdbd3-317">Přizpůsobte limit pomocí možnosti serveru [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-317">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="fdbd3-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>slouží k nastavení [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) pro jednu stránku nebo akci.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="fdbd3-319">V Razor aplikaci Pages použijte filtr s [konvencí](xref:razor-pages/razor-pages-conventions) v nástroji `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="fdbd3-319">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="fdbd3-320">V Razor aplikaci Pages nebo aplikaci MVC použijte filtr na třídu obslužné rutiny stránky nebo na metodu akce:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-320">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="fdbd3-321">`RequestSizeLimitAttribute`Lze také použít pomocí [`@attribute`](xref:mvc/views/razor#attribute) Razor direktivy:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-321">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="fdbd3-322">Další omezení Kestrel</span><span class="sxs-lookup"><span data-stu-id="fdbd3-322">Other Kestrel limits</span></span>

<span data-ttu-id="fdbd3-323">Pro aplikace hostované v Kestrel se můžou vztahovat další omezení Kestrel:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-323">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="fdbd3-324">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="fdbd3-324">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="fdbd3-325">Sazby za data požadavků a odpovědí</span><span class="sxs-lookup"><span data-stu-id="fdbd3-325">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="fdbd3-326">Omezení délky obsahu služby IIS</span><span class="sxs-lookup"><span data-stu-id="fdbd3-326">IIS content length limit</span></span>

<span data-ttu-id="fdbd3-327">Výchozí limit počtu požadavků ( `maxAllowedContentLength` ) je 30 000 000 bajtů, což je přibližně 28.6 MB.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-327">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="fdbd3-328">Upravte limit *web.config* souboru:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-328">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="fdbd3-329">Toto nastavení platí pouze pro službu IIS.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-329">This setting only applies to IIS.</span></span> <span data-ttu-id="fdbd3-330">K tomuto chování nedochází ve výchozím nastavení při hostování v Kestrel.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-330">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="fdbd3-331">Další informace najdete v tématu [omezení \<requestLimits> požadavků ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="fdbd3-331">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="fdbd3-332">Omezení v modulu ASP.NET Core nebo přítomnosti modulu filtrování požadavků služby IIS mohou omezit nahrávání na 2 nebo 4 GB.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-332">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="fdbd3-333">Další informace najdete v tématu [nelze odeslat soubor o velikosti větší než 2 GB (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span><span class="sxs-lookup"><span data-stu-id="fdbd3-333">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="fdbd3-334">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="fdbd3-334">Troubleshoot</span></span>

<span data-ttu-id="fdbd3-335">Níže jsou uvedeny některé běžné problémy, které se vyskytly při práci s nahráváním souborů a jejich možnými řešeními.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-335">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="fdbd3-336">Při nasazení na server služby IIS se nenašla chyba.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-336">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="fdbd3-337">Následující chyba znamená, že nahraný soubor překračuje délku nakonfigurovaného obsahu serveru:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-337">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="fdbd3-338">Další informace o zvýšení limitu najdete v části [omezení délky obsahu služby IIS](#iis-content-length-limit) .</span><span class="sxs-lookup"><span data-stu-id="fdbd3-338">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="fdbd3-339">Chyba připojení</span><span class="sxs-lookup"><span data-stu-id="fdbd3-339">Connection failure</span></span>

<span data-ttu-id="fdbd3-340">Chyba připojení a připojení k serveru pro resetování pravděpodobně znamená, že nahraný soubor překračuje maximální velikost textu požadavku Kestrel.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-340">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="fdbd3-341">Další informace najdete v části [Kestrel maximální velikost textu požadavku](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="fdbd3-341">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="fdbd3-342">Omezení připojení klientů Kestrel mohou také vyžadovat úpravu.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-342">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="fdbd3-343">Výjimka odkazu s hodnotou null s IFormFile</span><span class="sxs-lookup"><span data-stu-id="fdbd3-343">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="fdbd3-344">Pokud kontroler přijímá odeslané soubory pomocí <xref:Microsoft.AspNetCore.Http.IFormFile> , ale hodnota je `null` , potvrďte, že formulář HTML určuje `enctype` hodnotu `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="fdbd3-344">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="fdbd3-345">Pokud tento atribut není nastaven na `<form>` elementu, odeslání souboru neproběhne a jakékoli vázané <xref:Microsoft.AspNetCore.Http.IFormFile> argumenty jsou `null` .</span><span class="sxs-lookup"><span data-stu-id="fdbd3-345">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="fdbd3-346">Ujistěte se také, že [nahrávání názvů v datech formuláře odpovídá pojmenování aplikace](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="fdbd3-346">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="fdbd3-347">Proud je příliš dlouhý.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-347">Stream was too long</span></span>

<span data-ttu-id="fdbd3-348">Příklady v tomto tématu <xref:System.IO.MemoryStream> se spoléhají na uchovávání obsahu nahraného souboru.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-348">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="fdbd3-349">Omezení velikosti `MemoryStream` je `int.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="fdbd3-349">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="fdbd3-350">Pokud scénář nahrávání souborů aplikace vyžaduje, aby byl obsah souboru větší než 50 MB, použijte alternativní přístup, který nespoléhá na jednu z nich `MemoryStream` pro uchovávání obsahu nahraného souboru.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-350">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fdbd3-351">ASP.NET Core podporuje nahrávání jednoho nebo více souborů pomocí vazby modelu ve vyrovnávací paměti pro menší soubory a streamování bez vyrovnávací paměti pro větší soubory.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-351">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="fdbd3-352">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fdbd3-352">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="fdbd3-353">Důležité informace o zabezpečení</span><span class="sxs-lookup"><span data-stu-id="fdbd3-353">Security considerations</span></span>

<span data-ttu-id="fdbd3-354">Pokud chcete uživatelům poskytnout možnost nahrávat soubory na server, buďte opatrní.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-354">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="fdbd3-355">Útočníci se můžou pokusit:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-355">Attackers may attempt to:</span></span>

* <span data-ttu-id="fdbd3-356">Vykoná útok [DOS (Denial of Service](/windows-hardware/drivers/ifs/denial-of-service) ).</span><span class="sxs-lookup"><span data-stu-id="fdbd3-356">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="fdbd3-357">Nahrání virů nebo malwaru</span><span class="sxs-lookup"><span data-stu-id="fdbd3-357">Upload viruses or malware.</span></span>
* <span data-ttu-id="fdbd3-358">Narušit sítě a servery jinými způsoby.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-358">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="fdbd3-359">Bezpečnostní kroky, které snižují pravděpodobnost úspěšného útoku, jsou:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-359">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="fdbd3-360">Nahrajte soubory do vyhrazené oblasti pro nahrávání souborů, nejlépe do nesystémové jednotky.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-360">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="fdbd3-361">Vyhrazené umístění usnadňuje omezení zabezpečení pro nahrané soubory.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-361">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="fdbd3-362">Zakažte oprávnění EXECUTE pro umístění pro nahrání souboru.&dagger;</span><span class="sxs-lookup"><span data-stu-id="fdbd3-362">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="fdbd3-363">Neuchovávat nahrané soubory ve stejném stromu adresářů jako aplikace. **not**&dagger;</span><span class="sxs-lookup"><span data-stu-id="fdbd3-363">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="fdbd3-364">Použijte název bezpečného souboru určený aplikací.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-364">Use a safe file name determined by the app.</span></span> <span data-ttu-id="fdbd3-365">Nepoužívejte název souboru poskytnutý uživatelem nebo nedůvěryhodného názvu nahraného souboru. &dagger; HTML při zobrazení kódování názvu nedůvěryhodného souboru.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-365">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="fdbd3-366">Například protokolování názvu souboru nebo zobrazení v uživatelském rozhraní ( Razor Automatické kódování HTML kódování).</span><span class="sxs-lookup"><span data-stu-id="fdbd3-366">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="fdbd3-367">Povolte pro specifikaci návrhu aplikace jenom schválené přípony souborů.&dagger;</span><span class="sxs-lookup"><span data-stu-id="fdbd3-367">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="fdbd3-368">Ověřte, zda jsou na serveru provedeny kontroly na straně klienta. &dagger; Kontroly na straně klienta je snadné obejít.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-368">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="fdbd3-369">Ověřte velikost nahraného souboru.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-369">Check the size of an uploaded file.</span></span> <span data-ttu-id="fdbd3-370">Nastavte limit maximální velikosti, aby se zabránilo velkým nahrávání.&dagger;</span><span class="sxs-lookup"><span data-stu-id="fdbd3-370">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="fdbd3-371">Pokud by soubory neměly být přepsány nahraným souborem se stejným názvem, před nahráním souboru ověřte název souboru proti databázi nebo fyzickému úložišti.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-371">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="fdbd3-372">**Před uložením souboru spusťte v nahraném obsahu skener virů nebo malwaru.**</span><span class="sxs-lookup"><span data-stu-id="fdbd3-372">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="fdbd3-373">&dagger;Ukázková aplikace předvádí přístup, který splňuje kritéria.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-373">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="fdbd3-374">Nahrávání škodlivého kódu do systému je často prvním krokem ke spuštění kódu, který může:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-374">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="fdbd3-375">Zcela získá kontrolu nad systémem.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-375">Completely gain control of a system.</span></span>
> * <span data-ttu-id="fdbd3-376">Přetížit systém s výsledkem, že dojde k chybě systému.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-376">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="fdbd3-377">Napadnout data uživatelů nebo systémových dat.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-377">Compromise user or system data.</span></span>
> * <span data-ttu-id="fdbd3-378">Použijte graffiti pro veřejné uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-378">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="fdbd3-379">Informace o omezení oblasti útoku při přijímání souborů uživateli najdete v následujících zdrojích informací:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-379">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="fdbd3-380">Neomezená nahrávání souboru</span><span class="sxs-lookup"><span data-stu-id="fdbd3-380">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="fdbd3-381">Zabezpečení Azure: Ujistěte se, že jsou při přijímání souborů od uživatelů k dismístě správné ovládací prvky.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-381">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="fdbd3-382">Další informace o implementaci bezpečnostních opatření, včetně příkladů z ukázkové aplikace, najdete v části [ověření](#validation) .</span><span class="sxs-lookup"><span data-stu-id="fdbd3-382">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="fdbd3-383">Scénáře úložiště</span><span class="sxs-lookup"><span data-stu-id="fdbd3-383">Storage scenarios</span></span>

<span data-ttu-id="fdbd3-384">Mezi běžné možnosti úložiště pro soubory patří:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-384">Common storage options for files include:</span></span>

* <span data-ttu-id="fdbd3-385">Databáze</span><span class="sxs-lookup"><span data-stu-id="fdbd3-385">Database</span></span>

  * <span data-ttu-id="fdbd3-386">U malých nahrávání souborů je databáze často rychlejší než možnosti fyzického úložiště (systému souborů nebo síťového sdílení).</span><span class="sxs-lookup"><span data-stu-id="fdbd3-386">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="fdbd3-387">Databáze je často pohodlnější než možnosti fyzického úložiště, protože načtení záznamu databáze pro uživatelská data může současně poskytovat obsah souboru (například obrázek miniatury).</span><span class="sxs-lookup"><span data-stu-id="fdbd3-387">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="fdbd3-388">Databáze je potenciálně levnější než použití služby úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-388">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="fdbd3-389">Fyzické úložiště (systém souborů nebo síťová sdílená složka)</span><span class="sxs-lookup"><span data-stu-id="fdbd3-389">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="fdbd3-390">Pro nahrávání velkých souborů:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-390">For large file uploads:</span></span>
    * <span data-ttu-id="fdbd3-391">Omezení databáze mohou omezit velikost nahrávání.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-391">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="fdbd3-392">Fyzické úložiště je často méně hospodárné než úložiště v databázi.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-392">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="fdbd3-393">Fyzické úložiště je potenciálně levnější než použití služby úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-393">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="fdbd3-394">Proces aplikace musí mít oprávnění ke čtení a zápisu do umístění úložiště.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-394">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="fdbd3-395">**Nikdy neudělujte oprávnění EXECUTE.**</span><span class="sxs-lookup"><span data-stu-id="fdbd3-395">**Never grant execute permission.**</span></span>

* <span data-ttu-id="fdbd3-396">Služba úložiště dat (například [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="fdbd3-396">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="fdbd3-397">Služby obvykle nabízejí vylepšenou škálovatelnost a odolnost proti místním řešením, které obvykle podléhají jednomu bodu selhání.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-397">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="fdbd3-398">Služby jsou potenciálně nižší náklady ve scénářích infrastruktury velkých úložišť.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-398">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="fdbd3-399">Další informace najdete v tématu [rychlý Start: použití .NET k vytvoření objektu BLOB v úložišti objektů](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="fdbd3-399">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="fdbd3-400">Téma ukazuje <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*> , ale <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> dá se použít k uložení do <xref:System.IO.FileStream> úložiště objektů BLOB při práci s <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="fdbd3-400">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="fdbd3-401">Scénáře nahrávání souborů</span><span class="sxs-lookup"><span data-stu-id="fdbd3-401">File upload scenarios</span></span>

<span data-ttu-id="fdbd3-402">Dva obecné přístupy k nahrávání souborů jsou ukládání do vyrovnávací paměti a streamování.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-402">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="fdbd3-403">**Do vyrovnávací paměti**</span><span class="sxs-lookup"><span data-stu-id="fdbd3-403">**Buffering**</span></span>

<span data-ttu-id="fdbd3-404">Celý soubor je načten do <xref:Microsoft.AspNetCore.Http.IFormFile> , což je reprezentace souboru, který se používá ke zpracování nebo uložení souboru v jazyce C#.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-404">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="fdbd3-405">Prostředky (disk, paměť) používané při nahrávání souborů závisí na počtu a velikosti souběžných nahrávání souborů.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-405">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="fdbd3-406">Pokud se aplikace pokusí do vyrovnávací paměti příliš mnoho nahrávání, dojde k selhání lokality, když dojde k vynechání paměti nebo místa na disku.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-406">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="fdbd3-407">Pokud velikost nebo frekvence nahrávání souborů vyčerpá prostředky aplikace, použijte streamování.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-407">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="fdbd3-408">Z paměti do dočasného souboru na disku se přesune libovolný soubor s vyrovnávací pamětí větší než 64 KB.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-408">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="fdbd3-409">Ukládání malých souborů do vyrovnávací paměti je popsáno v následujících částech tohoto tématu:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-409">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="fdbd3-410">Fyzické úložiště</span><span class="sxs-lookup"><span data-stu-id="fdbd3-410">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="fdbd3-411">Database</span><span class="sxs-lookup"><span data-stu-id="fdbd3-411">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="fdbd3-412">**Streamování**</span><span class="sxs-lookup"><span data-stu-id="fdbd3-412">**Streaming**</span></span>

<span data-ttu-id="fdbd3-413">Soubor se přijímá z požadavku na více částí a přímo se zpracovává nebo ukládá v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-413">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="fdbd3-414">Streamování nijak významně nezvyšuje výkon.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-414">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="fdbd3-415">Streamování snižuje nároky na paměť nebo místo na disku při nahrávání souborů.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-415">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="fdbd3-416">Streamování velkých souborů je zahrnuté v části [nahrávání velkých souborů pomocí streamování](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="fdbd3-416">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="fdbd3-417">Nahrávání malých souborů s vazbou modelu ve vyrovnávací paměti na fyzické úložiště</span><span class="sxs-lookup"><span data-stu-id="fdbd3-417">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="fdbd3-418">Pro nahrání malých souborů použijte formulář s více částmi nebo sestavte požadavek POST pomocí JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-418">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="fdbd3-419">Následující příklad ukazuje použití Razor formuláře stránky k nahrání jednoho souboru (*Pages/BufferedSingleFileUploadPhysical. cshtml* do ukázkové aplikace):</span><span class="sxs-lookup"><span data-stu-id="fdbd3-419">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="fdbd3-420">Následující příklad je podobný předchozímu příkladu s tím rozdílem, že:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-420">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="fdbd3-421">K odeslání dat formuláře se používá JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)).</span><span class="sxs-lookup"><span data-stu-id="fdbd3-421">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="fdbd3-422">Neexistuje žádné ověření.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-422">There's no validation.</span></span>

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

<span data-ttu-id="fdbd3-423">Chcete-li provést příspěvek formuláře v jazyce JavaScript pro klienty, kteří [nepodporují rozhraní API pro načítání](https://caniuse.com/#feat=fetch), použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-423">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="fdbd3-424">Použijte načtenou výplň (například [window. Fetch Fill (GitHub/Fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="fdbd3-424">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="fdbd3-425">Použijte `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-425">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="fdbd3-426">Například:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-426">For example:</span></span>

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

<span data-ttu-id="fdbd3-427">Aby bylo možné podporovat nahrávání souborů, musí formuláře HTML určovat typ kódování ( `enctype` ) `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="fdbd3-427">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="fdbd3-428">Pro `files` vstupní element, který podporuje nahrávání více souborů, poskytněte `multiple` atribut `<input>` elementu:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-428">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="fdbd3-429">Jednotlivé soubory nahrané na server jsou k dispozici prostřednictvím [vazby modelu](xref:mvc/models/model-binding) pomocí <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="fdbd3-429">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="fdbd3-430">Ukázková aplikace ukazuje více ukládání souborů do vyrovnávací paměti pro scénáře databáze a fyzických úložišť.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-430">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="fdbd3-431">Nepoužívejte **not** `FileName` vlastnost <xref:Microsoft.AspNetCore.Http.IFormFile> jinou než pro zobrazení a protokolování.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-431">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="fdbd3-432">Při zobrazení nebo protokolování je název souboru kódován HTML.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-432">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="fdbd3-433">Útočník může poskytnout škodlivý název souboru, včetně úplných cest nebo relativních cest.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-433">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="fdbd3-434">Aplikace by měly:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-434">Applications should:</span></span>
>
> * <span data-ttu-id="fdbd3-435">Odeberte cestu z názvu souboru zadaného uživatelem.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-435">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="fdbd3-436">Uložte název souboru s příponou PATH s kódováním HTML pro uživatelské rozhraní nebo protokolování.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-436">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="fdbd3-437">Vygenerujte nový náhodný název souboru pro úložiště.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-437">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="fdbd3-438">Následující kód odstraní cestu z názvu souboru:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-438">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="fdbd3-439">Zde uvedené příklady neberou ohled na zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-439">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="fdbd3-440">Další informace jsou k dispozici v následujících částech a [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="fdbd3-440">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="fdbd3-441">Důležité informace o zabezpečení</span><span class="sxs-lookup"><span data-stu-id="fdbd3-441">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="fdbd3-442">Export</span><span class="sxs-lookup"><span data-stu-id="fdbd3-442">Validation</span></span>](#validation)

<span data-ttu-id="fdbd3-443">Při nahrávání souborů pomocí vazby modelu a <xref:Microsoft.AspNetCore.Http.IFormFile> může metoda Action přijmout:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-443">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="fdbd3-444">Jedna <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="fdbd3-444">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="fdbd3-445">Kterákoli z následujících kolekcí, které reprezentují několik souborů:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-445">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="fdbd3-446">[Seznamu](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="fdbd3-446">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="fdbd3-447">Vazba odpovídá souborům formuláře podle názvu.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-447">Binding matches form files by name.</span></span> <span data-ttu-id="fdbd3-448">Například hodnota HTML v se `name` `<input type="file" name="formFile">` musí shodovat s parametrem nebo vazbou vlastnosti jazyka C# ( `FormFile` ).</span><span class="sxs-lookup"><span data-stu-id="fdbd3-448">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="fdbd3-449">Další informace naleznete v části [název atributu matched na název parametru metody post](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="fdbd3-449">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="fdbd3-450">Následující příklad:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-450">The following example:</span></span>

* <span data-ttu-id="fdbd3-451">Projde jedním nebo více nahranými soubory.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-451">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="fdbd3-452">Pomocí [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) vrátí úplnou cestu k souboru, včetně názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-452">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="fdbd3-453">Uloží soubory do místního systému souborů pomocí názvu souboru generovaného aplikací.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-453">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="fdbd3-454">Vrátí celkový počet nahraných souborů a jejich velikost.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-454">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="fdbd3-455">Slouží `Path.GetRandomFileName` k vygenerování názvu souboru bez cesty.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-455">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="fdbd3-456">V následujícím příkladu je cesta získána z konfigurace:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-456">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="fdbd3-457">Cesta předaná do <xref:System.IO.FileStream> *musí* zahrnovat název souboru.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-457">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="fdbd3-458">Pokud není zadán název souboru, <xref:System.UnauthorizedAccessException> je vyvolána za běhu.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-458">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="fdbd3-459">Soubory odeslané pomocí <xref:Microsoft.AspNetCore.Http.IFormFile> techniky jsou v paměti nebo na disku na serveru před zpracováním uloženy do vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-459">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="fdbd3-460">V rámci metody Action je <xref:Microsoft.AspNetCore.Http.IFormFile> obsah přístupný jako <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="fdbd3-460">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="fdbd3-461">Kromě místního systému souborů je možné soubory ukládat do síťové sdílené složky nebo do služby úložiště souborů, jako je [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="fdbd3-461">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="fdbd3-462">Další příklad, který projde několik souborů pro nahrání a používá bezpečné názvy souborů, najdete v ukázkové aplikaci v části *Pages/BufferedMultipleFileUploadPhysical. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="fdbd3-462">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="fdbd3-463">[Cesta. GetTempFileName](xref:System.IO.Path.GetTempFileName*) vyvolá výjimku, <xref:System.IO.IOException> Pokud jsou vytvořeny více než 65 535 souborů bez odstranění předchozích dočasných souborů.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-463">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="fdbd3-464">Limit 65 535 souborů je omezen na server.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-464">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="fdbd3-465">Další informace o tomto limitu pro operační systém Windows najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-465">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="fdbd3-466">GetTempFileNameA – funkce</span><span class="sxs-lookup"><span data-stu-id="fdbd3-466">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="fdbd3-467">Nahrávání malých souborů s vazbou modelu s vyrovnávací pamětí do databáze</span><span class="sxs-lookup"><span data-stu-id="fdbd3-467">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="fdbd3-468">Chcete-li uložit data binárního souboru do databáze pomocí [Entity Framework](/ef/core/index), definujte v <xref:System.Byte> entitě vlastnost Array:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-468">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="fdbd3-469">Zadejte vlastnost modelu stránky pro třídu, která obsahuje <xref:Microsoft.AspNetCore.Http.IFormFile> :</span><span class="sxs-lookup"><span data-stu-id="fdbd3-469">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="fdbd3-470"><xref:Microsoft.AspNetCore.Http.IFormFile>lze ji použít přímo jako parametr metody akce nebo jako vlastnost vázaného modelu.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-470"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="fdbd3-471">Předchozí příklad používá vlastnost vázaného modelu.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-471">The prior example uses a bound model property.</span></span>

<span data-ttu-id="fdbd3-472">`FileUpload`Je použit ve Razor formuláři stránky:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-472">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="fdbd3-473">Když je formulář publikovaný na serveru, zkopírujte <xref:Microsoft.AspNetCore.Http.IFormFile> ho do datového proudu a uložte ho jako pole bajtů v databázi.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-473">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="fdbd3-474">V následujícím příkladu `_dbContext` ukládá kontext databáze aplikace:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-474">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="fdbd3-475">Předchozí příklad je podobný scénáři, který je znázorněný v ukázkové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-475">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="fdbd3-476">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="fdbd3-476">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="fdbd3-477">*Pages/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="fdbd3-477">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="fdbd3-478">Při ukládání binárních dat do relačních databází buďte opatrní, protože to může mít nepříznivý vliv na výkon.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-478">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="fdbd3-479">Nespoléhá se na nebo důvěřujete `FileName` vlastnosti <xref:Microsoft.AspNetCore.Http.IFormFile> bez ověření.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-479">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="fdbd3-480">`FileName`Vlastnost by měla být použita pouze pro účely zobrazení a pouze po kódování HTML.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-480">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="fdbd3-481">Uvedené příklady nevezmou ohled na zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-481">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="fdbd3-482">Další informace jsou k dispozici v následujících částech a [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="fdbd3-482">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="fdbd3-483">Důležité informace o zabezpečení</span><span class="sxs-lookup"><span data-stu-id="fdbd3-483">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="fdbd3-484">Export</span><span class="sxs-lookup"><span data-stu-id="fdbd3-484">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="fdbd3-485">Nahrávání velkých souborů pomocí streamování</span><span class="sxs-lookup"><span data-stu-id="fdbd3-485">Upload large files with streaming</span></span>

<span data-ttu-id="fdbd3-486">Následující příklad ukazuje, jak použít JavaScript ke streamování souboru do akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-486">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="fdbd3-487">Token proti padělání souboru se generuje pomocí vlastního atributu filtru a předává se do hlaviček protokolu HTTP klienta místo v textu žádosti.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-487">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="fdbd3-488">Vzhledem k tomu, že metoda akce zpracovává nahraná data přímo, vazba modelu formuláře je zakázána jiným vlastním filtrem.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-488">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="fdbd3-489">V rámci akce je obsah formuláře čten pomocí `MultipartReader` , který čte každou jednotlivou osobu `MultipartSection` , zpracovává soubor nebo ukládá obsah podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-489">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="fdbd3-490">Po načtení oddílů s více částmi provede akce vlastní vazbu modelu.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-490">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="fdbd3-491">Počáteční odpověď stránky načte formulář a uloží token proti padělání do cookie (prostřednictvím `GenerateAntiforgeryTokenCookieAttribute` atributu).</span><span class="sxs-lookup"><span data-stu-id="fdbd3-491">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="fdbd3-492">Atribut používá ASP.NET Core integrovanou [podporu proti padělání](xref:security/anti-request-forgery) pro nastavení cookie s tokenem požadavku:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-492">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="fdbd3-493">`DisableFormValueModelBindingAttribute`Slouží k zakázání vazby modelu:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-493">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="fdbd3-494">V ukázkové aplikaci a používá `GenerateAntiforgeryTokenCookieAttribute` se `DisableFormValueModelBindingAttribute` jako filtr pro modelové aplikace stránky `/StreamedSingleFileUploadDb` a `/StreamedSingleFileUploadPhysical` v `Startup.ConfigureServices` [ Razor konvencích použití stránek](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="fdbd3-494">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="fdbd3-495">Vzhledem k tomu, že vazba modelu nepřečte formulář, parametry, které jsou svázané z formuláře, se nezobrazují (budou pokračovat v práci s dotazem, trasou a hlavičkou).</span><span class="sxs-lookup"><span data-stu-id="fdbd3-495">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="fdbd3-496">Metoda Action pracuje přímo s `Request` vlastností.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-496">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="fdbd3-497">`MultipartReader`Slouží ke čtení jednotlivých oddílů.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-497">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="fdbd3-498">Data klíč/hodnota jsou uložena v `KeyValueAccumulator` .</span><span class="sxs-lookup"><span data-stu-id="fdbd3-498">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="fdbd3-499">Po načtení oddílů s více částmi se obsah `KeyValueAccumulator` používá pro svázání dat formuláře s typem modelu.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-499">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="fdbd3-500">Úplná `StreamingController.UploadDatabase` metoda pro streamování do databáze s EF Core:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-500">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="fdbd3-501">`MultipartRequestHelper`(*Nástroje/MultipartRequestHelper. cs*):</span><span class="sxs-lookup"><span data-stu-id="fdbd3-501">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="fdbd3-502">Úplná `StreamingController.UploadPhysical` metoda pro streamování do fyzického umístění:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-502">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="fdbd3-503">V ukázkové aplikaci jsou kontroly ověřování zpracovávány nástrojem `FileHelpers.ProcessStreamedFile` .</span><span class="sxs-lookup"><span data-stu-id="fdbd3-503">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="fdbd3-504">Ověřování vstupů (validace)</span><span class="sxs-lookup"><span data-stu-id="fdbd3-504">Validation</span></span>

<span data-ttu-id="fdbd3-505">Třída ukázkové aplikace `FileHelpers` ukazuje několik kontrol ukládání souborů do vyrovnávací paměti <xref:Microsoft.AspNetCore.Http.IFormFile> a datových proudů při nahrávání.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-505">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="fdbd3-506">Informace o zpracování <xref:Microsoft.AspNetCore.Http.IFormFile> ukládání souborů do vyrovnávací paměti v ukázkové aplikaci naleznete v `ProcessFormFile` metodě v souboru *Utilities/Helper. cs* .</span><span class="sxs-lookup"><span data-stu-id="fdbd3-506">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="fdbd3-507">Pro zpracování streamované soubory se podívejte na `ProcessStreamedFile` metodu ve stejném souboru.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-507">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="fdbd3-508">Metody zpracování ověřování, které jsou znázorněné v ukázkové aplikaci, nekontrolují obsah nahraných souborů.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-508">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="fdbd3-509">Ve většině produkčních scénářů se v souboru používá rozhraní API pro skenování virů nebo malwaru, než je soubor dostupný uživatelům nebo jiným systémům.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-509">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="fdbd3-510">I když ukázka tématu poskytuje pracovní příklad technik ověřování, Neimplementujte `FileHelpers` třídu v produkční aplikaci, pokud:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-510">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="fdbd3-511">Plně rozumíte implementaci.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-511">Fully understand the implementation.</span></span>
> * <span data-ttu-id="fdbd3-512">Upravte implementaci podle potřeby pro prostředí a specifikace aplikace.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-512">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="fdbd3-513">**Nikdy nepoužívejte nerozlišený kód zabezpečení v aplikaci bez nutnosti řešit tyto požadavky.**</span><span class="sxs-lookup"><span data-stu-id="fdbd3-513">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="fdbd3-514">Ověření obsahu</span><span class="sxs-lookup"><span data-stu-id="fdbd3-514">Content validation</span></span>

<span data-ttu-id="fdbd3-515">**Pro nahraný obsah použijte rozhraní API pro kontrolu virů a malwaru třetí strany.**</span><span class="sxs-lookup"><span data-stu-id="fdbd3-515">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="fdbd3-516">Prohledávání souborů je náročné na prostředky serveru ve scénářích s vysokým objemem.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-516">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="fdbd3-517">Pokud dojde ke snížení výkonu zpracování požadavků z důvodu kontroly souborů, zvažte přesměrování práce skenování na službu na [pozadí](xref:fundamentals/host/hosted-services), případně služby spuštěné na serveru, který se liší od serveru aplikace.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-517">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="fdbd3-518">Nahrané soubory se obvykle uchovávají v oblasti v karanténě, dokud je kontrola virů na pozadí nevrátí.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-518">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="fdbd3-519">Když soubor projde, soubor se přesune do normálního umístění úložiště souborů.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-519">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="fdbd3-520">Tyto kroky se obvykle provádí ve spojení s databázovým záznamem, který indikuje stav kontroly souboru.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-520">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="fdbd3-521">Při použití takového přístupu zůstane aplikace a Server App zaměřené na reakci na požadavky.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-521">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="fdbd3-522">Ověření přípony souboru</span><span class="sxs-lookup"><span data-stu-id="fdbd3-522">File extension validation</span></span>

<span data-ttu-id="fdbd3-523">Přípona nahraného souboru by měla být zaškrtnutá na seznamu povolených rozšíření.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-523">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="fdbd3-524">Například:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-524">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="fdbd3-525">Ověření podpisu souboru</span><span class="sxs-lookup"><span data-stu-id="fdbd3-525">File signature validation</span></span>

<span data-ttu-id="fdbd3-526">Podpis souboru se určuje na prvních několika bajtech na začátku souboru.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-526">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="fdbd3-527">Tyto bajty lze použít k určení, zda přípona odpovídá obsahu souboru.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-527">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="fdbd3-528">Ukázková aplikace zkontroluje podpisy souborů pro několik běžných typů souborů.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-528">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="fdbd3-529">V následujícím příkladu se pro tento soubor kontroluje podpis souboru obrázku JPEG:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-529">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="fdbd3-530">Další signatury souborů získáte v dokumentaci [signatury souborů](https://www.filesignatures.net/) a oficiálních souborů.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-530">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="fdbd3-531">Zabezpečení názvu souboru</span><span class="sxs-lookup"><span data-stu-id="fdbd3-531">File name security</span></span>

<span data-ttu-id="fdbd3-532">Nikdy nepoužívejte název souboru dodaný klientem pro uložení souboru do fyzického úložiště.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-532">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="fdbd3-533">Vytvořte bezpečný název souboru pro soubor pomocí [cesty. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) nebo [cesty. GetTempFileName](xref:System.IO.Path.GetTempFileName*) a vytvořte úplnou cestu (včetně názvu souboru) pro dočasné úložiště.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-533">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="fdbd3-534">RazorAutomatické kódování HTML kóduje hodnoty vlastností pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-534">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="fdbd3-535">Následující kód je bezpečné použít:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-535">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="fdbd3-536">Mimo Razor , vždy <xref:System.Net.WebUtility.HtmlEncode*> obsah názvu souboru z požadavku uživatele.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-536">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="fdbd3-537">Mnoho implementací musí zahrnovat kontrolu, že soubor existuje. v opačném případě je soubor přepsán souborem se stejným názvem.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-537">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="fdbd3-538">Poskytněte další logiku pro splnění specifikací vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-538">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="fdbd3-539">Ověřování velikosti</span><span class="sxs-lookup"><span data-stu-id="fdbd3-539">Size validation</span></span>

<span data-ttu-id="fdbd3-540">Omezte velikost nahraných souborů.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-540">Limit the size of uploaded files.</span></span>

<span data-ttu-id="fdbd3-541">V ukázkové aplikaci je velikost souboru omezená na 2 MB (uvedené v bajtech).</span><span class="sxs-lookup"><span data-stu-id="fdbd3-541">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="fdbd3-542">Limit se poskytuje prostřednictvím [Konfigurace](xref:fundamentals/configuration/index) z *appsettings.jsv* souboru:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-542">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="fdbd3-543">`FileSizeLimit`Třída je vložena do `PageModel` tříd:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-543">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="fdbd3-544">Když velikost souboru překročí limit, soubor se odmítne:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-544">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="fdbd3-545">Porovnává hodnotu atributu name s parametrem název metody POST</span><span class="sxs-lookup"><span data-stu-id="fdbd3-545">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="fdbd3-546">V Razor neformulářích, které publikují data formuláře nebo přímo využívají JavaScript `FormData` , název zadaný v prvku formuláře nebo `FormData` musí odpovídat názvu parametru v akci kontroleru.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-546">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="fdbd3-547">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-547">In the following example:</span></span>

* <span data-ttu-id="fdbd3-548">Při použití `<input>` elementu `name` je atribut nastaven na hodnotu `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="fdbd3-548">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="fdbd3-549">Při použití `FormData` v jazyce JavaScript je název nastaven na hodnotu `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="fdbd3-549">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="fdbd3-550">Pro parametr metody jazyka C# () použijte stejný název `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="fdbd3-550">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="fdbd3-551">Pro Razor metodu obslužné rutiny stránky stránky s názvem `Upload` :</span><span class="sxs-lookup"><span data-stu-id="fdbd3-551">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="fdbd3-552">Pro metodu akce po kontroléru MVC:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-552">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="fdbd3-553">Konfigurace serveru a aplikace</span><span class="sxs-lookup"><span data-stu-id="fdbd3-553">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="fdbd3-554">Omezení délky těla částí</span><span class="sxs-lookup"><span data-stu-id="fdbd3-554">Multipart body length limit</span></span>

<span data-ttu-id="fdbd3-555"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>nastaví limit délky jednotlivých částí části.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-555"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="fdbd3-556">Oddíly formuláře, které překračují toto omezení, vyvolávají <xref:System.IO.InvalidDataException> při analýze.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-556">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="fdbd3-557">Výchozí hodnota je 134 217 728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="fdbd3-557">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="fdbd3-558">Upravte limit pomocí <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> nastavení v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="fdbd3-558">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="fdbd3-559"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>slouží k nastavení <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> pro jednu stránku nebo akci.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-559"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="fdbd3-560">V Razor aplikaci Pages použijte filtr s [konvencí](xref:razor-pages/razor-pages-conventions) v nástroji `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="fdbd3-560">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="fdbd3-561">V Razor aplikaci Pages nebo aplikaci MVC použijte filtr na model stránky nebo metodu akce:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-561">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="fdbd3-562">Kestrel maximální velikost textu požadavku</span><span class="sxs-lookup"><span data-stu-id="fdbd3-562">Kestrel maximum request body size</span></span>

<span data-ttu-id="fdbd3-563">Pro aplikace hostované v Kestrel je výchozí maximální velikost textu požadavku 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-563">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="fdbd3-564">Přizpůsobte limit pomocí možnosti serveru [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-564">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="fdbd3-565"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>slouží k nastavení [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) pro jednu stránku nebo akci.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-565"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="fdbd3-566">V Razor aplikaci Pages použijte filtr s [konvencí](xref:razor-pages/razor-pages-conventions) v nástroji `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="fdbd3-566">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="fdbd3-567">V Razor aplikaci Pages nebo aplikaci MVC použijte filtr na třídu obslužné rutiny stránky nebo na metodu akce:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-567">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="fdbd3-568">Další omezení Kestrel</span><span class="sxs-lookup"><span data-stu-id="fdbd3-568">Other Kestrel limits</span></span>

<span data-ttu-id="fdbd3-569">Pro aplikace hostované v Kestrel se můžou vztahovat další omezení Kestrel:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-569">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="fdbd3-570">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="fdbd3-570">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="fdbd3-571">Sazby za data požadavků a odpovědí</span><span class="sxs-lookup"><span data-stu-id="fdbd3-571">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="fdbd3-572">Omezení délky obsahu služby IIS</span><span class="sxs-lookup"><span data-stu-id="fdbd3-572">IIS content length limit</span></span>

<span data-ttu-id="fdbd3-573">Výchozí limit počtu požadavků ( `maxAllowedContentLength` ) je 30 000 000 bajtů, což je přibližně 28.6 MB.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-573">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="fdbd3-574">Upravte limit *web.config* souboru:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-574">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="fdbd3-575">Toto nastavení platí pouze pro službu IIS.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-575">This setting only applies to IIS.</span></span> <span data-ttu-id="fdbd3-576">K tomuto chování nedochází ve výchozím nastavení při hostování v Kestrel.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-576">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="fdbd3-577">Další informace najdete v tématu [omezení \<requestLimits> požadavků ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="fdbd3-577">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="fdbd3-578">Omezení v modulu ASP.NET Core nebo přítomnosti modulu filtrování požadavků služby IIS mohou omezit nahrávání na 2 nebo 4 GB.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-578">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="fdbd3-579">Další informace najdete v tématu [nelze odeslat soubor o velikosti větší než 2 GB (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span><span class="sxs-lookup"><span data-stu-id="fdbd3-579">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="fdbd3-580">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="fdbd3-580">Troubleshoot</span></span>

<span data-ttu-id="fdbd3-581">Níže jsou uvedeny některé běžné problémy, které se vyskytly při práci s nahráváním souborů a jejich možnými řešeními.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-581">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="fdbd3-582">Při nasazení na server služby IIS se nenašla chyba.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-582">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="fdbd3-583">Následující chyba znamená, že nahraný soubor překračuje délku nakonfigurovaného obsahu serveru:</span><span class="sxs-lookup"><span data-stu-id="fdbd3-583">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="fdbd3-584">Další informace o zvýšení limitu najdete v části [omezení délky obsahu služby IIS](#iis-content-length-limit) .</span><span class="sxs-lookup"><span data-stu-id="fdbd3-584">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="fdbd3-585">Chyba připojení</span><span class="sxs-lookup"><span data-stu-id="fdbd3-585">Connection failure</span></span>

<span data-ttu-id="fdbd3-586">Chyba připojení a připojení k serveru pro resetování pravděpodobně znamená, že nahraný soubor překračuje maximální velikost textu požadavku Kestrel.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-586">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="fdbd3-587">Další informace najdete v části [Kestrel maximální velikost textu požadavku](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="fdbd3-587">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="fdbd3-588">Omezení připojení klientů Kestrel mohou také vyžadovat úpravu.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-588">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="fdbd3-589">Výjimka odkazu s hodnotou null s IFormFile</span><span class="sxs-lookup"><span data-stu-id="fdbd3-589">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="fdbd3-590">Pokud kontroler přijímá odeslané soubory pomocí <xref:Microsoft.AspNetCore.Http.IFormFile> , ale hodnota je `null` , potvrďte, že formulář HTML určuje `enctype` hodnotu `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="fdbd3-590">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="fdbd3-591">Pokud tento atribut není nastaven na `<form>` elementu, odeslání souboru neproběhne a jakékoli vázané <xref:Microsoft.AspNetCore.Http.IFormFile> argumenty jsou `null` .</span><span class="sxs-lookup"><span data-stu-id="fdbd3-591">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="fdbd3-592">Ujistěte se také, že [nahrávání názvů v datech formuláře odpovídá pojmenování aplikace](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="fdbd3-592">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="fdbd3-593">Proud je příliš dlouhý.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-593">Stream was too long</span></span>

<span data-ttu-id="fdbd3-594">Příklady v tomto tématu <xref:System.IO.MemoryStream> se spoléhají na uchovávání obsahu nahraného souboru.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-594">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="fdbd3-595">Omezení velikosti `MemoryStream` je `int.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="fdbd3-595">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="fdbd3-596">Pokud scénář nahrávání souborů aplikace vyžaduje, aby byl obsah souboru větší než 50 MB, použijte alternativní přístup, který nespoléhá na jednu z nich `MemoryStream` pro uchovávání obsahu nahraného souboru.</span><span class="sxs-lookup"><span data-stu-id="fdbd3-596">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="fdbd3-597">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="fdbd3-597">Additional resources</span></span>

* [<span data-ttu-id="fdbd3-598">Vyprazdňování žádosti o připojení HTTP</span><span class="sxs-lookup"><span data-stu-id="fdbd3-598">HTTP connection request draining</span></span>](xref:fundamentals/servers/kestrel#http11-request-draining)
* [<span data-ttu-id="fdbd3-599">Neomezená nahrávání souboru</span><span class="sxs-lookup"><span data-stu-id="fdbd3-599">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
* [<span data-ttu-id="fdbd3-600">Zabezpečení Azure: rámec zabezpečení: ověření vstupu | Hrozeb</span><span class="sxs-lookup"><span data-stu-id="fdbd3-600">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="fdbd3-601">Vzory návrhu cloudu Azure: vzor osobního Key</span><span class="sxs-lookup"><span data-stu-id="fdbd3-601">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
