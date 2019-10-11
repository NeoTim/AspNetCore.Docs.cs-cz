---
title: Nahrání souborů v ASP.NET Core
author: guardrex
description: Jak používat vazbu modelu a streamování k nahrávání souborů v ASP.NET Core MVC
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2019
uid: mvc/models/file-uploads
ms.openlocfilehash: de8bfee22e39dfc5a6ed254cf0555887891d4590
ms.sourcegitcommit: d81912782a8b0bd164f30a516ad80f8defb5d020
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72179308"
---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="836ea-103">Nahrání souborů v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="836ea-103">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="836ea-104">Od [Luke Latham](https://github.com/guardrex), [Steve Smith](https://ardalis.com/)a [Rutger](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="836ea-104">By [Luke Latham](https://github.com/guardrex), [Steve Smith](https://ardalis.com/), and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="836ea-105">ASP.NET Core podporuje nahrávání jednoho nebo více souborů pomocí vazby modelu ve vyrovnávací paměti pro menší soubory a streamování bez vyrovnávací paměti pro větší soubory.</span><span class="sxs-lookup"><span data-stu-id="836ea-105">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="836ea-106">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="836ea-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="836ea-107">Důležité informace o zabezpečení</span><span class="sxs-lookup"><span data-stu-id="836ea-107">Security considerations</span></span>

<span data-ttu-id="836ea-108">Pokud chcete uživatelům poskytnout možnost nahrávat soubory na server, buďte opatrní.</span><span class="sxs-lookup"><span data-stu-id="836ea-108">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="836ea-109">Útočníci se můžou pokusit:</span><span class="sxs-lookup"><span data-stu-id="836ea-109">Attackers may attempt to:</span></span>

* <span data-ttu-id="836ea-110">Vykoná útok [DOS (Denial of Service](/windows-hardware/drivers/ifs/denial-of-service) ).</span><span class="sxs-lookup"><span data-stu-id="836ea-110">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="836ea-111">Nahrání virů nebo malwaru</span><span class="sxs-lookup"><span data-stu-id="836ea-111">Upload viruses or malware.</span></span>
* <span data-ttu-id="836ea-112">Narušit sítě a servery jinými způsoby.</span><span class="sxs-lookup"><span data-stu-id="836ea-112">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="836ea-113">Bezpečnostní kroky, které snižují pravděpodobnost úspěšného útoku, jsou:</span><span class="sxs-lookup"><span data-stu-id="836ea-113">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="836ea-114">Nahrajte soubory do vyhrazené oblasti pro nahrání souborů v systému, nejlépe pro nesystémovou jednotku.</span><span class="sxs-lookup"><span data-stu-id="836ea-114">Upload files to a dedicated file upload area on the system, preferably to a non-system drive.</span></span> <span data-ttu-id="836ea-115">Použití vyhrazeného umístění usnadňuje omezení zabezpečení pro nahrané soubory.</span><span class="sxs-lookup"><span data-stu-id="836ea-115">Using a dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="836ea-116">Zakažte oprávnění EXECUTE pro umístění pro nahrání souboru. &dagger;</span><span class="sxs-lookup"><span data-stu-id="836ea-116">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="836ea-117">Neuchovávat nahrané soubory ve stejném adresářovém stromu jako aplikace. &dagger;</span><span class="sxs-lookup"><span data-stu-id="836ea-117">Never persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="836ea-118">Použijte název bezpečného souboru určený aplikací.</span><span class="sxs-lookup"><span data-stu-id="836ea-118">Use a safe file name determined by the app.</span></span> <span data-ttu-id="836ea-119">Nepoužívejte název souboru poskytnutý uživatelem nebo nedůvěryhodného názvu nahraného souboru. &dagger; Chcete-li zobrazit nedůvěryhodný název souboru v uživatelském rozhraní nebo v zprávě protokolování, HTML hodnotu zakódovat.</span><span class="sxs-lookup"><span data-stu-id="836ea-119">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; To display an untrusted file name in a UI or in a logging message, HTML-encode the value.</span></span>
* <span data-ttu-id="836ea-120">Povoluje pouze konkrétní sadu schválených přípon souborů. &dagger;</span><span class="sxs-lookup"><span data-stu-id="836ea-120">Only allow a specific set of approved file extensions.&dagger;</span></span>
* <span data-ttu-id="836ea-121">Ověřte podpis formátu souboru, abyste zabránili uživateli v nahrávání maskovaných souborů. &dagger; například nepovolíte uživateli odeslat soubor *. exe* s příponou *. txt* .</span><span class="sxs-lookup"><span data-stu-id="836ea-121">Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension.</span></span>
* <span data-ttu-id="836ea-122">Ověřte, zda jsou na serveru provedeny také kontroly na straně klienta. &dagger; kontroly na straně klienta se snadno obejít.</span><span class="sxs-lookup"><span data-stu-id="836ea-122">Verify that client-side checks are also performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="836ea-123">Ověřte velikost nahraného souboru a zabraňte nahrávání, které jsou větší, než se očekávalo. &dagger;</span><span class="sxs-lookup"><span data-stu-id="836ea-123">Check the size of an uploaded file and prevent uploads that are larger than expected.&dagger;</span></span>
* <span data-ttu-id="836ea-124">Pokud by soubory neměly být přepsány nahraným souborem se stejným názvem, před nahráním souboru ověřte název souboru proti databázi nebo fyzickému úložišti.</span><span class="sxs-lookup"><span data-stu-id="836ea-124">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="836ea-125">**Před uložením souboru spusťte v nahraném obsahu skener virů nebo malwaru.**</span><span class="sxs-lookup"><span data-stu-id="836ea-125">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="836ea-126">Ukázková aplikace &dagger;The ukazuje přístup, který splňuje kritéria.</span><span class="sxs-lookup"><span data-stu-id="836ea-126">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="836ea-127">Nahrávání škodlivého kódu do systému je často prvním krokem ke spuštění kódu, který může:</span><span class="sxs-lookup"><span data-stu-id="836ea-127">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="836ea-128">Zcela získá kontrolu nad systémem.</span><span class="sxs-lookup"><span data-stu-id="836ea-128">Completely gain control of a system.</span></span>
> * <span data-ttu-id="836ea-129">Přetížit systém s výsledkem, že dojde k chybě systému.</span><span class="sxs-lookup"><span data-stu-id="836ea-129">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="836ea-130">Napadnout data uživatelů nebo systémových dat.</span><span class="sxs-lookup"><span data-stu-id="836ea-130">Compromise user or system data.</span></span>
> * <span data-ttu-id="836ea-131">Použijte graffiti pro veřejné uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="836ea-131">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="836ea-132">Informace o omezení oblasti útoku při přijímání souborů uživateli najdete v následujících zdrojích informací:</span><span class="sxs-lookup"><span data-stu-id="836ea-132">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="836ea-133">Neomezená nahrávání souboru</span><span class="sxs-lookup"><span data-stu-id="836ea-133">Unrestricted File Upload</span></span>](https://www.owasp.org/index.php/Unrestricted_File_Upload)
> * [<span data-ttu-id="836ea-134">Zabezpečení Azure: Ujistěte se, že jsou při přijímání souborů od uživatelů k dismístě správné ovládací prvky.</span><span class="sxs-lookup"><span data-stu-id="836ea-134">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="836ea-135">Další informace o implementaci bezpečnostních opatření, včetně příkladů z ukázkové aplikace, najdete v části [ověření](#validation) .</span><span class="sxs-lookup"><span data-stu-id="836ea-135">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="836ea-136">Scénáře úložiště</span><span class="sxs-lookup"><span data-stu-id="836ea-136">Storage scenarios</span></span>

<span data-ttu-id="836ea-137">Mezi běžné možnosti úložiště pro soubory patří:</span><span class="sxs-lookup"><span data-stu-id="836ea-137">Common storage options for files include:</span></span>

* <span data-ttu-id="836ea-138">Databáze</span><span class="sxs-lookup"><span data-stu-id="836ea-138">Database</span></span>

  * <span data-ttu-id="836ea-139">U malých nahrávání souborů je databáze často rychlejší než možnosti fyzického úložiště (systému souborů nebo síťového sdílení).</span><span class="sxs-lookup"><span data-stu-id="836ea-139">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="836ea-140">Databáze je často pohodlnější než možnosti fyzického úložiště, protože načtení záznamu databáze pro uživatelská data může současně poskytovat obsah souboru (například obrázek miniatury).</span><span class="sxs-lookup"><span data-stu-id="836ea-140">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="836ea-141">Databáze je potenciálně levnější než použití služby úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="836ea-141">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="836ea-142">Fyzické úložiště (systém souborů nebo síťová sdílená složka)</span><span class="sxs-lookup"><span data-stu-id="836ea-142">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="836ea-143">Pro nahrávání velkých souborů:</span><span class="sxs-lookup"><span data-stu-id="836ea-143">For large file uploads:</span></span>
    * <span data-ttu-id="836ea-144">Omezení databáze mohou omezit velikost nahrávání.</span><span class="sxs-lookup"><span data-stu-id="836ea-144">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="836ea-145">Fyzické úložiště je často méně hospodárné než úložiště v databázi.</span><span class="sxs-lookup"><span data-stu-id="836ea-145">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="836ea-146">Fyzické úložiště je potenciálně levnější než použití služby úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="836ea-146">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="836ea-147">Proces aplikace musí mít oprávnění ke čtení a zápisu do umístění úložiště.</span><span class="sxs-lookup"><span data-stu-id="836ea-147">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="836ea-148">**Nikdy neudělujte oprávnění EXECUTE.**</span><span class="sxs-lookup"><span data-stu-id="836ea-148">**Never grant execute permission.**</span></span>

* <span data-ttu-id="836ea-149">Služba úložiště dat (například [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="836ea-149">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="836ea-150">Služby obvykle nabízejí vylepšenou škálovatelnost a odolnost proti místním řešením, které obvykle podléhají jednomu bodu selhání.</span><span class="sxs-lookup"><span data-stu-id="836ea-150">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="836ea-151">Služby jsou potenciálně nižší náklady ve scénářích infrastruktury velkých úložišť.</span><span class="sxs-lookup"><span data-stu-id="836ea-151">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="836ea-152">Další informace najdete v tématu [rychlý Start: použití .NET k vytvoření objektu BLOB v úložišti objektů](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="836ea-152">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="836ea-153">Téma ukazuje <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, ale <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> lze použít k uložení <xref:System.IO.FileStream> do úložiště objektů BLOB při práci s <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="836ea-153">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="836ea-154">Scénáře nahrávání souborů</span><span class="sxs-lookup"><span data-stu-id="836ea-154">File upload scenarios</span></span>

<span data-ttu-id="836ea-155">Dva obecné přístupy k nahrávání souborů jsou ukládání do vyrovnávací paměti a streamování.</span><span class="sxs-lookup"><span data-stu-id="836ea-155">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="836ea-156">**Do vyrovnávací paměti**</span><span class="sxs-lookup"><span data-stu-id="836ea-156">**Buffering**</span></span>

<span data-ttu-id="836ea-157">Celý soubor se přečte do <xref:Microsoft.AspNetCore.Http.IFormFile>, což je C# reprezentace souboru, který se používá ke zpracování nebo uložení souboru.</span><span class="sxs-lookup"><span data-stu-id="836ea-157">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="836ea-158">Prostředky (disk, paměť) používané při nahrávání souborů závisí na počtu a velikosti souběžných nahrávání souborů.</span><span class="sxs-lookup"><span data-stu-id="836ea-158">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="836ea-159">Pokud se aplikace pokusí do vyrovnávací paměti příliš mnoho nahrávání, dojde k selhání lokality, když dojde k vynechání paměti nebo místa na disku.</span><span class="sxs-lookup"><span data-stu-id="836ea-159">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="836ea-160">Pokud velikost nebo frekvence nahrávání souborů vyčerpá prostředky aplikace, použijte streamování.</span><span class="sxs-lookup"><span data-stu-id="836ea-160">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="836ea-161">Z paměti do dočasného souboru na disku se přesune libovolný soubor s vyrovnávací pamětí větší než 64 KB.</span><span class="sxs-lookup"><span data-stu-id="836ea-161">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="836ea-162">Ukládání malých souborů do vyrovnávací paměti je popsáno v následujících částech tohoto tématu:</span><span class="sxs-lookup"><span data-stu-id="836ea-162">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="836ea-163">Fyzické úložiště</span><span class="sxs-lookup"><span data-stu-id="836ea-163">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="836ea-164">Databáze</span><span class="sxs-lookup"><span data-stu-id="836ea-164">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="836ea-165">**Streamování**</span><span class="sxs-lookup"><span data-stu-id="836ea-165">**Streaming**</span></span>

<span data-ttu-id="836ea-166">Soubor se přijímá z požadavku na více částí a přímo se zpracovává nebo ukládá v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="836ea-166">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="836ea-167">Streamování nijak významně nezvyšuje výkon.</span><span class="sxs-lookup"><span data-stu-id="836ea-167">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="836ea-168">Streamování snižuje nároky na paměť nebo místo na disku při nahrávání souborů.</span><span class="sxs-lookup"><span data-stu-id="836ea-168">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="836ea-169">Streamování velkých souborů je zahrnuté v části [nahrávání velkých souborů pomocí streamování](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="836ea-169">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="836ea-170">Nahrávání malých souborů s vazbou modelu ve vyrovnávací paměti na fyzické úložiště</span><span class="sxs-lookup"><span data-stu-id="836ea-170">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="836ea-171">Pro nahrání malých souborů použijte formulář s více částmi nebo sestavte požadavek POST pomocí JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="836ea-171">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="836ea-172">Následující příklad ukazuje použití formuláře Razor Pages k nahrání jednoho souboru (*Pages/BufferedSingleFileUploadPhysical. cshtml* do ukázkové aplikace):</span><span class="sxs-lookup"><span data-stu-id="836ea-172">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="836ea-173">Následující příklad je podobný předchozímu příkladu s tím rozdílem, že:</span><span class="sxs-lookup"><span data-stu-id="836ea-173">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="836ea-174">K odeslání dat formuláře se používá JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)).</span><span class="sxs-lookup"><span data-stu-id="836ea-174">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="836ea-175">Neexistuje žádné ověření.</span><span class="sxs-lookup"><span data-stu-id="836ea-175">There's no validation.</span></span>

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

<span data-ttu-id="836ea-176">Chcete-li provést příspěvek formuláře v jazyce JavaScript pro klienty, kteří [nepodporují rozhraní API pro načítání](https://caniuse.com/#feat=fetch), použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="836ea-176">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="836ea-177">Použijte načtenou výplň (například [window. Fetch Fill (GitHub/Fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="836ea-177">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="836ea-178">Použijte `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="836ea-178">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="836ea-179">Příklad:</span><span class="sxs-lookup"><span data-stu-id="836ea-179">For example:</span></span>

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

<span data-ttu-id="836ea-180">Aby bylo možné podporovat nahrávání souborů, musí formuláře HTML určovat typ kódování (`enctype`) `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="836ea-180">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="836ea-181">V případě, že vstupní prvek `files` podporuje nahrávání více souborů, zadejte atribut `multiple` pro element `<input>`:</span><span class="sxs-lookup"><span data-stu-id="836ea-181">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="836ea-182">Jednotlivé soubory nahrané na server jsou k dispozici prostřednictvím [vazby modelu](xref:mvc/models/model-binding) pomocí <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="836ea-182">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="836ea-183">Ukázková aplikace ukazuje více ukládání souborů do vyrovnávací paměti pro scénáře databáze a fyzických úložišť.</span><span class="sxs-lookup"><span data-stu-id="836ea-183">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

> [!WARNING]
> <span data-ttu-id="836ea-184">Nespoléhá se ani na nedůvěryhodnou vlastnost `FileName` <xref:Microsoft.AspNetCore.Http.IFormFile> bez ověření.</span><span class="sxs-lookup"><span data-stu-id="836ea-184">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="836ea-185">Vlastnost `FileName` by měla být použita pouze pro účely zobrazení a pouze po kódování hodnoty HTML.</span><span class="sxs-lookup"><span data-stu-id="836ea-185">The `FileName` property should only be used for display purposes and only after HTML encoding the value.</span></span>
>
> <span data-ttu-id="836ea-186">Zde uvedené příklady neberou ohled na zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="836ea-186">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="836ea-187">Další informace jsou k dispozici v následujících částech a [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="836ea-187">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="836ea-188">Důležité informace o zabezpečení</span><span class="sxs-lookup"><span data-stu-id="836ea-188">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="836ea-189">Ověřování</span><span class="sxs-lookup"><span data-stu-id="836ea-189">Validation</span></span>](#validation)

<span data-ttu-id="836ea-190">Při nahrávání souborů pomocí vazeb modelů a <xref:Microsoft.AspNetCore.Http.IFormFile> může metoda Action přijmout:</span><span class="sxs-lookup"><span data-stu-id="836ea-190">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="836ea-191">Jeden @no__t – 0.</span><span class="sxs-lookup"><span data-stu-id="836ea-191">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="836ea-192">Kterákoli z následujících kolekcí, které reprezentují několik souborů:</span><span class="sxs-lookup"><span data-stu-id="836ea-192">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="836ea-193">[Seznam](xref:System.Collections.Generic.List`1)\< @ no__t-2 @ no__t-3</span><span class="sxs-lookup"><span data-stu-id="836ea-193">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="836ea-194">Vazba odpovídá souborům formuláře podle názvu.</span><span class="sxs-lookup"><span data-stu-id="836ea-194">Binding matches form files by name.</span></span> <span data-ttu-id="836ea-195">Například hodnota HTML `name` v `<input type="file" name="formFile">` se musí shodovat s C# parametrem nebo vazbou vlastnosti (`FormFile`).</span><span class="sxs-lookup"><span data-stu-id="836ea-195">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="836ea-196">Další informace naleznete v části [název atributu matched na název parametru metody post](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="836ea-196">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="836ea-197">Následující příklad:</span><span class="sxs-lookup"><span data-stu-id="836ea-197">The following example:</span></span>

* <span data-ttu-id="836ea-198">Projde jedním nebo více nahranými soubory.</span><span class="sxs-lookup"><span data-stu-id="836ea-198">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="836ea-199">Pomocí [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) vrátí úplnou cestu k souboru, včetně názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="836ea-199">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="836ea-200">Uloží soubory do místního systému souborů pomocí názvu souboru generovaného aplikací.</span><span class="sxs-lookup"><span data-stu-id="836ea-200">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="836ea-201">Vrátí celkový počet nahraných souborů a jejich velikost.</span><span class="sxs-lookup"><span data-stu-id="836ea-201">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="836ea-202">K vygenerování názvu souboru bez cesty použijte `Path.GetRandomFileName`.</span><span class="sxs-lookup"><span data-stu-id="836ea-202">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="836ea-203">V následujícím příkladu je cesta získána z konfigurace:</span><span class="sxs-lookup"><span data-stu-id="836ea-203">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="836ea-204">Cesta předaná <xref:System.IO.FileStream> *musí* zahrnovat název souboru.</span><span class="sxs-lookup"><span data-stu-id="836ea-204">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="836ea-205">Pokud není zadán název souboru, je při spuštění vyvolána hodnota <xref:System.UnauthorizedAccessException>.</span><span class="sxs-lookup"><span data-stu-id="836ea-205">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="836ea-206">Soubory odeslané pomocí techniky <xref:Microsoft.AspNetCore.Http.IFormFile> jsou uloženy do vyrovnávací paměti nebo na disku na serveru před zpracováním.</span><span class="sxs-lookup"><span data-stu-id="836ea-206">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="836ea-207">V rámci metody Action je obsah <xref:Microsoft.AspNetCore.Http.IFormFile> přístupný jako <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="836ea-207">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="836ea-208">Kromě místního systému souborů je možné soubory ukládat do síťové sdílené složky nebo do služby úložiště souborů, jako je [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="836ea-208">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="836ea-209">Další příklad, který projde několik souborů pro nahrání a používá bezpečné názvy souborů, najdete v ukázkové aplikaci v části *Pages/BufferedMultipleFileUploadPhysical. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="836ea-209">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="836ea-210">[Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) vyvolá výjimku <xref:System.IO.IOException>, pokud jsou vytvořeny více než 65 535 souborů bez odstranění předchozích dočasných souborů.</span><span class="sxs-lookup"><span data-stu-id="836ea-210">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="836ea-211">Limit 65 535 souborů je omezen na server.</span><span class="sxs-lookup"><span data-stu-id="836ea-211">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="836ea-212">Další informace o tomto limitu pro operační systém Windows najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="836ea-212">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="836ea-213">GetTempFileNameA – funkce</span><span class="sxs-lookup"><span data-stu-id="836ea-213">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="836ea-214">Nahrávání malých souborů s vazbou modelu s vyrovnávací pamětí do databáze</span><span class="sxs-lookup"><span data-stu-id="836ea-214">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="836ea-215">Chcete-li uložit data binárního souboru do databáze pomocí [Entity Framework](/ef/core/index), definujte v entitě vlastnost pole <xref:System.Byte>:</span><span class="sxs-lookup"><span data-stu-id="836ea-215">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="836ea-216">Zadejte vlastnost modelu stránky pro třídu, která obsahuje <xref:Microsoft.AspNetCore.Http.IFormFile>:</span><span class="sxs-lookup"><span data-stu-id="836ea-216">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="836ea-217"><xref:Microsoft.AspNetCore.Http.IFormFile> lze použít přímo jako parametr metody akce nebo jako vlastnost vázaného modelu.</span><span class="sxs-lookup"><span data-stu-id="836ea-217"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="836ea-218">Předchozí příklad používá vlastnost vázaného modelu.</span><span class="sxs-lookup"><span data-stu-id="836ea-218">The prior example uses a bound model property.</span></span>

<span data-ttu-id="836ea-219">Ve formuláři Razor Pages se používá `FileUpload`:</span><span class="sxs-lookup"><span data-stu-id="836ea-219">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="836ea-220">Když je formulář publikovaný na serveru, zkopírujte <xref:Microsoft.AspNetCore.Http.IFormFile> do datového proudu a uložte ho jako pole bajtů v databázi.</span><span class="sxs-lookup"><span data-stu-id="836ea-220">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="836ea-221">V následujícím příkladu `_dbContext` ukládá kontext databáze aplikace:</span><span class="sxs-lookup"><span data-stu-id="836ea-221">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="836ea-222">Předchozí příklad je podobný scénáři, který je znázorněný v ukázkové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="836ea-222">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="836ea-223">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="836ea-223">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="836ea-224">*Pages/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="836ea-224">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="836ea-225">Při ukládání binárních dat do relačních databází buďte opatrní, protože to může mít nepříznivý vliv na výkon.</span><span class="sxs-lookup"><span data-stu-id="836ea-225">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="836ea-226">Nespoléhá se ani na nedůvěryhodnou vlastnost `FileName` <xref:Microsoft.AspNetCore.Http.IFormFile> bez ověření.</span><span class="sxs-lookup"><span data-stu-id="836ea-226">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="836ea-227">Vlastnost `FileName` by měla být použita pouze pro účely zobrazení a pouze po kódování HTML.</span><span class="sxs-lookup"><span data-stu-id="836ea-227">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="836ea-228">Uvedené příklady nevezmou ohled na zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="836ea-228">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="836ea-229">Další informace jsou k dispozici v následujících částech a [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="836ea-229">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="836ea-230">Důležité informace o zabezpečení</span><span class="sxs-lookup"><span data-stu-id="836ea-230">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="836ea-231">Ověřování</span><span class="sxs-lookup"><span data-stu-id="836ea-231">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="836ea-232">Nahrávání velkých souborů pomocí streamování</span><span class="sxs-lookup"><span data-stu-id="836ea-232">Upload large files with streaming</span></span>

<span data-ttu-id="836ea-233">Následující příklad ukazuje, jak použít JavaScript ke streamování souboru do akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="836ea-233">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="836ea-234">Token proti padělání souboru se generuje pomocí vlastního atributu filtru a předává se do hlaviček protokolu HTTP klienta místo v textu žádosti.</span><span class="sxs-lookup"><span data-stu-id="836ea-234">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="836ea-235">Vzhledem k tomu, že metoda akce zpracovává nahraná data přímo, vazba modelu formuláře je zakázána jiným vlastním filtrem.</span><span class="sxs-lookup"><span data-stu-id="836ea-235">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="836ea-236">V rámci akce je obsah formuláře čten pomocí `MultipartReader`, který čte každou jednotlivou `MultipartSection`, zpracovává soubor nebo ukládá obsah podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="836ea-236">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="836ea-237">Po načtení oddílů s více částmi provede akce vlastní vazbu modelu.</span><span class="sxs-lookup"><span data-stu-id="836ea-237">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="836ea-238">Počáteční odpověď stránky načte formulář a uloží token proti padělání do souboru cookie (prostřednictvím atributu `GenerateAntiforgeryTokenCookieAttribute`).</span><span class="sxs-lookup"><span data-stu-id="836ea-238">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="836ea-239">Atribut používá ASP.NET Core integrovanou [podporu proti padělání](xref:security/anti-request-forgery) pro nastavení souboru cookie s tokenem žádosti:</span><span class="sxs-lookup"><span data-stu-id="836ea-239">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="836ea-240">@No__t-0 se používá k zakázání vazby modelu:</span><span class="sxs-lookup"><span data-stu-id="836ea-240">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="836ea-241">V ukázkové aplikaci `GenerateAntiforgeryTokenCookieAttribute` a `DisableFormValueModelBindingAttribute` se aplikují jako filtry pro modely stránky aplikace `/StreamedSingleFileUploadDb` a `/StreamedSingleFileUploadPhysical` v `Startup.ConfigureServices` pomocí [konvencí Razor Pages](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="836ea-241">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=8-11,17-20)]

<span data-ttu-id="836ea-242">Vzhledem k tomu, že vazba modelu nepřečte formulář, parametry, které jsou svázané z formuláře, se nezobrazují (budou pokračovat v práci s dotazem, trasou a hlavičkou).</span><span class="sxs-lookup"><span data-stu-id="836ea-242">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="836ea-243">Metoda Action funguje přímo s vlastností `Request`.</span><span class="sxs-lookup"><span data-stu-id="836ea-243">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="836ea-244">Pro čtení jednotlivých oddílů se používá `MultipartReader`.</span><span class="sxs-lookup"><span data-stu-id="836ea-244">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="836ea-245">Data klíč/hodnota jsou uložena v `KeyValueAccumulator`.</span><span class="sxs-lookup"><span data-stu-id="836ea-245">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="836ea-246">Po načtení oddílů s více částmi se obsah `KeyValueAccumulator` použije pro svázání dat formuláře s typem modelu.</span><span class="sxs-lookup"><span data-stu-id="836ea-246">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="836ea-247">Kompletní metoda `StreamingController.UploadDatabase` pro streamování do databáze s EF Core:</span><span class="sxs-lookup"><span data-stu-id="836ea-247">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="836ea-248">`MultipartRequestHelper` (*nástroje/MultipartRequestHelper. cs*):</span><span class="sxs-lookup"><span data-stu-id="836ea-248">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="836ea-249">Úplná metoda `StreamingController.UploadPhysical` pro streamování do fyzického umístění:</span><span class="sxs-lookup"><span data-stu-id="836ea-249">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="836ea-250">V ukázkové aplikaci jsou kontroly ověřování zpracovávány `FileHelpers.ProcessStreamedFile`.</span><span class="sxs-lookup"><span data-stu-id="836ea-250">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="836ea-251">Ověřování</span><span class="sxs-lookup"><span data-stu-id="836ea-251">Validation</span></span>

<span data-ttu-id="836ea-252">Třída `FileHelpers` ukázkové aplikace ukazuje několik kontrol uložených do vyrovnávací paměti <xref:Microsoft.AspNetCore.Http.IFormFile> a streamovaná nahrávání souborů.</span><span class="sxs-lookup"><span data-stu-id="836ea-252">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="836ea-253">Pro zpracování @no__t ukládání souborů s vyrovnávací pamětí v ukázkové aplikaci si přečtěte část `ProcessFormFile` v souboru *Utilities/Helper. cs* .</span><span class="sxs-lookup"><span data-stu-id="836ea-253">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="836ea-254">Pro zpracování streamované soubory si Projděte metodu `ProcessStreamedFile` ve stejném souboru.</span><span class="sxs-lookup"><span data-stu-id="836ea-254">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="836ea-255">Metody zpracování ověřování, které jsou znázorněné v ukázkové aplikaci, nekontrolují obsah nahraných souborů.</span><span class="sxs-lookup"><span data-stu-id="836ea-255">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="836ea-256">Ve většině produkčních scénářů se v souboru používá rozhraní API pro skenování virů nebo malwaru, než je soubor dostupný uživatelům nebo jiným systémům.</span><span class="sxs-lookup"><span data-stu-id="836ea-256">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="836ea-257">I když ukázka tématu poskytuje pracovní příklad technik ověřování, Neimplementujte třídu `FileHelpers` v produkční aplikaci, pokud:</span><span class="sxs-lookup"><span data-stu-id="836ea-257">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="836ea-258">Plně rozumíte implementaci.</span><span class="sxs-lookup"><span data-stu-id="836ea-258">Fully understand the implementation.</span></span>
> * <span data-ttu-id="836ea-259">Upravte implementaci podle potřeby pro prostředí a specifikace aplikace.</span><span class="sxs-lookup"><span data-stu-id="836ea-259">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="836ea-260">**Nikdy nepoužívejte nerozlišený kód zabezpečení v aplikaci bez nutnosti řešit tyto požadavky.**</span><span class="sxs-lookup"><span data-stu-id="836ea-260">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="836ea-261">Ověření obsahu</span><span class="sxs-lookup"><span data-stu-id="836ea-261">Content validation</span></span>

<span data-ttu-id="836ea-262">**Pro nahraný obsah použijte rozhraní API pro kontrolu virů a malwaru třetí strany.**</span><span class="sxs-lookup"><span data-stu-id="836ea-262">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="836ea-263">Prohledávání souborů je náročné na prostředky serveru ve scénářích s vysokým objemem.</span><span class="sxs-lookup"><span data-stu-id="836ea-263">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="836ea-264">Pokud dojde ke snížení výkonu zpracování požadavků z důvodu kontroly souborů, zvažte přesměrování práce skenování na službu na [pozadí](xref:fundamentals/host/hosted-services), případně služby spuštěné na serveru, který se liší od serveru aplikace.</span><span class="sxs-lookup"><span data-stu-id="836ea-264">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="836ea-265">Nahrané soubory se obvykle uchovávají v oblasti v karanténě, dokud je kontrola virů na pozadí nevrátí.</span><span class="sxs-lookup"><span data-stu-id="836ea-265">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="836ea-266">Když soubor projde, soubor se přesune do normálního umístění úložiště souborů.</span><span class="sxs-lookup"><span data-stu-id="836ea-266">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="836ea-267">Tyto kroky se obvykle provádí ve spojení s databázovým záznamem, který indikuje stav kontroly souboru.</span><span class="sxs-lookup"><span data-stu-id="836ea-267">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="836ea-268">Při použití takového přístupu zůstane aplikace a Server App zaměřené na reakci na požadavky.</span><span class="sxs-lookup"><span data-stu-id="836ea-268">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="836ea-269">Ověření přípony souboru</span><span class="sxs-lookup"><span data-stu-id="836ea-269">File extension validation</span></span>

<span data-ttu-id="836ea-270">Přípona nahraného souboru by měla být zaškrtnutá na seznamu povolených rozšíření.</span><span class="sxs-lookup"><span data-stu-id="836ea-270">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="836ea-271">Příklad:</span><span class="sxs-lookup"><span data-stu-id="836ea-271">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="836ea-272">Ověření podpisu souboru</span><span class="sxs-lookup"><span data-stu-id="836ea-272">File signature validation</span></span>

<span data-ttu-id="836ea-273">Podpis souboru se určuje na prvních několika bajtech na začátku souboru.</span><span class="sxs-lookup"><span data-stu-id="836ea-273">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="836ea-274">Tyto bajty lze použít k určení, zda přípona odpovídá obsahu souboru.</span><span class="sxs-lookup"><span data-stu-id="836ea-274">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="836ea-275">Ukázková aplikace zkontroluje podpisy souborů pro několik běžných typů souborů.</span><span class="sxs-lookup"><span data-stu-id="836ea-275">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="836ea-276">V následujícím příkladu se pro tento soubor kontroluje podpis souboru obrázku JPEG:</span><span class="sxs-lookup"><span data-stu-id="836ea-276">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="836ea-277">Další signatury souborů získáte v dokumentaci [signatury souborů](https://www.filesignatures.net/) a oficiálních souborů.</span><span class="sxs-lookup"><span data-stu-id="836ea-277">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="836ea-278">Zabezpečení názvu souboru</span><span class="sxs-lookup"><span data-stu-id="836ea-278">File name security</span></span>

<span data-ttu-id="836ea-279">Nikdy nepoužívejte název souboru dodaný klientem pro uložení souboru do fyzického úložiště.</span><span class="sxs-lookup"><span data-stu-id="836ea-279">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="836ea-280">Vytvořte bezpečný název souboru pro soubor pomocí [cesty. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) nebo [cesty. GetTempFileName](xref:System.IO.Path.GetTempFileName*) a vytvořte úplnou cestu (včetně názvu souboru) pro dočasné úložiště.</span><span class="sxs-lookup"><span data-stu-id="836ea-280">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="836ea-281">Razor automaticky kóduje hodnoty vlastností pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="836ea-281">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="836ea-282">Následující kód je bezpečné použít:</span><span class="sxs-lookup"><span data-stu-id="836ea-282">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="836ea-283">Mimo Razor, vždy <xref:System.Net.WebUtility.HtmlEncode*> obsah názvu souboru z požadavku uživatele.</span><span class="sxs-lookup"><span data-stu-id="836ea-283">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="836ea-284">Mnoho implementací musí zahrnovat kontrolu, že soubor existuje. v opačném případě je soubor přepsán souborem se stejným názvem.</span><span class="sxs-lookup"><span data-stu-id="836ea-284">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="836ea-285">Poskytněte další logiku pro splnění specifikací vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="836ea-285">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="836ea-286">Ověřování velikosti</span><span class="sxs-lookup"><span data-stu-id="836ea-286">Size validation</span></span>

<span data-ttu-id="836ea-287">Omezte velikost nahraných souborů.</span><span class="sxs-lookup"><span data-stu-id="836ea-287">Limit the size of uploaded files.</span></span>

<span data-ttu-id="836ea-288">V ukázkové aplikaci je velikost souboru omezená na 2 MB (uvedené v bajtech).</span><span class="sxs-lookup"><span data-stu-id="836ea-288">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="836ea-289">Limit je zadán prostřednictvím [Konfigurace](xref:fundamentals/configuration/index) ze souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="836ea-289">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="836ea-290">@No__t-0 je vložen do tříd `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="836ea-290">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="836ea-291">Když velikost souboru překročí limit, soubor se odmítne:</span><span class="sxs-lookup"><span data-stu-id="836ea-291">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="836ea-292">Porovnává hodnotu atributu name s parametrem název metody POST</span><span class="sxs-lookup"><span data-stu-id="836ea-292">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="836ea-293">V nestandardních formulářích, které odesílají data formuláře nebo přímo využívají @no__t JavaScriptu, se název zadaný v prvku formuláře nebo `FormData` musí shodovat s názvem parametru v akci kontroleru.</span><span class="sxs-lookup"><span data-stu-id="836ea-293">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="836ea-294">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="836ea-294">In the following example:</span></span>

* <span data-ttu-id="836ea-295">Při použití elementu `<input>` je atribut `name` nastaven na hodnotu `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="836ea-295">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="836ea-296">Při použití `FormData` v jazyce JavaScript je název nastaven na hodnotu `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="836ea-296">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="836ea-297">Pro parametr C# metody použijte stejný název (`battlePlans`):</span><span class="sxs-lookup"><span data-stu-id="836ea-297">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="836ea-298">Pro Razor Pages metodu obslužné rutiny stránky s názvem `Upload`:</span><span class="sxs-lookup"><span data-stu-id="836ea-298">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="836ea-299">Pro metodu akce po kontroléru MVC:</span><span class="sxs-lookup"><span data-stu-id="836ea-299">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="836ea-300">Konfigurace serveru a aplikace</span><span class="sxs-lookup"><span data-stu-id="836ea-300">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="836ea-301">Omezení délky těla částí</span><span class="sxs-lookup"><span data-stu-id="836ea-301">Multipart body length limit</span></span>

<span data-ttu-id="836ea-302"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> nastaví limit délky jednotlivých částí části.</span><span class="sxs-lookup"><span data-stu-id="836ea-302"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="836ea-303">Oddíly formuláře, které překračují toto omezení, vyvolávají při analýze <xref:System.IO.InvalidDataException>.</span><span class="sxs-lookup"><span data-stu-id="836ea-303">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="836ea-304">Výchozí hodnota je 134 217 728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="836ea-304">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="836ea-305">Upravte limit pomocí nastavení <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="836ea-305">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="836ea-306"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> slouží k nastavení <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> pro jednu stránku nebo akci.</span><span class="sxs-lookup"><span data-stu-id="836ea-306"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="836ea-307">V aplikaci Razor Pages použijte filtr s [konvencí](xref:razor-pages/razor-pages-conventions) v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="836ea-307">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="836ea-308">V aplikaci Razor Pages nebo aplikaci MVC použijte filtr na model stránky nebo metodu akce:</span><span class="sxs-lookup"><span data-stu-id="836ea-308">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="836ea-309">Kestrel maximální velikost textu požadavku</span><span class="sxs-lookup"><span data-stu-id="836ea-309">Kestrel maximum request body size</span></span>

<span data-ttu-id="836ea-310">Pro aplikace hostované v Kestrel je výchozí maximální velikost textu požadavku 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="836ea-310">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="836ea-311">Přizpůsobte limit pomocí možnosti serveru [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:</span><span class="sxs-lookup"><span data-stu-id="836ea-311">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="836ea-312"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> slouží k nastavení [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) pro jednu stránku nebo akci.</span><span class="sxs-lookup"><span data-stu-id="836ea-312"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="836ea-313">V aplikaci Razor Pages použijte filtr s [konvencí](xref:razor-pages/razor-pages-conventions) v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="836ea-313">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="836ea-314">V aplikaci se stránkami Razor nebo v aplikaci MVC použijte filtr na třídu obslužné rutiny stránky nebo na metodu akce:</span><span class="sxs-lookup"><span data-stu-id="836ea-314">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="836ea-315">@No__t-0 lze také použít pomocí direktivy [@attribute](xref:mvc/views/razor#attribute) Razor:</span><span class="sxs-lookup"><span data-stu-id="836ea-315">The `RequestSizeLimitAttribute` can also be applied using the [@attribute](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="836ea-316">Další omezení Kestrel</span><span class="sxs-lookup"><span data-stu-id="836ea-316">Other Kestrel limits</span></span>

<span data-ttu-id="836ea-317">Pro aplikace hostované v Kestrel se můžou vztahovat další omezení Kestrel:</span><span class="sxs-lookup"><span data-stu-id="836ea-317">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="836ea-318">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="836ea-318">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="836ea-319">Sazby za data požadavků a odpovědí</span><span class="sxs-lookup"><span data-stu-id="836ea-319">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="836ea-320">Omezení délky obsahu služby IIS</span><span class="sxs-lookup"><span data-stu-id="836ea-320">IIS content length limit</span></span>

<span data-ttu-id="836ea-321">Výchozí limit počtu požadavků (`maxAllowedContentLength`) je 30 000 000 bajtů, což je přibližně 28.6 MB.</span><span class="sxs-lookup"><span data-stu-id="836ea-321">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="836ea-322">Upravte limit v souboru *Web. config* :</span><span class="sxs-lookup"><span data-stu-id="836ea-322">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="836ea-323">Toto nastavení platí pouze pro službu IIS.</span><span class="sxs-lookup"><span data-stu-id="836ea-323">This setting only applies to IIS.</span></span> <span data-ttu-id="836ea-324">K tomuto chování nedochází ve výchozím nastavení při hostování v Kestrel.</span><span class="sxs-lookup"><span data-stu-id="836ea-324">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="836ea-325">Další informace najdete v tématu [omezení požadavků \<requestLimits >](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="836ea-325">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="836ea-326">Omezení v modulu ASP.NET Core nebo přítomnosti modulu filtrování požadavků služby IIS mohou omezit nahrávání na 2 nebo 4 GB.</span><span class="sxs-lookup"><span data-stu-id="836ea-326">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="836ea-327">Další informace najdete v tématu [nelze odeslat soubor o velikosti větší než 2 GB (ASPNET/AspNetCore #2711)](https://github.com/aspnet/AspNetCore/issues/2711).</span><span class="sxs-lookup"><span data-stu-id="836ea-327">For more information, see [Unable to upload file greater than 2GB in size (aspnet/AspNetCore #2711)](https://github.com/aspnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="836ea-328">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="836ea-328">Troubleshoot</span></span>

<span data-ttu-id="836ea-329">Níže jsou uvedeny některé běžné problémy, které se vyskytly při práci s nahráváním souborů a jejich možnými řešeními.</span><span class="sxs-lookup"><span data-stu-id="836ea-329">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="836ea-330">Při nasazení na server služby IIS se nenašla chyba.</span><span class="sxs-lookup"><span data-stu-id="836ea-330">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="836ea-331">Následující chyba znamená, že nahraný soubor překračuje délku nakonfigurovaného obsahu serveru:</span><span class="sxs-lookup"><span data-stu-id="836ea-331">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="836ea-332">Další informace o zvýšení limitu najdete v části [omezení délky obsahu služby IIS](#iis-content-length-limit) .</span><span class="sxs-lookup"><span data-stu-id="836ea-332">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="836ea-333">Chyba připojení</span><span class="sxs-lookup"><span data-stu-id="836ea-333">Connection failure</span></span>

<span data-ttu-id="836ea-334">Chyba připojení a připojení k serveru pro resetování pravděpodobně znamená, že nahraný soubor překračuje maximální velikost textu požadavku Kestrel.</span><span class="sxs-lookup"><span data-stu-id="836ea-334">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="836ea-335">Další informace najdete v části [Kestrel maximální velikost textu požadavku](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="836ea-335">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="836ea-336">Omezení připojení klientů Kestrel mohou také vyžadovat úpravu.</span><span class="sxs-lookup"><span data-stu-id="836ea-336">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="836ea-337">Výjimka odkazu s hodnotou null s IFormFile</span><span class="sxs-lookup"><span data-stu-id="836ea-337">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="836ea-338">Pokud kontroler přijímá odeslané soubory pomocí <xref:Microsoft.AspNetCore.Http.IFormFile>, ale hodnota je `null`, potvrďte, že formulář HTML určuje hodnotu `enctype` `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="836ea-338">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="836ea-339">Pokud tento atribut není nastaven u elementu `<form>`, odeslání souboru neproběhne a všechny vázané argumenty <xref:Microsoft.AspNetCore.Http.IFormFile> jsou `null`.</span><span class="sxs-lookup"><span data-stu-id="836ea-339">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="836ea-340">Ujistěte se také, že [nahrávání názvů v datech formuláře odpovídá pojmenování aplikace](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="836ea-340">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="836ea-341">ASP.NET Core podporuje nahrávání jednoho nebo více souborů pomocí vazby modelu ve vyrovnávací paměti pro menší soubory a streamování bez vyrovnávací paměti pro větší soubory.</span><span class="sxs-lookup"><span data-stu-id="836ea-341">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="836ea-342">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="836ea-342">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="836ea-343">Důležité informace o zabezpečení</span><span class="sxs-lookup"><span data-stu-id="836ea-343">Security considerations</span></span>

<span data-ttu-id="836ea-344">Pokud chcete uživatelům poskytnout možnost nahrávat soubory na server, buďte opatrní.</span><span class="sxs-lookup"><span data-stu-id="836ea-344">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="836ea-345">Útočníci se můžou pokusit:</span><span class="sxs-lookup"><span data-stu-id="836ea-345">Attackers may attempt to:</span></span>

* <span data-ttu-id="836ea-346">Vykoná útok [DOS (Denial of Service](/windows-hardware/drivers/ifs/denial-of-service) ).</span><span class="sxs-lookup"><span data-stu-id="836ea-346">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="836ea-347">Nahrání virů nebo malwaru</span><span class="sxs-lookup"><span data-stu-id="836ea-347">Upload viruses or malware.</span></span>
* <span data-ttu-id="836ea-348">Narušit sítě a servery jinými způsoby.</span><span class="sxs-lookup"><span data-stu-id="836ea-348">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="836ea-349">Bezpečnostní kroky, které snižují pravděpodobnost úspěšného útoku, jsou:</span><span class="sxs-lookup"><span data-stu-id="836ea-349">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="836ea-350">Nahrajte soubory do vyhrazené oblasti pro nahrání souborů v systému, nejlépe pro nesystémovou jednotku.</span><span class="sxs-lookup"><span data-stu-id="836ea-350">Upload files to a dedicated file upload area on the system, preferably to a non-system drive.</span></span> <span data-ttu-id="836ea-351">Použití vyhrazeného umístění usnadňuje omezení zabezpečení pro nahrané soubory.</span><span class="sxs-lookup"><span data-stu-id="836ea-351">Using a dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="836ea-352">Zakažte oprávnění EXECUTE pro umístění pro nahrání souboru. &dagger;</span><span class="sxs-lookup"><span data-stu-id="836ea-352">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="836ea-353">Neuchovávat nahrané soubory ve stejném adresářovém stromu jako aplikace. &dagger;</span><span class="sxs-lookup"><span data-stu-id="836ea-353">Never persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="836ea-354">Použijte název bezpečného souboru určený aplikací.</span><span class="sxs-lookup"><span data-stu-id="836ea-354">Use a safe file name determined by the app.</span></span> <span data-ttu-id="836ea-355">Nepoužívejte název souboru poskytnutý uživatelem nebo nedůvěryhodného názvu nahraného souboru. &dagger; Chcete-li zobrazit nedůvěryhodný název souboru v uživatelském rozhraní nebo v zprávě protokolování, HTML hodnotu zakódovat.</span><span class="sxs-lookup"><span data-stu-id="836ea-355">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; To display an untrusted file name in a UI or in a logging message, HTML-encode the value.</span></span>
* <span data-ttu-id="836ea-356">Povoluje pouze konkrétní sadu schválených přípon souborů. &dagger;</span><span class="sxs-lookup"><span data-stu-id="836ea-356">Only allow a specific set of approved file extensions.&dagger;</span></span>
* <span data-ttu-id="836ea-357">Ověřte podpis formátu souboru, abyste zabránili uživateli v nahrávání maskovaných souborů. &dagger; například nepovolíte uživateli odeslat soubor *. exe* s příponou *. txt* .</span><span class="sxs-lookup"><span data-stu-id="836ea-357">Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension.</span></span>
* <span data-ttu-id="836ea-358">Ověřte, zda jsou na serveru provedeny také kontroly na straně klienta. &dagger; kontroly na straně klienta se snadno obejít.</span><span class="sxs-lookup"><span data-stu-id="836ea-358">Verify that client-side checks are also performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="836ea-359">Ověřte velikost nahraného souboru a zabraňte nahrávání, které jsou větší, než se očekávalo. &dagger;</span><span class="sxs-lookup"><span data-stu-id="836ea-359">Check the size of an uploaded file and prevent uploads that are larger than expected.&dagger;</span></span>
* <span data-ttu-id="836ea-360">Pokud by soubory neměly být přepsány nahraným souborem se stejným názvem, před nahráním souboru ověřte název souboru proti databázi nebo fyzickému úložišti.</span><span class="sxs-lookup"><span data-stu-id="836ea-360">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="836ea-361">**Před uložením souboru spusťte v nahraném obsahu skener virů nebo malwaru.**</span><span class="sxs-lookup"><span data-stu-id="836ea-361">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="836ea-362">Ukázková aplikace &dagger;The ukazuje přístup, který splňuje kritéria.</span><span class="sxs-lookup"><span data-stu-id="836ea-362">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="836ea-363">Nahrávání škodlivého kódu do systému je často prvním krokem ke spuštění kódu, který může:</span><span class="sxs-lookup"><span data-stu-id="836ea-363">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="836ea-364">Zcela získá kontrolu nad systémem.</span><span class="sxs-lookup"><span data-stu-id="836ea-364">Completely gain control of a system.</span></span>
> * <span data-ttu-id="836ea-365">Přetížit systém s výsledkem, že dojde k chybě systému.</span><span class="sxs-lookup"><span data-stu-id="836ea-365">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="836ea-366">Napadnout data uživatelů nebo systémových dat.</span><span class="sxs-lookup"><span data-stu-id="836ea-366">Compromise user or system data.</span></span>
> * <span data-ttu-id="836ea-367">Použijte graffiti pro veřejné uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="836ea-367">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="836ea-368">Informace o omezení oblasti útoku při přijímání souborů uživateli najdete v následujících zdrojích informací:</span><span class="sxs-lookup"><span data-stu-id="836ea-368">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="836ea-369">Neomezená nahrávání souboru</span><span class="sxs-lookup"><span data-stu-id="836ea-369">Unrestricted File Upload</span></span>](https://www.owasp.org/index.php/Unrestricted_File_Upload)
> * [<span data-ttu-id="836ea-370">Zabezpečení Azure: Ujistěte se, že jsou při přijímání souborů od uživatelů k dismístě správné ovládací prvky.</span><span class="sxs-lookup"><span data-stu-id="836ea-370">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="836ea-371">Další informace o implementaci bezpečnostních opatření, včetně příkladů z ukázkové aplikace, najdete v části [ověření](#validation) .</span><span class="sxs-lookup"><span data-stu-id="836ea-371">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="836ea-372">Scénáře úložiště</span><span class="sxs-lookup"><span data-stu-id="836ea-372">Storage scenarios</span></span>

<span data-ttu-id="836ea-373">Mezi běžné možnosti úložiště pro soubory patří:</span><span class="sxs-lookup"><span data-stu-id="836ea-373">Common storage options for files include:</span></span>

* <span data-ttu-id="836ea-374">Databáze</span><span class="sxs-lookup"><span data-stu-id="836ea-374">Database</span></span>

  * <span data-ttu-id="836ea-375">U malých nahrávání souborů je databáze často rychlejší než možnosti fyzického úložiště (systému souborů nebo síťového sdílení).</span><span class="sxs-lookup"><span data-stu-id="836ea-375">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="836ea-376">Databáze je často pohodlnější než možnosti fyzického úložiště, protože načtení záznamu databáze pro uživatelská data může současně poskytovat obsah souboru (například obrázek miniatury).</span><span class="sxs-lookup"><span data-stu-id="836ea-376">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="836ea-377">Databáze je potenciálně levnější než použití služby úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="836ea-377">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="836ea-378">Fyzické úložiště (systém souborů nebo síťová sdílená složka)</span><span class="sxs-lookup"><span data-stu-id="836ea-378">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="836ea-379">Pro nahrávání velkých souborů:</span><span class="sxs-lookup"><span data-stu-id="836ea-379">For large file uploads:</span></span>
    * <span data-ttu-id="836ea-380">Omezení databáze mohou omezit velikost nahrávání.</span><span class="sxs-lookup"><span data-stu-id="836ea-380">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="836ea-381">Fyzické úložiště je často méně hospodárné než úložiště v databázi.</span><span class="sxs-lookup"><span data-stu-id="836ea-381">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="836ea-382">Fyzické úložiště je potenciálně levnější než použití služby úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="836ea-382">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="836ea-383">Proces aplikace musí mít oprávnění ke čtení a zápisu do umístění úložiště.</span><span class="sxs-lookup"><span data-stu-id="836ea-383">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="836ea-384">**Nikdy neudělujte oprávnění EXECUTE.**</span><span class="sxs-lookup"><span data-stu-id="836ea-384">**Never grant execute permission.**</span></span>

* <span data-ttu-id="836ea-385">Služba úložiště dat (například [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="836ea-385">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="836ea-386">Služby obvykle nabízejí vylepšenou škálovatelnost a odolnost proti místním řešením, které obvykle podléhají jednomu bodu selhání.</span><span class="sxs-lookup"><span data-stu-id="836ea-386">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="836ea-387">Služby jsou potenciálně nižší náklady ve scénářích infrastruktury velkých úložišť.</span><span class="sxs-lookup"><span data-stu-id="836ea-387">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="836ea-388">Další informace najdete v tématu [rychlý Start: použití .NET k vytvoření objektu BLOB v úložišti objektů](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="836ea-388">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="836ea-389">Téma ukazuje <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, ale <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> lze použít k uložení <xref:System.IO.FileStream> do úložiště objektů BLOB při práci s <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="836ea-389">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="836ea-390">Scénáře nahrávání souborů</span><span class="sxs-lookup"><span data-stu-id="836ea-390">File upload scenarios</span></span>

<span data-ttu-id="836ea-391">Dva obecné přístupy k nahrávání souborů jsou ukládání do vyrovnávací paměti a streamování.</span><span class="sxs-lookup"><span data-stu-id="836ea-391">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="836ea-392">**Do vyrovnávací paměti**</span><span class="sxs-lookup"><span data-stu-id="836ea-392">**Buffering**</span></span>

<span data-ttu-id="836ea-393">Celý soubor se přečte do <xref:Microsoft.AspNetCore.Http.IFormFile>, což je C# reprezentace souboru, který se používá ke zpracování nebo uložení souboru.</span><span class="sxs-lookup"><span data-stu-id="836ea-393">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="836ea-394">Prostředky (disk, paměť) používané při nahrávání souborů závisí na počtu a velikosti souběžných nahrávání souborů.</span><span class="sxs-lookup"><span data-stu-id="836ea-394">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="836ea-395">Pokud se aplikace pokusí do vyrovnávací paměti příliš mnoho nahrávání, dojde k selhání lokality, když dojde k vynechání paměti nebo místa na disku.</span><span class="sxs-lookup"><span data-stu-id="836ea-395">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="836ea-396">Pokud velikost nebo frekvence nahrávání souborů vyčerpá prostředky aplikace, použijte streamování.</span><span class="sxs-lookup"><span data-stu-id="836ea-396">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="836ea-397">Z paměti do dočasného souboru na disku se přesune libovolný soubor s vyrovnávací pamětí větší než 64 KB.</span><span class="sxs-lookup"><span data-stu-id="836ea-397">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="836ea-398">Ukládání malých souborů do vyrovnávací paměti je popsáno v následujících částech tohoto tématu:</span><span class="sxs-lookup"><span data-stu-id="836ea-398">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="836ea-399">Fyzické úložiště</span><span class="sxs-lookup"><span data-stu-id="836ea-399">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="836ea-400">Databáze</span><span class="sxs-lookup"><span data-stu-id="836ea-400">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="836ea-401">**Streamování**</span><span class="sxs-lookup"><span data-stu-id="836ea-401">**Streaming**</span></span>

<span data-ttu-id="836ea-402">Soubor se přijímá z požadavku na více částí a přímo se zpracovává nebo ukládá v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="836ea-402">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="836ea-403">Streamování nijak významně nezvyšuje výkon.</span><span class="sxs-lookup"><span data-stu-id="836ea-403">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="836ea-404">Streamování snižuje nároky na paměť nebo místo na disku při nahrávání souborů.</span><span class="sxs-lookup"><span data-stu-id="836ea-404">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="836ea-405">Streamování velkých souborů je zahrnuté v části [nahrávání velkých souborů pomocí streamování](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="836ea-405">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="836ea-406">Nahrávání malých souborů s vazbou modelu ve vyrovnávací paměti na fyzické úložiště</span><span class="sxs-lookup"><span data-stu-id="836ea-406">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="836ea-407">Pro nahrání malých souborů použijte formulář s více částmi nebo sestavte požadavek POST pomocí JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="836ea-407">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="836ea-408">Následující příklad ukazuje použití formuláře Razor Pages k nahrání jednoho souboru (*Pages/BufferedSingleFileUploadPhysical. cshtml* do ukázkové aplikace):</span><span class="sxs-lookup"><span data-stu-id="836ea-408">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="836ea-409">Následující příklad je podobný předchozímu příkladu s tím rozdílem, že:</span><span class="sxs-lookup"><span data-stu-id="836ea-409">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="836ea-410">K odeslání dat formuláře se používá JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)).</span><span class="sxs-lookup"><span data-stu-id="836ea-410">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="836ea-411">Neexistuje žádné ověření.</span><span class="sxs-lookup"><span data-stu-id="836ea-411">There's no validation.</span></span>

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

<span data-ttu-id="836ea-412">Chcete-li provést příspěvek formuláře v jazyce JavaScript pro klienty, kteří [nepodporují rozhraní API pro načítání](https://caniuse.com/#feat=fetch), použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="836ea-412">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="836ea-413">Použijte načtenou výplň (například [window. Fetch Fill (GitHub/Fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="836ea-413">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="836ea-414">Použijte `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="836ea-414">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="836ea-415">Příklad:</span><span class="sxs-lookup"><span data-stu-id="836ea-415">For example:</span></span>

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

<span data-ttu-id="836ea-416">Aby bylo možné podporovat nahrávání souborů, musí formuláře HTML určovat typ kódování (`enctype`) `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="836ea-416">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="836ea-417">V případě, že vstupní prvek `files` podporuje nahrávání více souborů, zadejte atribut `multiple` pro element `<input>`:</span><span class="sxs-lookup"><span data-stu-id="836ea-417">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="836ea-418">Jednotlivé soubory nahrané na server jsou k dispozici prostřednictvím [vazby modelu](xref:mvc/models/model-binding) pomocí <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="836ea-418">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="836ea-419">Ukázková aplikace ukazuje více ukládání souborů do vyrovnávací paměti pro scénáře databáze a fyzických úložišť.</span><span class="sxs-lookup"><span data-stu-id="836ea-419">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

> [!WARNING]
> <span data-ttu-id="836ea-420">Nespoléhá se ani na nedůvěryhodnou vlastnost `FileName` <xref:Microsoft.AspNetCore.Http.IFormFile> bez ověření.</span><span class="sxs-lookup"><span data-stu-id="836ea-420">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="836ea-421">Vlastnost `FileName` by měla být použita pouze pro účely zobrazení a pouze po kódování hodnoty HTML.</span><span class="sxs-lookup"><span data-stu-id="836ea-421">The `FileName` property should only be used for display purposes and only after HTML encoding the value.</span></span>
>
> <span data-ttu-id="836ea-422">Zde uvedené příklady neberou ohled na zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="836ea-422">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="836ea-423">Další informace jsou k dispozici v následujících částech a [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="836ea-423">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="836ea-424">Důležité informace o zabezpečení</span><span class="sxs-lookup"><span data-stu-id="836ea-424">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="836ea-425">Ověřování</span><span class="sxs-lookup"><span data-stu-id="836ea-425">Validation</span></span>](#validation)

<span data-ttu-id="836ea-426">Při nahrávání souborů pomocí vazeb modelů a <xref:Microsoft.AspNetCore.Http.IFormFile> může metoda Action přijmout:</span><span class="sxs-lookup"><span data-stu-id="836ea-426">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="836ea-427">Jeden @no__t – 0.</span><span class="sxs-lookup"><span data-stu-id="836ea-427">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="836ea-428">Kterákoli z následujících kolekcí, které reprezentují několik souborů:</span><span class="sxs-lookup"><span data-stu-id="836ea-428">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="836ea-429">[Seznam](xref:System.Collections.Generic.List`1)\< @ no__t-2 @ no__t-3</span><span class="sxs-lookup"><span data-stu-id="836ea-429">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="836ea-430">Vazba odpovídá souborům formuláře podle názvu.</span><span class="sxs-lookup"><span data-stu-id="836ea-430">Binding matches form files by name.</span></span> <span data-ttu-id="836ea-431">Například hodnota HTML `name` v `<input type="file" name="formFile">` se musí shodovat s C# parametrem nebo vazbou vlastnosti (`FormFile`).</span><span class="sxs-lookup"><span data-stu-id="836ea-431">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="836ea-432">Další informace naleznete v části [název atributu matched na název parametru metody post](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="836ea-432">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="836ea-433">Následující příklad:</span><span class="sxs-lookup"><span data-stu-id="836ea-433">The following example:</span></span>

* <span data-ttu-id="836ea-434">Projde jedním nebo více nahranými soubory.</span><span class="sxs-lookup"><span data-stu-id="836ea-434">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="836ea-435">Pomocí [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) vrátí úplnou cestu k souboru, včetně názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="836ea-435">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="836ea-436">Uloží soubory do místního systému souborů pomocí názvu souboru generovaného aplikací.</span><span class="sxs-lookup"><span data-stu-id="836ea-436">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="836ea-437">Vrátí celkový počet nahraných souborů a jejich velikost.</span><span class="sxs-lookup"><span data-stu-id="836ea-437">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="836ea-438">K vygenerování názvu souboru bez cesty použijte `Path.GetRandomFileName`.</span><span class="sxs-lookup"><span data-stu-id="836ea-438">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="836ea-439">V následujícím příkladu je cesta získána z konfigurace:</span><span class="sxs-lookup"><span data-stu-id="836ea-439">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="836ea-440">Cesta předaná <xref:System.IO.FileStream> *musí* zahrnovat název souboru.</span><span class="sxs-lookup"><span data-stu-id="836ea-440">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="836ea-441">Pokud není zadán název souboru, je při spuštění vyvolána hodnota <xref:System.UnauthorizedAccessException>.</span><span class="sxs-lookup"><span data-stu-id="836ea-441">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="836ea-442">Soubory odeslané pomocí techniky <xref:Microsoft.AspNetCore.Http.IFormFile> jsou uloženy do vyrovnávací paměti nebo na disku na serveru před zpracováním.</span><span class="sxs-lookup"><span data-stu-id="836ea-442">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="836ea-443">V rámci metody Action je obsah <xref:Microsoft.AspNetCore.Http.IFormFile> přístupný jako <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="836ea-443">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="836ea-444">Kromě místního systému souborů je možné soubory ukládat do síťové sdílené složky nebo do služby úložiště souborů, jako je [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="836ea-444">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="836ea-445">Další příklad, který projde několik souborů pro nahrání a používá bezpečné názvy souborů, najdete v ukázkové aplikaci v části *Pages/BufferedMultipleFileUploadPhysical. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="836ea-445">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="836ea-446">[Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) vyvolá výjimku <xref:System.IO.IOException>, pokud jsou vytvořeny více než 65 535 souborů bez odstranění předchozích dočasných souborů.</span><span class="sxs-lookup"><span data-stu-id="836ea-446">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="836ea-447">Limit 65 535 souborů je omezen na server.</span><span class="sxs-lookup"><span data-stu-id="836ea-447">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="836ea-448">Další informace o tomto limitu pro operační systém Windows najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="836ea-448">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="836ea-449">GetTempFileNameA – funkce</span><span class="sxs-lookup"><span data-stu-id="836ea-449">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="836ea-450">Nahrávání malých souborů s vazbou modelu s vyrovnávací pamětí do databáze</span><span class="sxs-lookup"><span data-stu-id="836ea-450">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="836ea-451">Chcete-li uložit data binárního souboru do databáze pomocí [Entity Framework](/ef/core/index), definujte v entitě vlastnost pole <xref:System.Byte>:</span><span class="sxs-lookup"><span data-stu-id="836ea-451">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="836ea-452">Zadejte vlastnost modelu stránky pro třídu, která obsahuje <xref:Microsoft.AspNetCore.Http.IFormFile>:</span><span class="sxs-lookup"><span data-stu-id="836ea-452">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="836ea-453"><xref:Microsoft.AspNetCore.Http.IFormFile> lze použít přímo jako parametr metody akce nebo jako vlastnost vázaného modelu.</span><span class="sxs-lookup"><span data-stu-id="836ea-453"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="836ea-454">Předchozí příklad používá vlastnost vázaného modelu.</span><span class="sxs-lookup"><span data-stu-id="836ea-454">The prior example uses a bound model property.</span></span>

<span data-ttu-id="836ea-455">Ve formuláři Razor Pages se používá `FileUpload`:</span><span class="sxs-lookup"><span data-stu-id="836ea-455">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="836ea-456">Když je formulář publikovaný na serveru, zkopírujte <xref:Microsoft.AspNetCore.Http.IFormFile> do datového proudu a uložte ho jako pole bajtů v databázi.</span><span class="sxs-lookup"><span data-stu-id="836ea-456">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="836ea-457">V následujícím příkladu `_dbContext` ukládá kontext databáze aplikace:</span><span class="sxs-lookup"><span data-stu-id="836ea-457">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="836ea-458">Předchozí příklad je podobný scénáři, který je znázorněný v ukázkové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="836ea-458">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="836ea-459">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="836ea-459">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="836ea-460">*Pages/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="836ea-460">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="836ea-461">Při ukládání binárních dat do relačních databází buďte opatrní, protože to může mít nepříznivý vliv na výkon.</span><span class="sxs-lookup"><span data-stu-id="836ea-461">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="836ea-462">Nespoléhá se ani na nedůvěryhodnou vlastnost `FileName` <xref:Microsoft.AspNetCore.Http.IFormFile> bez ověření.</span><span class="sxs-lookup"><span data-stu-id="836ea-462">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="836ea-463">Vlastnost `FileName` by měla být použita pouze pro účely zobrazení a pouze po kódování HTML.</span><span class="sxs-lookup"><span data-stu-id="836ea-463">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="836ea-464">Uvedené příklady nevezmou ohled na zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="836ea-464">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="836ea-465">Další informace jsou k dispozici v následujících částech a [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="836ea-465">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="836ea-466">Důležité informace o zabezpečení</span><span class="sxs-lookup"><span data-stu-id="836ea-466">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="836ea-467">Ověřování</span><span class="sxs-lookup"><span data-stu-id="836ea-467">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="836ea-468">Nahrávání velkých souborů pomocí streamování</span><span class="sxs-lookup"><span data-stu-id="836ea-468">Upload large files with streaming</span></span>

<span data-ttu-id="836ea-469">Následující příklad ukazuje, jak použít JavaScript ke streamování souboru do akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="836ea-469">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="836ea-470">Token proti padělání souboru se generuje pomocí vlastního atributu filtru a předává se do hlaviček protokolu HTTP klienta místo v textu žádosti.</span><span class="sxs-lookup"><span data-stu-id="836ea-470">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="836ea-471">Vzhledem k tomu, že metoda akce zpracovává nahraná data přímo, vazba modelu formuláře je zakázána jiným vlastním filtrem.</span><span class="sxs-lookup"><span data-stu-id="836ea-471">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="836ea-472">V rámci akce je obsah formuláře čten pomocí `MultipartReader`, který čte každou jednotlivou `MultipartSection`, zpracovává soubor nebo ukládá obsah podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="836ea-472">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="836ea-473">Po načtení oddílů s více částmi provede akce vlastní vazbu modelu.</span><span class="sxs-lookup"><span data-stu-id="836ea-473">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="836ea-474">Počáteční odpověď stránky načte formulář a uloží token proti padělání do souboru cookie (prostřednictvím atributu `GenerateAntiforgeryTokenCookieAttribute`).</span><span class="sxs-lookup"><span data-stu-id="836ea-474">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="836ea-475">Atribut používá ASP.NET Core integrovanou [podporu proti padělání](xref:security/anti-request-forgery) pro nastavení souboru cookie s tokenem žádosti:</span><span class="sxs-lookup"><span data-stu-id="836ea-475">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="836ea-476">@No__t-0 se používá k zakázání vazby modelu:</span><span class="sxs-lookup"><span data-stu-id="836ea-476">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="836ea-477">V ukázkové aplikaci `GenerateAntiforgeryTokenCookieAttribute` a `DisableFormValueModelBindingAttribute` se aplikují jako filtry pro modely stránky aplikace `/StreamedSingleFileUploadDb` a `/StreamedSingleFileUploadPhysical` v `Startup.ConfigureServices` pomocí [konvencí Razor Pages](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="836ea-477">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="836ea-478">Vzhledem k tomu, že vazba modelu nepřečte formulář, parametry, které jsou svázané z formuláře, se nezobrazují (budou pokračovat v práci s dotazem, trasou a hlavičkou).</span><span class="sxs-lookup"><span data-stu-id="836ea-478">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="836ea-479">Metoda Action funguje přímo s vlastností `Request`.</span><span class="sxs-lookup"><span data-stu-id="836ea-479">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="836ea-480">Pro čtení jednotlivých oddílů se používá `MultipartReader`.</span><span class="sxs-lookup"><span data-stu-id="836ea-480">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="836ea-481">Data klíč/hodnota jsou uložena v `KeyValueAccumulator`.</span><span class="sxs-lookup"><span data-stu-id="836ea-481">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="836ea-482">Po načtení oddílů s více částmi se obsah `KeyValueAccumulator` použije pro svázání dat formuláře s typem modelu.</span><span class="sxs-lookup"><span data-stu-id="836ea-482">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="836ea-483">Kompletní metoda `StreamingController.UploadDatabase` pro streamování do databáze s EF Core:</span><span class="sxs-lookup"><span data-stu-id="836ea-483">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="836ea-484">`MultipartRequestHelper` (*nástroje/MultipartRequestHelper. cs*):</span><span class="sxs-lookup"><span data-stu-id="836ea-484">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="836ea-485">Úplná metoda `StreamingController.UploadPhysical` pro streamování do fyzického umístění:</span><span class="sxs-lookup"><span data-stu-id="836ea-485">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="836ea-486">V ukázkové aplikaci jsou kontroly ověřování zpracovávány `FileHelpers.ProcessStreamedFile`.</span><span class="sxs-lookup"><span data-stu-id="836ea-486">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="836ea-487">Ověřování</span><span class="sxs-lookup"><span data-stu-id="836ea-487">Validation</span></span>

<span data-ttu-id="836ea-488">Třída `FileHelpers` ukázkové aplikace ukazuje několik kontrol uložených do vyrovnávací paměti <xref:Microsoft.AspNetCore.Http.IFormFile> a streamovaná nahrávání souborů.</span><span class="sxs-lookup"><span data-stu-id="836ea-488">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="836ea-489">Pro zpracování @no__t ukládání souborů s vyrovnávací pamětí v ukázkové aplikaci si přečtěte část `ProcessFormFile` v souboru *Utilities/Helper. cs* .</span><span class="sxs-lookup"><span data-stu-id="836ea-489">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="836ea-490">Pro zpracování streamované soubory si Projděte metodu `ProcessStreamedFile` ve stejném souboru.</span><span class="sxs-lookup"><span data-stu-id="836ea-490">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="836ea-491">Metody zpracování ověřování, které jsou znázorněné v ukázkové aplikaci, nekontrolují obsah nahraných souborů.</span><span class="sxs-lookup"><span data-stu-id="836ea-491">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="836ea-492">Ve většině produkčních scénářů se v souboru používá rozhraní API pro skenování virů nebo malwaru, než je soubor dostupný uživatelům nebo jiným systémům.</span><span class="sxs-lookup"><span data-stu-id="836ea-492">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="836ea-493">I když ukázka tématu poskytuje pracovní příklad technik ověřování, Neimplementujte třídu `FileHelpers` v produkční aplikaci, pokud:</span><span class="sxs-lookup"><span data-stu-id="836ea-493">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="836ea-494">Plně rozumíte implementaci.</span><span class="sxs-lookup"><span data-stu-id="836ea-494">Fully understand the implementation.</span></span>
> * <span data-ttu-id="836ea-495">Upravte implementaci podle potřeby pro prostředí a specifikace aplikace.</span><span class="sxs-lookup"><span data-stu-id="836ea-495">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="836ea-496">**Nikdy nepoužívejte nerozlišený kód zabezpečení v aplikaci bez nutnosti řešit tyto požadavky.**</span><span class="sxs-lookup"><span data-stu-id="836ea-496">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="836ea-497">Ověření obsahu</span><span class="sxs-lookup"><span data-stu-id="836ea-497">Content validation</span></span>

<span data-ttu-id="836ea-498">**Pro nahraný obsah použijte rozhraní API pro kontrolu virů a malwaru třetí strany.**</span><span class="sxs-lookup"><span data-stu-id="836ea-498">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="836ea-499">Prohledávání souborů je náročné na prostředky serveru ve scénářích s vysokým objemem.</span><span class="sxs-lookup"><span data-stu-id="836ea-499">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="836ea-500">Pokud dojde ke snížení výkonu zpracování požadavků z důvodu kontroly souborů, zvažte přesměrování práce skenování na službu na [pozadí](xref:fundamentals/host/hosted-services), případně služby spuštěné na serveru, který se liší od serveru aplikace.</span><span class="sxs-lookup"><span data-stu-id="836ea-500">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="836ea-501">Nahrané soubory se obvykle uchovávají v oblasti v karanténě, dokud je kontrola virů na pozadí nevrátí.</span><span class="sxs-lookup"><span data-stu-id="836ea-501">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="836ea-502">Když soubor projde, soubor se přesune do normálního umístění úložiště souborů.</span><span class="sxs-lookup"><span data-stu-id="836ea-502">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="836ea-503">Tyto kroky se obvykle provádí ve spojení s databázovým záznamem, který indikuje stav kontroly souboru.</span><span class="sxs-lookup"><span data-stu-id="836ea-503">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="836ea-504">Při použití takového přístupu zůstane aplikace a Server App zaměřené na reakci na požadavky.</span><span class="sxs-lookup"><span data-stu-id="836ea-504">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="836ea-505">Ověření přípony souboru</span><span class="sxs-lookup"><span data-stu-id="836ea-505">File extension validation</span></span>

<span data-ttu-id="836ea-506">Přípona nahraného souboru by měla být zaškrtnutá na seznamu povolených rozšíření.</span><span class="sxs-lookup"><span data-stu-id="836ea-506">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="836ea-507">Příklad:</span><span class="sxs-lookup"><span data-stu-id="836ea-507">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="836ea-508">Ověření podpisu souboru</span><span class="sxs-lookup"><span data-stu-id="836ea-508">File signature validation</span></span>

<span data-ttu-id="836ea-509">Podpis souboru se určuje na prvních několika bajtech na začátku souboru.</span><span class="sxs-lookup"><span data-stu-id="836ea-509">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="836ea-510">Tyto bajty lze použít k určení, zda přípona odpovídá obsahu souboru.</span><span class="sxs-lookup"><span data-stu-id="836ea-510">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="836ea-511">Ukázková aplikace zkontroluje podpisy souborů pro několik běžných typů souborů.</span><span class="sxs-lookup"><span data-stu-id="836ea-511">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="836ea-512">V následujícím příkladu se pro tento soubor kontroluje podpis souboru obrázku JPEG:</span><span class="sxs-lookup"><span data-stu-id="836ea-512">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="836ea-513">Další signatury souborů získáte v dokumentaci [signatury souborů](https://www.filesignatures.net/) a oficiálních souborů.</span><span class="sxs-lookup"><span data-stu-id="836ea-513">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="836ea-514">Zabezpečení názvu souboru</span><span class="sxs-lookup"><span data-stu-id="836ea-514">File name security</span></span>

<span data-ttu-id="836ea-515">Nikdy nepoužívejte název souboru dodaný klientem pro uložení souboru do fyzického úložiště.</span><span class="sxs-lookup"><span data-stu-id="836ea-515">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="836ea-516">Vytvořte bezpečný název souboru pro soubor pomocí [cesty. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) nebo [cesty. GetTempFileName](xref:System.IO.Path.GetTempFileName*) a vytvořte úplnou cestu (včetně názvu souboru) pro dočasné úložiště.</span><span class="sxs-lookup"><span data-stu-id="836ea-516">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="836ea-517">Razor automaticky kóduje hodnoty vlastností pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="836ea-517">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="836ea-518">Následující kód je bezpečné použít:</span><span class="sxs-lookup"><span data-stu-id="836ea-518">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="836ea-519">Mimo Razor, vždy <xref:System.Net.WebUtility.HtmlEncode*> obsah názvu souboru z požadavku uživatele.</span><span class="sxs-lookup"><span data-stu-id="836ea-519">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="836ea-520">Mnoho implementací musí zahrnovat kontrolu, že soubor existuje. v opačném případě je soubor přepsán souborem se stejným názvem.</span><span class="sxs-lookup"><span data-stu-id="836ea-520">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="836ea-521">Poskytněte další logiku pro splnění specifikací vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="836ea-521">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="836ea-522">Ověřování velikosti</span><span class="sxs-lookup"><span data-stu-id="836ea-522">Size validation</span></span>

<span data-ttu-id="836ea-523">Omezte velikost nahraných souborů.</span><span class="sxs-lookup"><span data-stu-id="836ea-523">Limit the size of uploaded files.</span></span>

<span data-ttu-id="836ea-524">V ukázkové aplikaci je velikost souboru omezená na 2 MB (uvedené v bajtech).</span><span class="sxs-lookup"><span data-stu-id="836ea-524">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="836ea-525">Limit je zadán prostřednictvím [Konfigurace](xref:fundamentals/configuration/index) ze souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="836ea-525">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="836ea-526">@No__t-0 je vložen do tříd `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="836ea-526">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="836ea-527">Když velikost souboru překročí limit, soubor se odmítne:</span><span class="sxs-lookup"><span data-stu-id="836ea-527">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="836ea-528">Porovnává hodnotu atributu name s parametrem název metody POST</span><span class="sxs-lookup"><span data-stu-id="836ea-528">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="836ea-529">V nestandardních formulářích, které odesílají data formuláře nebo přímo využívají @no__t JavaScriptu, se název zadaný v prvku formuláře nebo `FormData` musí shodovat s názvem parametru v akci kontroleru.</span><span class="sxs-lookup"><span data-stu-id="836ea-529">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="836ea-530">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="836ea-530">In the following example:</span></span>

* <span data-ttu-id="836ea-531">Při použití elementu `<input>` je atribut `name` nastaven na hodnotu `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="836ea-531">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="836ea-532">Při použití `FormData` v jazyce JavaScript je název nastaven na hodnotu `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="836ea-532">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="836ea-533">Pro parametr C# metody použijte stejný název (`battlePlans`):</span><span class="sxs-lookup"><span data-stu-id="836ea-533">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="836ea-534">Pro Razor Pages metodu obslužné rutiny stránky s názvem `Upload`:</span><span class="sxs-lookup"><span data-stu-id="836ea-534">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="836ea-535">Pro metodu akce po kontroléru MVC:</span><span class="sxs-lookup"><span data-stu-id="836ea-535">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="836ea-536">Konfigurace serveru a aplikace</span><span class="sxs-lookup"><span data-stu-id="836ea-536">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="836ea-537">Omezení délky těla částí</span><span class="sxs-lookup"><span data-stu-id="836ea-537">Multipart body length limit</span></span>

<span data-ttu-id="836ea-538"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> nastaví limit délky jednotlivých částí části.</span><span class="sxs-lookup"><span data-stu-id="836ea-538"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="836ea-539">Oddíly formuláře, které překračují toto omezení, vyvolávají při analýze <xref:System.IO.InvalidDataException>.</span><span class="sxs-lookup"><span data-stu-id="836ea-539">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="836ea-540">Výchozí hodnota je 134 217 728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="836ea-540">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="836ea-541">Upravte limit pomocí nastavení <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="836ea-541">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="836ea-542"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> slouží k nastavení <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> pro jednu stránku nebo akci.</span><span class="sxs-lookup"><span data-stu-id="836ea-542"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="836ea-543">V aplikaci Razor Pages použijte filtr s [konvencí](xref:razor-pages/razor-pages-conventions) v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="836ea-543">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="836ea-544">V aplikaci Razor Pages nebo aplikaci MVC použijte filtr na model stránky nebo metodu akce:</span><span class="sxs-lookup"><span data-stu-id="836ea-544">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="836ea-545">Kestrel maximální velikost textu požadavku</span><span class="sxs-lookup"><span data-stu-id="836ea-545">Kestrel maximum request body size</span></span>

<span data-ttu-id="836ea-546">Pro aplikace hostované v Kestrel je výchozí maximální velikost textu požadavku 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="836ea-546">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="836ea-547">Přizpůsobte limit pomocí možnosti serveru [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:</span><span class="sxs-lookup"><span data-stu-id="836ea-547">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="836ea-548"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> slouží k nastavení [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) pro jednu stránku nebo akci.</span><span class="sxs-lookup"><span data-stu-id="836ea-548"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="836ea-549">V aplikaci Razor Pages použijte filtr s [konvencí](xref:razor-pages/razor-pages-conventions) v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="836ea-549">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="836ea-550">V aplikaci se stránkami Razor nebo v aplikaci MVC použijte filtr na třídu obslužné rutiny stránky nebo na metodu akce:</span><span class="sxs-lookup"><span data-stu-id="836ea-550">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="836ea-551">Další omezení Kestrel</span><span class="sxs-lookup"><span data-stu-id="836ea-551">Other Kestrel limits</span></span>

<span data-ttu-id="836ea-552">Pro aplikace hostované v Kestrel se můžou vztahovat další omezení Kestrel:</span><span class="sxs-lookup"><span data-stu-id="836ea-552">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="836ea-553">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="836ea-553">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="836ea-554">Sazby za data požadavků a odpovědí</span><span class="sxs-lookup"><span data-stu-id="836ea-554">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="836ea-555">Omezení délky obsahu služby IIS</span><span class="sxs-lookup"><span data-stu-id="836ea-555">IIS content length limit</span></span>

<span data-ttu-id="836ea-556">Výchozí limit počtu požadavků (`maxAllowedContentLength`) je 30 000 000 bajtů, což je přibližně 28.6 MB.</span><span class="sxs-lookup"><span data-stu-id="836ea-556">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="836ea-557">Upravte limit v souboru *Web. config* :</span><span class="sxs-lookup"><span data-stu-id="836ea-557">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="836ea-558">Toto nastavení platí pouze pro službu IIS.</span><span class="sxs-lookup"><span data-stu-id="836ea-558">This setting only applies to IIS.</span></span> <span data-ttu-id="836ea-559">K tomuto chování nedochází ve výchozím nastavení při hostování v Kestrel.</span><span class="sxs-lookup"><span data-stu-id="836ea-559">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="836ea-560">Další informace najdete v tématu [omezení požadavků \<requestLimits >](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="836ea-560">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="836ea-561">Omezení v modulu ASP.NET Core nebo přítomnosti modulu filtrování požadavků služby IIS mohou omezit nahrávání na 2 nebo 4 GB.</span><span class="sxs-lookup"><span data-stu-id="836ea-561">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="836ea-562">Další informace najdete v tématu [nelze odeslat soubor o velikosti větší než 2 GB (ASPNET/AspNetCore #2711)](https://github.com/aspnet/AspNetCore/issues/2711).</span><span class="sxs-lookup"><span data-stu-id="836ea-562">For more information, see [Unable to upload file greater than 2GB in size (aspnet/AspNetCore #2711)](https://github.com/aspnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="836ea-563">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="836ea-563">Troubleshoot</span></span>

<span data-ttu-id="836ea-564">Níže jsou uvedeny některé běžné problémy, které se vyskytly při práci s nahráváním souborů a jejich možnými řešeními.</span><span class="sxs-lookup"><span data-stu-id="836ea-564">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="836ea-565">Při nasazení na server služby IIS se nenašla chyba.</span><span class="sxs-lookup"><span data-stu-id="836ea-565">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="836ea-566">Následující chyba znamená, že nahraný soubor překračuje délku nakonfigurovaného obsahu serveru:</span><span class="sxs-lookup"><span data-stu-id="836ea-566">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="836ea-567">Další informace o zvýšení limitu najdete v části [omezení délky obsahu služby IIS](#iis-content-length-limit) .</span><span class="sxs-lookup"><span data-stu-id="836ea-567">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="836ea-568">Chyba připojení</span><span class="sxs-lookup"><span data-stu-id="836ea-568">Connection failure</span></span>

<span data-ttu-id="836ea-569">Chyba připojení a připojení k serveru pro resetování pravděpodobně znamená, že nahraný soubor překračuje maximální velikost textu požadavku Kestrel.</span><span class="sxs-lookup"><span data-stu-id="836ea-569">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="836ea-570">Další informace najdete v části [Kestrel maximální velikost textu požadavku](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="836ea-570">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="836ea-571">Omezení připojení klientů Kestrel mohou také vyžadovat úpravu.</span><span class="sxs-lookup"><span data-stu-id="836ea-571">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="836ea-572">Výjimka odkazu s hodnotou null s IFormFile</span><span class="sxs-lookup"><span data-stu-id="836ea-572">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="836ea-573">Pokud kontroler přijímá odeslané soubory pomocí <xref:Microsoft.AspNetCore.Http.IFormFile>, ale hodnota je `null`, potvrďte, že formulář HTML určuje hodnotu `enctype` `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="836ea-573">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="836ea-574">Pokud tento atribut není nastaven u elementu `<form>`, odeslání souboru neproběhne a všechny vázané argumenty <xref:Microsoft.AspNetCore.Http.IFormFile> jsou `null`.</span><span class="sxs-lookup"><span data-stu-id="836ea-574">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="836ea-575">Ujistěte se také, že [nahrávání názvů v datech formuláře odpovídá pojmenování aplikace](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="836ea-575">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="836ea-576">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="836ea-576">Additional resources</span></span>

* [<span data-ttu-id="836ea-577">Neomezená nahrávání souboru</span><span class="sxs-lookup"><span data-stu-id="836ea-577">Unrestricted File Upload</span></span>](https://www.owasp.org/index.php/Unrestricted_File_Upload)
* [<span data-ttu-id="836ea-578">Zabezpečení Azure: rámec zabezpečení: ověření vstupu | Hrozeb</span><span class="sxs-lookup"><span data-stu-id="836ea-578">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="836ea-579">Vzory návrhu cloudu Azure: vzor osobního Key</span><span class="sxs-lookup"><span data-stu-id="836ea-579">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
