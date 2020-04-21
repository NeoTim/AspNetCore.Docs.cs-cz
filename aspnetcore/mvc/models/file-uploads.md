---
title: Nahrávání souborů v ASP.NET jádru
author: rick-anderson
description: Jak používat vazby modelu a streamování k nahrávání souborů v ASP.NET Core MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/18/2020
uid: mvc/models/file-uploads
ms.openlocfilehash: e25da0b3867181a16a4636768f36c148a152dd23
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661741"
---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="f507c-103">Nahrávání souborů v ASP.NET jádru</span><span class="sxs-lookup"><span data-stu-id="f507c-103">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="f507c-104">[Steve Smith](https://ardalis.com/) a [Rutger Storm](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="f507c-104">By [Steve Smith](https://ardalis.com/) and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f507c-105">ASP.NET Core podporuje nahrávání jednoho nebo více souborů pomocí vazby modelu ve vyrovnávací paměti pro menší soubory a streamování bez vyrovnávací paměti pro větší soubory.</span><span class="sxs-lookup"><span data-stu-id="f507c-105">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="f507c-106">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="f507c-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="f507c-107">Důležité informace o zabezpečení</span><span class="sxs-lookup"><span data-stu-id="f507c-107">Security considerations</span></span>

<span data-ttu-id="f507c-108">Buďte opatrní, pokud uživatelům poskytujete možnost nahrávat soubory na server.</span><span class="sxs-lookup"><span data-stu-id="f507c-108">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="f507c-109">Útočníci se mohou pokusit o:</span><span class="sxs-lookup"><span data-stu-id="f507c-109">Attackers may attempt to:</span></span>

* <span data-ttu-id="f507c-110">Spusťte útoky [odmítnutí služby.](/windows-hardware/drivers/ifs/denial-of-service)</span><span class="sxs-lookup"><span data-stu-id="f507c-110">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="f507c-111">Nahrajte viry nebo malware.</span><span class="sxs-lookup"><span data-stu-id="f507c-111">Upload viruses or malware.</span></span>
* <span data-ttu-id="f507c-112">Kompromitujte sítě a servery jinými způsoby.</span><span class="sxs-lookup"><span data-stu-id="f507c-112">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="f507c-113">Bezpečnostní kroky, které snižují pravděpodobnost úspěšného útoku, jsou:</span><span class="sxs-lookup"><span data-stu-id="f507c-113">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="f507c-114">Nahrávejte soubory do vyhrazené oblasti pro nahrávání souborů, nejlépe na nesystémovou jednotku.</span><span class="sxs-lookup"><span data-stu-id="f507c-114">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="f507c-115">Vyhrazené umístění usnadňuje ukládání bezpečnostních omezení pro nahrané soubory.</span><span class="sxs-lookup"><span data-stu-id="f507c-115">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="f507c-116">Zakažte oprávnění ke spuštění v umístění pro nahrávání souboru.&dagger;</span><span class="sxs-lookup"><span data-stu-id="f507c-116">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="f507c-117">**Nezachovávejte** nahrané soubory ve stejném adresářovém stromu jako aplikace.&dagger;</span><span class="sxs-lookup"><span data-stu-id="f507c-117">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="f507c-118">Použijte bezpečný název souboru určený aplikací.</span><span class="sxs-lookup"><span data-stu-id="f507c-118">Use a safe file name determined by the app.</span></span> <span data-ttu-id="f507c-119">Nepoužívejte název souboru poskytnutý uživatelem nebo nedůvěryhodný název souboru nahraného souboru. &dagger; HTML kódovat nedůvěryhodný název souboru při jeho zobrazení.</span><span class="sxs-lookup"><span data-stu-id="f507c-119">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="f507c-120">Například protokolování názvu souboru nebo zobrazení v uživatelském režimu (Razor automaticky kóduje výstup).</span><span class="sxs-lookup"><span data-stu-id="f507c-120">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="f507c-121">Povolit pouze schválené přípony souborů pro specifikaci návrhu aplikace.&dagger;</span><span class="sxs-lookup"><span data-stu-id="f507c-121">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="f507c-122">Ověřte, zda jsou na serveru prováděny kontroly na straně klienta. &dagger; Kontroly na straně klienta lze snadno obejít.</span><span class="sxs-lookup"><span data-stu-id="f507c-122">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="f507c-123">Zkontrolujte velikost nahraného souboru.</span><span class="sxs-lookup"><span data-stu-id="f507c-123">Check the size of an uploaded file.</span></span> <span data-ttu-id="f507c-124">Nastavte maximální limit velikosti, abyste zabránili velkým nahráváním.&dagger;</span><span class="sxs-lookup"><span data-stu-id="f507c-124">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="f507c-125">Pokud by soubory neměly být přepsány nahraným souborem se stejným názvem, zkontrolujte před nahráním souboru název souboru v databázi nebo fyzickém úložišti.</span><span class="sxs-lookup"><span data-stu-id="f507c-125">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="f507c-126">**Před uložením souboru spusťte nahraný obsah skener virů a malwaru.**</span><span class="sxs-lookup"><span data-stu-id="f507c-126">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="f507c-127">&dagger;Ukázková aplikace ukazuje přístup, který splňuje kritéria.</span><span class="sxs-lookup"><span data-stu-id="f507c-127">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="f507c-128">Nahrání škodlivého kódu do systému je často prvním krokem k provedení kódu, který může:</span><span class="sxs-lookup"><span data-stu-id="f507c-128">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="f507c-129">Zcela získat kontrolu nad systémem.</span><span class="sxs-lookup"><span data-stu-id="f507c-129">Completely gain control of a system.</span></span>
> * <span data-ttu-id="f507c-130">Přetížení systému s výsledkem, že systém havaruje.</span><span class="sxs-lookup"><span data-stu-id="f507c-130">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="f507c-131">Ohrožení uživatelských nebo systémových dat.</span><span class="sxs-lookup"><span data-stu-id="f507c-131">Compromise user or system data.</span></span>
> * <span data-ttu-id="f507c-132">Aplikujte graffiti na veřejné ui.</span><span class="sxs-lookup"><span data-stu-id="f507c-132">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="f507c-133">Informace o snížení oblasti útoku při přijímání souborů od uživatelů naleznete v následujících zdrojích:</span><span class="sxs-lookup"><span data-stu-id="f507c-133">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="f507c-134">Neomezené nahrávání souborů</span><span class="sxs-lookup"><span data-stu-id="f507c-134">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="f507c-135">Zabezpečení Azure: Ujistěte se, že jsou při přijímání souborů od uživatelů zavedeny příslušné ovládací prvky</span><span class="sxs-lookup"><span data-stu-id="f507c-135">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="f507c-136">Další informace o implementaci bezpečnostních opatření, včetně příkladů z ukázkové aplikace, najdete v části [Ověření.](#validation)</span><span class="sxs-lookup"><span data-stu-id="f507c-136">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="f507c-137">Scénáře úložiště</span><span class="sxs-lookup"><span data-stu-id="f507c-137">Storage scenarios</span></span>

<span data-ttu-id="f507c-138">Mezi běžné možnosti úložiště souborů patří:</span><span class="sxs-lookup"><span data-stu-id="f507c-138">Common storage options for files include:</span></span>

* <span data-ttu-id="f507c-139">databáze</span><span class="sxs-lookup"><span data-stu-id="f507c-139">Database</span></span>

  * <span data-ttu-id="f507c-140">U malých nahrávek souborů je databáze často rychlejší než možnosti fyzického úložiště (systém souborů nebo sdílená síť).</span><span class="sxs-lookup"><span data-stu-id="f507c-140">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="f507c-141">Databáze je často pohodlnější než možnosti fyzického úložiště, protože načítání záznamu databáze pro uživatelská data může současně poskytovat obsah souboru (například obrázek avatara).</span><span class="sxs-lookup"><span data-stu-id="f507c-141">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="f507c-142">Databáze je potenciálně levnější než použití služby úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="f507c-142">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="f507c-143">Fyzické úložiště (systém souborů nebo sdílená síťová složka)</span><span class="sxs-lookup"><span data-stu-id="f507c-143">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="f507c-144">Pro nahrávání velkých souborů:</span><span class="sxs-lookup"><span data-stu-id="f507c-144">For large file uploads:</span></span>
    * <span data-ttu-id="f507c-145">Omezení databáze může omezit velikost nahrávání.</span><span class="sxs-lookup"><span data-stu-id="f507c-145">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="f507c-146">Fyzické úložiště je často méně ekonomické než úložiště v databázi.</span><span class="sxs-lookup"><span data-stu-id="f507c-146">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="f507c-147">Fyzické úložiště je potenciálně levnější než použití služby úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="f507c-147">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="f507c-148">Proces aplikace musí mít oprávnění ke čtení a zápisu do umístění úložiště.</span><span class="sxs-lookup"><span data-stu-id="f507c-148">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="f507c-149">**Nikdy neudělte oprávnění k spuštění.**</span><span class="sxs-lookup"><span data-stu-id="f507c-149">**Never grant execute permission.**</span></span>

* <span data-ttu-id="f507c-150">Služba úložiště dat (například [Azure Blob Storage)](https://azure.microsoft.com/services/storage/blobs/)</span><span class="sxs-lookup"><span data-stu-id="f507c-150">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="f507c-151">Služby obvykle nabízejí lepší škálovatelnost a odolnost proti místním řešením, která obvykle podléhají jedinému bodu selhání.</span><span class="sxs-lookup"><span data-stu-id="f507c-151">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="f507c-152">Služby jsou potenciálně nižší náklady ve scénářích velké infrastruktury úložiště.</span><span class="sxs-lookup"><span data-stu-id="f507c-152">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="f507c-153">Další informace naleznete [v tématu Úvodní příručka: Vytvoření objektu blob v úložišti objektů pomocí rozhraní .NET](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="f507c-153">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="f507c-154">Scénáře nahrávání souborů</span><span class="sxs-lookup"><span data-stu-id="f507c-154">File upload scenarios</span></span>

<span data-ttu-id="f507c-155">Dva obecné přístupy pro nahrávání souborů jsou ukládání do vyrovnávací paměti a streamování.</span><span class="sxs-lookup"><span data-stu-id="f507c-155">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="f507c-156">**Vyrovnávací paměti**</span><span class="sxs-lookup"><span data-stu-id="f507c-156">**Buffering**</span></span>

<span data-ttu-id="f507c-157">Celý soubor je číst <xref:Microsoft.AspNetCore.Http.IFormFile>do , což je c# reprezentace souboru slouží ke zpracování nebo uložení souboru.</span><span class="sxs-lookup"><span data-stu-id="f507c-157">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="f507c-158">Prostředky (disk, paměť) používané nahráváním souborů závisí na počtu a velikosti souběžných nahrávek souborů.</span><span class="sxs-lookup"><span data-stu-id="f507c-158">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="f507c-159">Pokud se aplikace pokusí ukládat do vyrovnávací paměti příliš mnoho nahraných, dojde k chybě webu, když dojde místo v paměti nebo na disku.</span><span class="sxs-lookup"><span data-stu-id="f507c-159">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="f507c-160">Pokud velikost nebo četnost nahrávání souborů vyčerpává prostředky aplikací, použijte streamování.</span><span class="sxs-lookup"><span data-stu-id="f507c-160">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="f507c-161">Každý jeden soubor ve vyrovnávací paměti přesahující 64 kB je přesunut z paměti do dočasného souboru na disku.</span><span class="sxs-lookup"><span data-stu-id="f507c-161">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="f507c-162">Ukládání malých souborů do vyrovnávací paměti je popsáno v následujících částech tohoto tématu:</span><span class="sxs-lookup"><span data-stu-id="f507c-162">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="f507c-163">Fyzické skladování</span><span class="sxs-lookup"><span data-stu-id="f507c-163">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="f507c-164">databáze</span><span class="sxs-lookup"><span data-stu-id="f507c-164">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="f507c-165">**Streamování**</span><span class="sxs-lookup"><span data-stu-id="f507c-165">**Streaming**</span></span>

<span data-ttu-id="f507c-166">Soubor je přijat z vícedílného požadavku a přímo zpracován nebo uložen aplikací.</span><span class="sxs-lookup"><span data-stu-id="f507c-166">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="f507c-167">Streamování výrazně nezvyšuje výkon.</span><span class="sxs-lookup"><span data-stu-id="f507c-167">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="f507c-168">Streamování snižuje nároky na paměť nebo místo na disku při nahrávání souborů.</span><span class="sxs-lookup"><span data-stu-id="f507c-168">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="f507c-169">Streamování velkých souborů je pokryto v sekci [Nahrát velké soubory s datovými proudy.](#upload-large-files-with-streaming)</span><span class="sxs-lookup"><span data-stu-id="f507c-169">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="f507c-170">Nahrání malých souborů s vazbou modelu ve vyrovnávací paměti do fyzického úložiště</span><span class="sxs-lookup"><span data-stu-id="f507c-170">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="f507c-171">Chcete-li nahrát malé soubory, použijte vícedílný formulář nebo vytvořte požadavek POST pomocí JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="f507c-171">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="f507c-172">Následující příklad ukazuje použití formuláře Razor Pages k nahrání jednoho souboru *(Pages/BufferedSingleFileUploadPhysical.cshtml* v ukázkové aplikaci):</span><span class="sxs-lookup"><span data-stu-id="f507c-172">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="f507c-173">Následující příklad je obdobou předchozípříklad s tím rozdílem, že:</span><span class="sxs-lookup"><span data-stu-id="f507c-173">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="f507c-174">JavaScript[(Fetch API)](https://developer.mozilla.org/docs/Web/API/Fetch_API)se používá k odeslání dat formuláře.</span><span class="sxs-lookup"><span data-stu-id="f507c-174">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="f507c-175">Není tu žádné potvrzení.</span><span class="sxs-lookup"><span data-stu-id="f507c-175">There's no validation.</span></span>

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

<span data-ttu-id="f507c-176">Chcete-li provést formulář POST v jazyce JavaScript pro klienty, kteří [nepodporují načíst rozhraní API](https://caniuse.com/#feat=fetch), použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="f507c-176">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="f507c-177">Použijte Fetch Polyfill (například [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="f507c-177">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="f507c-178">Použijte `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="f507c-178">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="f507c-179">Příklad:</span><span class="sxs-lookup"><span data-stu-id="f507c-179">For example:</span></span>

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

<span data-ttu-id="f507c-180">Aby bylo možné podporovat nahrávání souborů, musí formuláře`enctype`HTML `multipart/form-data`určit typ kódování ( ) aplikace .</span><span class="sxs-lookup"><span data-stu-id="f507c-180">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="f507c-181">Pro `files` vstupní prvek pro podporu nahrávání `multiple` více souborů `<input>` poskytují atribut na prvek:</span><span class="sxs-lookup"><span data-stu-id="f507c-181">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="f507c-182">Jednotlivé soubory nahrané na server lze přistupovat prostřednictvím [model vazby](xref:mvc/models/model-binding) pomocí <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="f507c-182">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="f507c-183">Ukázková aplikace ukazuje více souborů ve vyrovnávací paměti pro scénáře databáze a fyzického úložiště.</span><span class="sxs-lookup"><span data-stu-id="f507c-183">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="f507c-184">**Nepoužívejte** `FileName` vlastnost jiné <xref:Microsoft.AspNetCore.Http.IFormFile> než pro zobrazení a protokolování.</span><span class="sxs-lookup"><span data-stu-id="f507c-184">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="f507c-185">Při zobrazení nebo protokolování html zakóduje název souboru.</span><span class="sxs-lookup"><span data-stu-id="f507c-185">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="f507c-186">Útočník může poskytnout škodlivý název souboru, včetně úplných cest nebo relativních cest.</span><span class="sxs-lookup"><span data-stu-id="f507c-186">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="f507c-187">Žádosti by měly:</span><span class="sxs-lookup"><span data-stu-id="f507c-187">Applications should:</span></span>
>
> * <span data-ttu-id="f507c-188">Odeberte cestu z uživatelem zadaného názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="f507c-188">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="f507c-189">Uložte název souboru kódovaný html, který je odstraněn z cesty, pro uživatelské ui nebo protokolování.</span><span class="sxs-lookup"><span data-stu-id="f507c-189">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="f507c-190">Vygenerujte nový náhodný název souboru pro úložiště.</span><span class="sxs-lookup"><span data-stu-id="f507c-190">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="f507c-191">Následující kód odebere cestu z názvu souboru:</span><span class="sxs-lookup"><span data-stu-id="f507c-191">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="f507c-192">Dosud poskytnuté příklady neberou v úvahu bezpečnostní aspekty.</span><span class="sxs-lookup"><span data-stu-id="f507c-192">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="f507c-193">Další informace jsou uvedeny v následujících částech a [v ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="f507c-193">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="f507c-194">Důležité informace o zabezpečení</span><span class="sxs-lookup"><span data-stu-id="f507c-194">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="f507c-195">Ověřování</span><span class="sxs-lookup"><span data-stu-id="f507c-195">Validation</span></span>](#validation)

<span data-ttu-id="f507c-196">Při nahrávání souborů pomocí <xref:Microsoft.AspNetCore.Http.IFormFile>vazby modelu a , může metoda akce přijmout:</span><span class="sxs-lookup"><span data-stu-id="f507c-196">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="f507c-197">Jeden <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="f507c-197">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="f507c-198">Všechny následující kolekce, které představují několik souborů:</span><span class="sxs-lookup"><span data-stu-id="f507c-198">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="f507c-199">[Seznamu](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="f507c-199">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="f507c-200">Vazba odpovídá souborům formuláře podle názvu.</span><span class="sxs-lookup"><span data-stu-id="f507c-200">Binding matches form files by name.</span></span> <span data-ttu-id="f507c-201">Například hodnota `name` HTML `<input type="file" name="formFile">` v aplikaci musí odpovídat`FormFile`vazbě parametru/vlastnosti jazyka C# ( ).</span><span class="sxs-lookup"><span data-stu-id="f507c-201">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="f507c-202">Další informace naleznete v [tématu Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span><span class="sxs-lookup"><span data-stu-id="f507c-202">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="f507c-203">Následující příklad:</span><span class="sxs-lookup"><span data-stu-id="f507c-203">The following example:</span></span>

* <span data-ttu-id="f507c-204">Smyčky přes jeden nebo více nahraných souborů.</span><span class="sxs-lookup"><span data-stu-id="f507c-204">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="f507c-205">Použije [path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) vrátit úplnou cestu pro soubor, včetně názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="f507c-205">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="f507c-206">Uloží soubory do místního systému souborů pomocí názvu souboru generovaného aplikací.</span><span class="sxs-lookup"><span data-stu-id="f507c-206">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="f507c-207">Vrátí celkový počet a velikost odeslaných souborů.</span><span class="sxs-lookup"><span data-stu-id="f507c-207">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="f507c-208">Slouží `Path.GetRandomFileName` ke generování názvu souboru bez cesty.</span><span class="sxs-lookup"><span data-stu-id="f507c-208">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="f507c-209">V následujícím příkladu je cesta získána z konfigurace:</span><span class="sxs-lookup"><span data-stu-id="f507c-209">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="f507c-210">Cesta předaná <xref:System.IO.FileStream> *musí* obsahovat název souboru.</span><span class="sxs-lookup"><span data-stu-id="f507c-210">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="f507c-211">Pokud není k dispozici název souboru, <xref:System.UnauthorizedAccessException> je vyvolána za běhu.</span><span class="sxs-lookup"><span data-stu-id="f507c-211">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="f507c-212">Soubory nahrané pomocí <xref:Microsoft.AspNetCore.Http.IFormFile> techniky jsou před zpracováním uloženy do vyrovnávací paměti nebo na disku na serveru.</span><span class="sxs-lookup"><span data-stu-id="f507c-212">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="f507c-213">Uvnitř metody akce <xref:Microsoft.AspNetCore.Http.IFormFile> je obsah přístupný <xref:System.IO.Stream>jako .</span><span class="sxs-lookup"><span data-stu-id="f507c-213">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="f507c-214">Kromě místního systému souborů lze soubory uložit do sdílené síťové složky nebo do služby úložiště souborů, jako je [například úložiště objektů blob Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="f507c-214">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="f507c-215">Další příklad, který prochází přes více souborů pro nahrávání a používá bezpečné názvy souborů, najdete *v tématu Stránky/BufferedMultipleFileUploadPhysical.cshtml.csv* ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f507c-215">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="f507c-216">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) vyvolá <xref:System.IO.IOException> pokud více než 65 535 soubory jsou vytvořeny bez odstranění předchozídočasné soubory.</span><span class="sxs-lookup"><span data-stu-id="f507c-216">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="f507c-217">Limit 65 535 souborů je limit pro server.</span><span class="sxs-lookup"><span data-stu-id="f507c-217">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="f507c-218">Další informace o tomto limitu pro operační systém Windows naleznete v poznámkách v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="f507c-218">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="f507c-219">Funkce GetTempFileNameA</span><span class="sxs-lookup"><span data-stu-id="f507c-219">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="f507c-220">Nahrání malých souborů s vazbou modelu ve vyrovnávací paměti do databáze</span><span class="sxs-lookup"><span data-stu-id="f507c-220">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="f507c-221">Chcete-li uložit binární data souborů do <xref:System.Byte> databáze pomocí entity [Framework](/ef/core/index), definujte vlastnost pole na entitě:</span><span class="sxs-lookup"><span data-stu-id="f507c-221">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="f507c-222">Zadejte vlastnost modelu stránky pro <xref:Microsoft.AspNetCore.Http.IFormFile>třídu, která obsahuje :</span><span class="sxs-lookup"><span data-stu-id="f507c-222">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="f507c-223"><xref:Microsoft.AspNetCore.Http.IFormFile>lze použít přímo jako parametr metody akce nebo jako vlastnost vázaného modelu.</span><span class="sxs-lookup"><span data-stu-id="f507c-223"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="f507c-224">Předchozí příklad používá vlastnost vázaného modelu.</span><span class="sxs-lookup"><span data-stu-id="f507c-224">The prior example uses a bound model property.</span></span>

<span data-ttu-id="f507c-225">Používá `FileUpload` se ve formuláři Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="f507c-225">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="f507c-226">Pokud je formulář posted na server, zkopírujte <xref:Microsoft.AspNetCore.Http.IFormFile> do datového proudu a uložte jej jako bajtové pole v databázi.</span><span class="sxs-lookup"><span data-stu-id="f507c-226">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="f507c-227">V následujícím příkladu `_dbContext` ukládá kontext databáze aplikace:</span><span class="sxs-lookup"><span data-stu-id="f507c-227">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="f507c-228">Předchozí příklad je podobný scénáři, který byl ukázán v ukázkové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="f507c-228">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="f507c-229">*Stránky/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f507c-229">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="f507c-230">*Stránky/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="f507c-230">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="f507c-231">Při ukládání binárních dat do relačních databází buďte opatrní, protože to může nepříznivě ovlivnit výkon.</span><span class="sxs-lookup"><span data-stu-id="f507c-231">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="f507c-232">Nespoléhejte na `FileName` vlastnost <xref:Microsoft.AspNetCore.Http.IFormFile> bez ověření ani ji nedůvěřujte.</span><span class="sxs-lookup"><span data-stu-id="f507c-232">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="f507c-233">Vlastnost `FileName` by měla být použita pouze pro účely zobrazení a pouze po kódování HTML.</span><span class="sxs-lookup"><span data-stu-id="f507c-233">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="f507c-234">Uvedené příklady neberou v úvahu aspekty zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="f507c-234">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="f507c-235">Další informace jsou uvedeny v následujících částech a [v ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="f507c-235">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="f507c-236">Důležité informace o zabezpečení</span><span class="sxs-lookup"><span data-stu-id="f507c-236">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="f507c-237">Ověřování</span><span class="sxs-lookup"><span data-stu-id="f507c-237">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="f507c-238">Nahrávání velkých souborů pomocí streamování</span><span class="sxs-lookup"><span data-stu-id="f507c-238">Upload large files with streaming</span></span>

<span data-ttu-id="f507c-239">Následující příklad ukazuje, jak používat JavaScript k streamování souboru do akce řadiče.</span><span class="sxs-lookup"><span data-stu-id="f507c-239">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="f507c-240">Token antiforgery souboru je generován pomocí vlastního atributu filtru a předán klientovi http záhlaví namísto v těle požadavku.</span><span class="sxs-lookup"><span data-stu-id="f507c-240">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="f507c-241">Vzhledem k tomu, že metoda akce zpracovává nahraná data přímo, vazba modelu formuláře je zakázána jiným vlastním filtrem.</span><span class="sxs-lookup"><span data-stu-id="f507c-241">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="f507c-242">V rámci akce je obsah formuláře přečten `MultipartReader`pomocí , `MultipartSection`který čte jednotlivé osoby , zpracovává soubor nebo ukládá obsah podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="f507c-242">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="f507c-243">Po čtení vícedílných oddílů akce provede vlastní vazbu modelu.</span><span class="sxs-lookup"><span data-stu-id="f507c-243">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="f507c-244">Počáteční odpověď na stránce načte formulář a uloží token antiforgery do souboru cookie (prostřednictvím atributu). `GenerateAntiforgeryTokenCookieAttribute`</span><span class="sxs-lookup"><span data-stu-id="f507c-244">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="f507c-245">Atribut používá ASP.NET integrované [antiforgery podpory](xref:security/anti-request-forgery) Core nastavit cookie s tokenem požadavku:</span><span class="sxs-lookup"><span data-stu-id="f507c-245">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="f507c-246">Slouží `DisableFormValueModelBindingAttribute` k zakázání vazby modelu:</span><span class="sxs-lookup"><span data-stu-id="f507c-246">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="f507c-247">V ukázkové `GenerateAntiforgeryTokenCookieAttribute` `DisableFormValueModelBindingAttribute` aplikaci a jsou použity jako `/StreamedSingleFileUploadDb` `/StreamedSingleFileUploadPhysical` filtry na modely aplikací stránky a v `Startup.ConfigureServices` použití [Razor Pages konvence](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="f507c-247">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=8-11,17-20)]

<span data-ttu-id="f507c-248">Vzhledem k tomu, že vazba modelu nečte formulář, parametry, které jsou vázány z formuláře, se nevážou (dotaz, trasa a záhlaví nadále fungují).</span><span class="sxs-lookup"><span data-stu-id="f507c-248">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="f507c-249">Metoda akce pracuje přímo `Request` s vlastností.</span><span class="sxs-lookup"><span data-stu-id="f507c-249">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="f507c-250">A `MultipartReader` se používá ke čtení jednotlivých oddílů.</span><span class="sxs-lookup"><span data-stu-id="f507c-250">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="f507c-251">Data klíč/hodnota jsou `KeyValueAccumulator`uložena v .</span><span class="sxs-lookup"><span data-stu-id="f507c-251">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="f507c-252">Po čtení vícedílných oddílů se `KeyValueAccumulator` obsah těchto oddílů použije k vytvoření sazby dat formuláře s typem modelu.</span><span class="sxs-lookup"><span data-stu-id="f507c-252">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="f507c-253">Kompletní `StreamingController.UploadDatabase` metoda pro streamování do databáze s EF Core:</span><span class="sxs-lookup"><span data-stu-id="f507c-253">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="f507c-254">`MultipartRequestHelper`(*Utility/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="f507c-254">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="f507c-255">Kompletní `StreamingController.UploadPhysical` metoda pro streamování do fyzického umístění:</span><span class="sxs-lookup"><span data-stu-id="f507c-255">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="f507c-256">Ve vzorové aplikaci jsou `FileHelpers.ProcessStreamedFile`kontroly ověření zpracovávány .</span><span class="sxs-lookup"><span data-stu-id="f507c-256">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="f507c-257">Ověřování</span><span class="sxs-lookup"><span data-stu-id="f507c-257">Validation</span></span>

<span data-ttu-id="f507c-258">Ukázková `FileHelpers` aplikace třídy ukazuje několik kontrol <xref:Microsoft.AspNetCore.Http.IFormFile> pro nahrávání souborů do vyrovnávací paměti a datových proudů.</span><span class="sxs-lookup"><span data-stu-id="f507c-258">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="f507c-259">Zpracování <xref:Microsoft.AspNetCore.Http.IFormFile> nahrávání souborů ve vyrovnávací paměti v `ProcessFormFile` ukázkové aplikaci naleznete v metodě v souboru *Utilities/FileHelpers.cs.*</span><span class="sxs-lookup"><span data-stu-id="f507c-259">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="f507c-260">Zpracování streamovaných souborů naleznete `ProcessStreamedFile` v metodě ve stejném souboru.</span><span class="sxs-lookup"><span data-stu-id="f507c-260">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="f507c-261">Metody zpracování ověření demonstrované v ukázkové aplikaci neprohledává obsah nahraných souborů.</span><span class="sxs-lookup"><span data-stu-id="f507c-261">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="f507c-262">Ve většině produkčních scénářů se v souboru používá rozhraní API pro skener virů a malwaru před zpřístupněním souboru uživatelům nebo jiným systémům.</span><span class="sxs-lookup"><span data-stu-id="f507c-262">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="f507c-263">Přestože ukázka tématu obsahuje funkční příklad technik `FileHelpers` ověření, neimplementujte třídu v produkční aplikaci, pokud:</span><span class="sxs-lookup"><span data-stu-id="f507c-263">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="f507c-264">Plně pochopit implementaci.</span><span class="sxs-lookup"><span data-stu-id="f507c-264">Fully understand the implementation.</span></span>
> * <span data-ttu-id="f507c-265">Upravte implementaci podle potřeby pro prostředí a specifikace aplikace.</span><span class="sxs-lookup"><span data-stu-id="f507c-265">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="f507c-266">**Nikdy bez rozdílu implementovat bezpečnostní kód v aplikaci bez řešení těchto požadavků.**</span><span class="sxs-lookup"><span data-stu-id="f507c-266">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="f507c-267">Ověření obsahu</span><span class="sxs-lookup"><span data-stu-id="f507c-267">Content validation</span></span>

<span data-ttu-id="f507c-268">**U nahraného obsahu použijte rozhraní API pro vyhledávání virů a malwaru třetí strany.**</span><span class="sxs-lookup"><span data-stu-id="f507c-268">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="f507c-269">Prohledávání souborů je náročné na serverové prostředky ve scénářích s velkým objemem.</span><span class="sxs-lookup"><span data-stu-id="f507c-269">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="f507c-270">Pokud je výkon zpracování požadavků snížen v důsledku prohledávání souborů, zvažte přečtení úlohy skenování na [službu na pozadí](xref:fundamentals/host/hosted-services), případně službu spuštěnou na serveru jiném než server aplikace.</span><span class="sxs-lookup"><span data-stu-id="f507c-270">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="f507c-271">Nahrané soubory jsou obvykle uchovávány v karanténě, dokud je nezkontroluje počítačový virus.</span><span class="sxs-lookup"><span data-stu-id="f507c-271">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="f507c-272">Když soubor projde, soubor je přesunut do normálního umístění úložiště souborů.</span><span class="sxs-lookup"><span data-stu-id="f507c-272">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="f507c-273">Tyto kroky jsou obvykle prováděny ve spojení se záznamem databáze, který označuje stav skenování souboru.</span><span class="sxs-lookup"><span data-stu-id="f507c-273">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="f507c-274">Pomocí takového přístupu se aplikace a aplikační server stále zaměřují na odpovědi na požadavky.</span><span class="sxs-lookup"><span data-stu-id="f507c-274">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="f507c-275">Ověření přípony souboru</span><span class="sxs-lookup"><span data-stu-id="f507c-275">File extension validation</span></span>

<span data-ttu-id="f507c-276">Přípona nahraného souboru by měla být zkontrolována podle seznamu povolených rozšíření.</span><span class="sxs-lookup"><span data-stu-id="f507c-276">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="f507c-277">Příklad:</span><span class="sxs-lookup"><span data-stu-id="f507c-277">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="f507c-278">Ověření podpisu souboru</span><span class="sxs-lookup"><span data-stu-id="f507c-278">File signature validation</span></span>

<span data-ttu-id="f507c-279">Podpis souboru je určen několika prvními bajty na začátku souboru.</span><span class="sxs-lookup"><span data-stu-id="f507c-279">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="f507c-280">Tyto bajty lze použít k označení, pokud přípona odpovídá obsahu souboru.</span><span class="sxs-lookup"><span data-stu-id="f507c-280">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="f507c-281">Ukázková aplikace kontroluje podpisy souborů pro několik běžných typů souborů.</span><span class="sxs-lookup"><span data-stu-id="f507c-281">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="f507c-282">V následujícím příkladu je podpis souboru pro obraz JPEG zkontrolován proti souboru:</span><span class="sxs-lookup"><span data-stu-id="f507c-282">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="f507c-283">Další podpisy souborů naleznete v [databázi podpisů souborů](https://www.filesignatures.net/) a oficiálníspecifikace souborů.</span><span class="sxs-lookup"><span data-stu-id="f507c-283">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="f507c-284">Zabezpečení názvu souboru</span><span class="sxs-lookup"><span data-stu-id="f507c-284">File name security</span></span>

<span data-ttu-id="f507c-285">Nikdy nepoužívejte název souboru dodaného klientem pro uložení souboru do fyzického úložiště.</span><span class="sxs-lookup"><span data-stu-id="f507c-285">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="f507c-286">Vytvořte bezpečný název souboru pomocí [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) nebo [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) k vytvoření úplné cesty (včetně názvu souboru) pro dočasné úložiště.</span><span class="sxs-lookup"><span data-stu-id="f507c-286">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="f507c-287">Holicí strojek automaticky kóduje hodnoty vlastností pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="f507c-287">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="f507c-288">Použití následujícího kódu je bezpečné:</span><span class="sxs-lookup"><span data-stu-id="f507c-288">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="f507c-289">Mimo Razor vždy <xref:System.Net.WebUtility.HtmlEncode*> soubor název obsahu z požadavku uživatele.</span><span class="sxs-lookup"><span data-stu-id="f507c-289">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="f507c-290">Mnoho implementací musí obsahovat kontrolu, zda soubor existuje; v opačném případě je soubor přepsán souborem se stejným názvem.</span><span class="sxs-lookup"><span data-stu-id="f507c-290">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="f507c-291">Zadejte další logiku, která vyhovuje specifikacím vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="f507c-291">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="f507c-292">Ověření velikosti</span><span class="sxs-lookup"><span data-stu-id="f507c-292">Size validation</span></span>

<span data-ttu-id="f507c-293">Omezte velikost nahraných souborů.</span><span class="sxs-lookup"><span data-stu-id="f507c-293">Limit the size of uploaded files.</span></span>

<span data-ttu-id="f507c-294">V ukázkové aplikaci je velikost souboru omezena na 2 MB (označeno v bajtech).</span><span class="sxs-lookup"><span data-stu-id="f507c-294">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="f507c-295">Limit je dodáván prostřednictvím [konfigurace](xref:fundamentals/configuration/index) ze souboru *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="f507c-295">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="f507c-296">Injektuje se `FileSizeLimit` do `PageModel` tříd:</span><span class="sxs-lookup"><span data-stu-id="f507c-296">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="f507c-297">Pokud velikost souboru překročí limit, soubor je odmítnut:</span><span class="sxs-lookup"><span data-stu-id="f507c-297">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="f507c-298">Shodovat hodnotu atributu názvu s názvem parametru metody POST</span><span class="sxs-lookup"><span data-stu-id="f507c-298">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="f507c-299">Ve formulářích bez břitvy, které POST `FormData` tvoří data nebo používají javascriptpřímo, název zadaný v elementu formuláře nebo `FormData` musí odpovídat názvu parametru v akci řadiče.</span><span class="sxs-lookup"><span data-stu-id="f507c-299">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="f507c-300">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="f507c-300">In the following example:</span></span>

* <span data-ttu-id="f507c-301">Při použití `<input>` prvku `name` je atribut nastaven `battlePlans`na hodnotu :</span><span class="sxs-lookup"><span data-stu-id="f507c-301">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="f507c-302">Při `FormData` použití v Jazyce JavaScript je `battlePlans`název nastaven na hodnotu :</span><span class="sxs-lookup"><span data-stu-id="f507c-302">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="f507c-303">Použijte odpovídající název pro parametr metody C# (`battlePlans`):</span><span class="sxs-lookup"><span data-stu-id="f507c-303">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="f507c-304">Pro metodu obslužné rutiny stránky Razor Pages s názvem `Upload`:</span><span class="sxs-lookup"><span data-stu-id="f507c-304">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="f507c-305">Pro metodu akce řadiče MVC POST:</span><span class="sxs-lookup"><span data-stu-id="f507c-305">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="f507c-306">Konfigurace serveru a aplikace</span><span class="sxs-lookup"><span data-stu-id="f507c-306">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="f507c-307">Limit délky vícedílné části těla</span><span class="sxs-lookup"><span data-stu-id="f507c-307">Multipart body length limit</span></span>

<span data-ttu-id="f507c-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>nastaví limit pro délku každého vícedílného těla.</span><span class="sxs-lookup"><span data-stu-id="f507c-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="f507c-309">Oddíly formuláře, které <xref:System.IO.InvalidDataException> překračují tento limit, při analýzě vyhovují.</span><span class="sxs-lookup"><span data-stu-id="f507c-309">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="f507c-310">Výchozí hodnota je 134 217 728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="f507c-310">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="f507c-311">Přizpůsobení limitu <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> pomocí `Startup.ConfigureServices`nastavení v :</span><span class="sxs-lookup"><span data-stu-id="f507c-311">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="f507c-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>slouží k nastavení <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> pro jednu stránku nebo akci.</span><span class="sxs-lookup"><span data-stu-id="f507c-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="f507c-313">V aplikaci Razor Pages použijte filtr `Startup.ConfigureServices`s [konvencí](xref:razor-pages/razor-pages-conventions) v aplikaci :</span><span class="sxs-lookup"><span data-stu-id="f507c-313">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="f507c-314">V aplikaci Razor Pages nebo MVC použijte filtr na model stránky nebo metodu akce:</span><span class="sxs-lookup"><span data-stu-id="f507c-314">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="f507c-315">Maximální velikost těla požadavku kestrelu</span><span class="sxs-lookup"><span data-stu-id="f507c-315">Kestrel maximum request body size</span></span>

<span data-ttu-id="f507c-316">Pro aplikace hostované kestrel, výchozí maximální velikost těla požadavku je 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="f507c-316">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="f507c-317">Přizpůsobit limit pomocí [maxrequestbodysize](xref:fundamentals/servers/kestrel#maximum-request-body-size) kestrel server možnost:</span><span class="sxs-lookup"><span data-stu-id="f507c-317">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="f507c-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>se používá k nastavení [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) pro jednu stránku nebo akci.</span><span class="sxs-lookup"><span data-stu-id="f507c-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="f507c-319">V aplikaci Razor Pages použijte filtr `Startup.ConfigureServices`s [konvencí](xref:razor-pages/razor-pages-conventions) v aplikaci :</span><span class="sxs-lookup"><span data-stu-id="f507c-319">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="f507c-320">V aplikaci Razor stránky nebo MVC použít filtr na třídu obslužné rutiny stránky nebo metody akce:</span><span class="sxs-lookup"><span data-stu-id="f507c-320">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="f507c-321">Lze `RequestSizeLimitAttribute` použít také pomocí [`@attribute`](xref:mvc/views/razor#attribute) směrnice Razor:</span><span class="sxs-lookup"><span data-stu-id="f507c-321">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="f507c-322">Ostatní limity kestrelu</span><span class="sxs-lookup"><span data-stu-id="f507c-322">Other Kestrel limits</span></span>

<span data-ttu-id="f507c-323">Pro aplikace hostované společností Kestrel se mohou vztahovat i na další limity kestrelu:</span><span class="sxs-lookup"><span data-stu-id="f507c-323">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="f507c-324">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="f507c-324">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="f507c-325">Rychlost dat žádostí a odpovědí</span><span class="sxs-lookup"><span data-stu-id="f507c-325">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="f507c-326">Omezení délky obsahu služby IIS</span><span class="sxs-lookup"><span data-stu-id="f507c-326">IIS content length limit</span></span>

<span data-ttu-id="f507c-327">Výchozí limit požadavku`maxAllowedContentLength`( ) je 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="f507c-327">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="f507c-328">Přizpůsobení limitu v souboru *web.config:*</span><span class="sxs-lookup"><span data-stu-id="f507c-328">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="f507c-329">Toto nastavení platí pouze pro službu IIS.</span><span class="sxs-lookup"><span data-stu-id="f507c-329">This setting only applies to IIS.</span></span> <span data-ttu-id="f507c-330">Chování nedochází ve výchozím nastavení při hostování na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f507c-330">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="f507c-331">Další informace naleznete v [tématu Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="f507c-331">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="f507c-332">Omezení v ASP.NET základního modulu nebo přítomnost modulu filtrování požadavků služby IIS mohou omezit nahrávání na 2 nebo 4 GB.</span><span class="sxs-lookup"><span data-stu-id="f507c-332">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="f507c-333">Další informace naleznete [v tématu Nelze nahrát soubor větší než 2 GB velikosti (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span><span class="sxs-lookup"><span data-stu-id="f507c-333">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="f507c-334">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="f507c-334">Troubleshoot</span></span>

<span data-ttu-id="f507c-335">Níže jsou uvedeny některé běžné problémy při práci s nahráváním souborů a jejich možná řešení.</span><span class="sxs-lookup"><span data-stu-id="f507c-335">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="f507c-336">Při nasazení na server služby IIS nebyla nalezena chyba</span><span class="sxs-lookup"><span data-stu-id="f507c-336">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="f507c-337">Následující chyba označuje, že nahraný soubor přesahuje nakonfigurovanou délku obsahu na serveru:</span><span class="sxs-lookup"><span data-stu-id="f507c-337">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="f507c-338">Další informace o zvýšení limitu naleznete v části [Omezení délky obsahu služby IIS.](#iis-content-length-limit)</span><span class="sxs-lookup"><span data-stu-id="f507c-338">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="f507c-339">Chyba připojení</span><span class="sxs-lookup"><span data-stu-id="f507c-339">Connection failure</span></span>

<span data-ttu-id="f507c-340">Chyba připojení a připojení k serveru resetování pravděpodobně znamená, že nahraný soubor překračuje maximální velikost těla požadavku Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f507c-340">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="f507c-341">Další informace naleznete v části [Maximální velikost těla požadavku Kestrel.](#kestrel-maximum-request-body-size)</span><span class="sxs-lookup"><span data-stu-id="f507c-341">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="f507c-342">Kestrel klienta omezení připojení může také vyžadovat úpravu.</span><span class="sxs-lookup"><span data-stu-id="f507c-342">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="f507c-343">Výjimka nulového odkazu se souborem IFormFile</span><span class="sxs-lookup"><span data-stu-id="f507c-343">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="f507c-344">Pokud řadič přijímá nahrané soubory <xref:Microsoft.AspNetCore.Http.IFormFile> pomocí, `null`ale hodnota je , zkontrolujte, zda formulář HTML určuje `enctype` hodnotu . `multipart/form-data`</span><span class="sxs-lookup"><span data-stu-id="f507c-344">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="f507c-345">Pokud tento atribut není nastaven `<form>` na prvek, nahrávání souboru nedojde <xref:Microsoft.AspNetCore.Http.IFormFile> a `null`všechny vázané argumenty jsou .</span><span class="sxs-lookup"><span data-stu-id="f507c-345">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="f507c-346">Také zkontrolujte, zda [se pojmenování při nahrávání ve formulářových datech shoduje s pojmenováním aplikace](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="f507c-346">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="f507c-347">Stream byl příliš dlouhý</span><span class="sxs-lookup"><span data-stu-id="f507c-347">Stream was too long</span></span>

<span data-ttu-id="f507c-348">Příklady v tomto tématu spoléhají na <xref:System.IO.MemoryStream> uložení obsahu nahraného souboru.</span><span class="sxs-lookup"><span data-stu-id="f507c-348">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="f507c-349">Limit velikosti `MemoryStream` a `int.MaxValue`je .</span><span class="sxs-lookup"><span data-stu-id="f507c-349">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="f507c-350">Pokud scénář nahrávání souborů aplikace vyžaduje držení obsahu souboru většího než 50 MB, `MemoryStream` použijte alternativní přístup, který nespoléhá na jeden pro držení obsahu nahraného souboru.</span><span class="sxs-lookup"><span data-stu-id="f507c-350">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f507c-351">ASP.NET Core podporuje nahrávání jednoho nebo více souborů pomocí vazby modelu ve vyrovnávací paměti pro menší soubory a streamování bez vyrovnávací paměti pro větší soubory.</span><span class="sxs-lookup"><span data-stu-id="f507c-351">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="f507c-352">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="f507c-352">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="f507c-353">Důležité informace o zabezpečení</span><span class="sxs-lookup"><span data-stu-id="f507c-353">Security considerations</span></span>

<span data-ttu-id="f507c-354">Buďte opatrní, pokud uživatelům poskytujete možnost nahrávat soubory na server.</span><span class="sxs-lookup"><span data-stu-id="f507c-354">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="f507c-355">Útočníci se mohou pokusit o:</span><span class="sxs-lookup"><span data-stu-id="f507c-355">Attackers may attempt to:</span></span>

* <span data-ttu-id="f507c-356">Spusťte útoky [odmítnutí služby.](/windows-hardware/drivers/ifs/denial-of-service)</span><span class="sxs-lookup"><span data-stu-id="f507c-356">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="f507c-357">Nahrajte viry nebo malware.</span><span class="sxs-lookup"><span data-stu-id="f507c-357">Upload viruses or malware.</span></span>
* <span data-ttu-id="f507c-358">Kompromitujte sítě a servery jinými způsoby.</span><span class="sxs-lookup"><span data-stu-id="f507c-358">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="f507c-359">Bezpečnostní kroky, které snižují pravděpodobnost úspěšného útoku, jsou:</span><span class="sxs-lookup"><span data-stu-id="f507c-359">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="f507c-360">Nahrávejte soubory do vyhrazené oblasti pro nahrávání souborů, nejlépe na nesystémovou jednotku.</span><span class="sxs-lookup"><span data-stu-id="f507c-360">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="f507c-361">Vyhrazené umístění usnadňuje ukládání bezpečnostních omezení pro nahrané soubory.</span><span class="sxs-lookup"><span data-stu-id="f507c-361">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="f507c-362">Zakažte oprávnění ke spuštění v umístění pro nahrávání souboru.&dagger;</span><span class="sxs-lookup"><span data-stu-id="f507c-362">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="f507c-363">**Nezachovávejte** nahrané soubory ve stejném adresářovém stromu jako aplikace.&dagger;</span><span class="sxs-lookup"><span data-stu-id="f507c-363">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="f507c-364">Použijte bezpečný název souboru určený aplikací.</span><span class="sxs-lookup"><span data-stu-id="f507c-364">Use a safe file name determined by the app.</span></span> <span data-ttu-id="f507c-365">Nepoužívejte název souboru poskytnutý uživatelem nebo nedůvěryhodný název souboru nahraného souboru. &dagger; HTML kódovat nedůvěryhodný název souboru při jeho zobrazení.</span><span class="sxs-lookup"><span data-stu-id="f507c-365">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="f507c-366">Například protokolování názvu souboru nebo zobrazení v uživatelském režimu (Razor automaticky kóduje výstup).</span><span class="sxs-lookup"><span data-stu-id="f507c-366">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="f507c-367">Povolit pouze schválené přípony souborů pro specifikaci návrhu aplikace.&dagger;</span><span class="sxs-lookup"><span data-stu-id="f507c-367">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="f507c-368">Ověřte, zda jsou na serveru prováděny kontroly na straně klienta. &dagger; Kontroly na straně klienta lze snadno obejít.</span><span class="sxs-lookup"><span data-stu-id="f507c-368">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="f507c-369">Zkontrolujte velikost nahraného souboru.</span><span class="sxs-lookup"><span data-stu-id="f507c-369">Check the size of an uploaded file.</span></span> <span data-ttu-id="f507c-370">Nastavte maximální limit velikosti, abyste zabránili velkým nahráváním.&dagger;</span><span class="sxs-lookup"><span data-stu-id="f507c-370">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="f507c-371">Pokud by soubory neměly být přepsány nahraným souborem se stejným názvem, zkontrolujte před nahráním souboru název souboru v databázi nebo fyzickém úložišti.</span><span class="sxs-lookup"><span data-stu-id="f507c-371">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="f507c-372">**Před uložením souboru spusťte nahraný obsah skener virů a malwaru.**</span><span class="sxs-lookup"><span data-stu-id="f507c-372">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="f507c-373">&dagger;Ukázková aplikace ukazuje přístup, který splňuje kritéria.</span><span class="sxs-lookup"><span data-stu-id="f507c-373">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="f507c-374">Nahrání škodlivého kódu do systému je často prvním krokem k provedení kódu, který může:</span><span class="sxs-lookup"><span data-stu-id="f507c-374">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="f507c-375">Zcela získat kontrolu nad systémem.</span><span class="sxs-lookup"><span data-stu-id="f507c-375">Completely gain control of a system.</span></span>
> * <span data-ttu-id="f507c-376">Přetížení systému s výsledkem, že systém havaruje.</span><span class="sxs-lookup"><span data-stu-id="f507c-376">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="f507c-377">Ohrožení uživatelských nebo systémových dat.</span><span class="sxs-lookup"><span data-stu-id="f507c-377">Compromise user or system data.</span></span>
> * <span data-ttu-id="f507c-378">Aplikujte graffiti na veřejné ui.</span><span class="sxs-lookup"><span data-stu-id="f507c-378">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="f507c-379">Informace o snížení oblasti útoku při přijímání souborů od uživatelů naleznete v následujících zdrojích:</span><span class="sxs-lookup"><span data-stu-id="f507c-379">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="f507c-380">Neomezené nahrávání souborů</span><span class="sxs-lookup"><span data-stu-id="f507c-380">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="f507c-381">Zabezpečení Azure: Ujistěte se, že jsou při přijímání souborů od uživatelů zavedeny příslušné ovládací prvky</span><span class="sxs-lookup"><span data-stu-id="f507c-381">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="f507c-382">Další informace o implementaci bezpečnostních opatření, včetně příkladů z ukázkové aplikace, najdete v části [Ověření.](#validation)</span><span class="sxs-lookup"><span data-stu-id="f507c-382">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="f507c-383">Scénáře úložiště</span><span class="sxs-lookup"><span data-stu-id="f507c-383">Storage scenarios</span></span>

<span data-ttu-id="f507c-384">Mezi běžné možnosti úložiště souborů patří:</span><span class="sxs-lookup"><span data-stu-id="f507c-384">Common storage options for files include:</span></span>

* <span data-ttu-id="f507c-385">databáze</span><span class="sxs-lookup"><span data-stu-id="f507c-385">Database</span></span>

  * <span data-ttu-id="f507c-386">U malých nahrávek souborů je databáze často rychlejší než možnosti fyzického úložiště (systém souborů nebo sdílená síť).</span><span class="sxs-lookup"><span data-stu-id="f507c-386">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="f507c-387">Databáze je často pohodlnější než možnosti fyzického úložiště, protože načítání záznamu databáze pro uživatelská data může současně poskytovat obsah souboru (například obrázek avatara).</span><span class="sxs-lookup"><span data-stu-id="f507c-387">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="f507c-388">Databáze je potenciálně levnější než použití služby úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="f507c-388">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="f507c-389">Fyzické úložiště (systém souborů nebo sdílená síťová složka)</span><span class="sxs-lookup"><span data-stu-id="f507c-389">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="f507c-390">Pro nahrávání velkých souborů:</span><span class="sxs-lookup"><span data-stu-id="f507c-390">For large file uploads:</span></span>
    * <span data-ttu-id="f507c-391">Omezení databáze může omezit velikost nahrávání.</span><span class="sxs-lookup"><span data-stu-id="f507c-391">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="f507c-392">Fyzické úložiště je často méně ekonomické než úložiště v databázi.</span><span class="sxs-lookup"><span data-stu-id="f507c-392">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="f507c-393">Fyzické úložiště je potenciálně levnější než použití služby úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="f507c-393">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="f507c-394">Proces aplikace musí mít oprávnění ke čtení a zápisu do umístění úložiště.</span><span class="sxs-lookup"><span data-stu-id="f507c-394">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="f507c-395">**Nikdy neudělte oprávnění k spuštění.**</span><span class="sxs-lookup"><span data-stu-id="f507c-395">**Never grant execute permission.**</span></span>

* <span data-ttu-id="f507c-396">Služba úložiště dat (například [Azure Blob Storage)](https://azure.microsoft.com/services/storage/blobs/)</span><span class="sxs-lookup"><span data-stu-id="f507c-396">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="f507c-397">Služby obvykle nabízejí lepší škálovatelnost a odolnost proti místním řešením, která obvykle podléhají jedinému bodu selhání.</span><span class="sxs-lookup"><span data-stu-id="f507c-397">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="f507c-398">Služby jsou potenciálně nižší náklady ve scénářích velké infrastruktury úložiště.</span><span class="sxs-lookup"><span data-stu-id="f507c-398">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="f507c-399">Další informace naleznete [v tématu Úvodní příručka: Vytvoření objektu blob v úložišti objektů pomocí rozhraní .NET](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="f507c-399">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="f507c-400">Toto téma <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>ukazuje <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> , ale lze <xref:System.IO.FileStream> uložit do úložiště objektů <xref:System.IO.Stream>blob při práci s .</span><span class="sxs-lookup"><span data-stu-id="f507c-400">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="f507c-401">Scénáře nahrávání souborů</span><span class="sxs-lookup"><span data-stu-id="f507c-401">File upload scenarios</span></span>

<span data-ttu-id="f507c-402">Dva obecné přístupy pro nahrávání souborů jsou ukládání do vyrovnávací paměti a streamování.</span><span class="sxs-lookup"><span data-stu-id="f507c-402">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="f507c-403">**Vyrovnávací paměti**</span><span class="sxs-lookup"><span data-stu-id="f507c-403">**Buffering**</span></span>

<span data-ttu-id="f507c-404">Celý soubor je číst <xref:Microsoft.AspNetCore.Http.IFormFile>do , což je c# reprezentace souboru slouží ke zpracování nebo uložení souboru.</span><span class="sxs-lookup"><span data-stu-id="f507c-404">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="f507c-405">Prostředky (disk, paměť) používané nahráváním souborů závisí na počtu a velikosti souběžných nahrávek souborů.</span><span class="sxs-lookup"><span data-stu-id="f507c-405">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="f507c-406">Pokud se aplikace pokusí ukládat do vyrovnávací paměti příliš mnoho nahraných, dojde k chybě webu, když dojde místo v paměti nebo na disku.</span><span class="sxs-lookup"><span data-stu-id="f507c-406">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="f507c-407">Pokud velikost nebo četnost nahrávání souborů vyčerpává prostředky aplikací, použijte streamování.</span><span class="sxs-lookup"><span data-stu-id="f507c-407">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="f507c-408">Každý jeden soubor ve vyrovnávací paměti přesahující 64 kB je přesunut z paměti do dočasného souboru na disku.</span><span class="sxs-lookup"><span data-stu-id="f507c-408">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="f507c-409">Ukládání malých souborů do vyrovnávací paměti je popsáno v následujících částech tohoto tématu:</span><span class="sxs-lookup"><span data-stu-id="f507c-409">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="f507c-410">Fyzické skladování</span><span class="sxs-lookup"><span data-stu-id="f507c-410">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="f507c-411">databáze</span><span class="sxs-lookup"><span data-stu-id="f507c-411">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="f507c-412">**Streamování**</span><span class="sxs-lookup"><span data-stu-id="f507c-412">**Streaming**</span></span>

<span data-ttu-id="f507c-413">Soubor je přijat z vícedílného požadavku a přímo zpracován nebo uložen aplikací.</span><span class="sxs-lookup"><span data-stu-id="f507c-413">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="f507c-414">Streamování výrazně nezvyšuje výkon.</span><span class="sxs-lookup"><span data-stu-id="f507c-414">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="f507c-415">Streamování snižuje nároky na paměť nebo místo na disku při nahrávání souborů.</span><span class="sxs-lookup"><span data-stu-id="f507c-415">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="f507c-416">Streamování velkých souborů je pokryto v sekci [Nahrát velké soubory s datovými proudy.](#upload-large-files-with-streaming)</span><span class="sxs-lookup"><span data-stu-id="f507c-416">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="f507c-417">Nahrání malých souborů s vazbou modelu ve vyrovnávací paměti do fyzického úložiště</span><span class="sxs-lookup"><span data-stu-id="f507c-417">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="f507c-418">Chcete-li nahrát malé soubory, použijte vícedílný formulář nebo vytvořte požadavek POST pomocí JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="f507c-418">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="f507c-419">Následující příklad ukazuje použití formuláře Razor Pages k nahrání jednoho souboru *(Pages/BufferedSingleFileUploadPhysical.cshtml* v ukázkové aplikaci):</span><span class="sxs-lookup"><span data-stu-id="f507c-419">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="f507c-420">Následující příklad je obdobou předchozípříklad s tím rozdílem, že:</span><span class="sxs-lookup"><span data-stu-id="f507c-420">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="f507c-421">JavaScript[(Fetch API)](https://developer.mozilla.org/docs/Web/API/Fetch_API)se používá k odeslání dat formuláře.</span><span class="sxs-lookup"><span data-stu-id="f507c-421">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="f507c-422">Není tu žádné potvrzení.</span><span class="sxs-lookup"><span data-stu-id="f507c-422">There's no validation.</span></span>

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

<span data-ttu-id="f507c-423">Chcete-li provést formulář POST v jazyce JavaScript pro klienty, kteří [nepodporují načíst rozhraní API](https://caniuse.com/#feat=fetch), použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="f507c-423">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="f507c-424">Použijte Fetch Polyfill (například [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="f507c-424">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="f507c-425">Použijte `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="f507c-425">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="f507c-426">Příklad:</span><span class="sxs-lookup"><span data-stu-id="f507c-426">For example:</span></span>

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

<span data-ttu-id="f507c-427">Aby bylo možné podporovat nahrávání souborů, musí formuláře`enctype`HTML `multipart/form-data`určit typ kódování ( ) aplikace .</span><span class="sxs-lookup"><span data-stu-id="f507c-427">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="f507c-428">Pro `files` vstupní prvek pro podporu nahrávání `multiple` více souborů `<input>` poskytují atribut na prvek:</span><span class="sxs-lookup"><span data-stu-id="f507c-428">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="f507c-429">Jednotlivé soubory nahrané na server lze přistupovat prostřednictvím [model vazby](xref:mvc/models/model-binding) pomocí <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="f507c-429">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="f507c-430">Ukázková aplikace ukazuje více souborů ve vyrovnávací paměti pro scénáře databáze a fyzického úložiště.</span><span class="sxs-lookup"><span data-stu-id="f507c-430">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="f507c-431">**Nepoužívejte** `FileName` vlastnost jiné <xref:Microsoft.AspNetCore.Http.IFormFile> než pro zobrazení a protokolování.</span><span class="sxs-lookup"><span data-stu-id="f507c-431">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="f507c-432">Při zobrazení nebo protokolování html zakóduje název souboru.</span><span class="sxs-lookup"><span data-stu-id="f507c-432">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="f507c-433">Útočník může poskytnout škodlivý název souboru, včetně úplných cest nebo relativních cest.</span><span class="sxs-lookup"><span data-stu-id="f507c-433">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="f507c-434">Žádosti by měly:</span><span class="sxs-lookup"><span data-stu-id="f507c-434">Applications should:</span></span>
>
> * <span data-ttu-id="f507c-435">Odeberte cestu z uživatelem zadaného názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="f507c-435">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="f507c-436">Uložte název souboru kódovaný html, který je odstraněn z cesty, pro uživatelské ui nebo protokolování.</span><span class="sxs-lookup"><span data-stu-id="f507c-436">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="f507c-437">Vygenerujte nový náhodný název souboru pro úložiště.</span><span class="sxs-lookup"><span data-stu-id="f507c-437">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="f507c-438">Následující kód odebere cestu z názvu souboru:</span><span class="sxs-lookup"><span data-stu-id="f507c-438">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="f507c-439">Dosud poskytnuté příklady neberou v úvahu bezpečnostní aspekty.</span><span class="sxs-lookup"><span data-stu-id="f507c-439">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="f507c-440">Další informace jsou uvedeny v následujících částech a [v ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="f507c-440">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="f507c-441">Důležité informace o zabezpečení</span><span class="sxs-lookup"><span data-stu-id="f507c-441">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="f507c-442">Ověřování</span><span class="sxs-lookup"><span data-stu-id="f507c-442">Validation</span></span>](#validation)

<span data-ttu-id="f507c-443">Při nahrávání souborů pomocí <xref:Microsoft.AspNetCore.Http.IFormFile>vazby modelu a , může metoda akce přijmout:</span><span class="sxs-lookup"><span data-stu-id="f507c-443">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="f507c-444">Jeden <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="f507c-444">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="f507c-445">Všechny následující kolekce, které představují několik souborů:</span><span class="sxs-lookup"><span data-stu-id="f507c-445">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="f507c-446">[Seznamu](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="f507c-446">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="f507c-447">Vazba odpovídá souborům formuláře podle názvu.</span><span class="sxs-lookup"><span data-stu-id="f507c-447">Binding matches form files by name.</span></span> <span data-ttu-id="f507c-448">Například hodnota `name` HTML `<input type="file" name="formFile">` v aplikaci musí odpovídat`FormFile`vazbě parametru/vlastnosti jazyka C# ( ).</span><span class="sxs-lookup"><span data-stu-id="f507c-448">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="f507c-449">Další informace naleznete v [tématu Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span><span class="sxs-lookup"><span data-stu-id="f507c-449">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="f507c-450">Následující příklad:</span><span class="sxs-lookup"><span data-stu-id="f507c-450">The following example:</span></span>

* <span data-ttu-id="f507c-451">Smyčky přes jeden nebo více nahraných souborů.</span><span class="sxs-lookup"><span data-stu-id="f507c-451">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="f507c-452">Použije [path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) vrátit úplnou cestu pro soubor, včetně názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="f507c-452">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="f507c-453">Uloží soubory do místního systému souborů pomocí názvu souboru generovaného aplikací.</span><span class="sxs-lookup"><span data-stu-id="f507c-453">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="f507c-454">Vrátí celkový počet a velikost odeslaných souborů.</span><span class="sxs-lookup"><span data-stu-id="f507c-454">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="f507c-455">Slouží `Path.GetRandomFileName` ke generování názvu souboru bez cesty.</span><span class="sxs-lookup"><span data-stu-id="f507c-455">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="f507c-456">V následujícím příkladu je cesta získána z konfigurace:</span><span class="sxs-lookup"><span data-stu-id="f507c-456">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="f507c-457">Cesta předaná <xref:System.IO.FileStream> *musí* obsahovat název souboru.</span><span class="sxs-lookup"><span data-stu-id="f507c-457">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="f507c-458">Pokud není k dispozici název souboru, <xref:System.UnauthorizedAccessException> je vyvolána za běhu.</span><span class="sxs-lookup"><span data-stu-id="f507c-458">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="f507c-459">Soubory nahrané pomocí <xref:Microsoft.AspNetCore.Http.IFormFile> techniky jsou před zpracováním uloženy do vyrovnávací paměti nebo na disku na serveru.</span><span class="sxs-lookup"><span data-stu-id="f507c-459">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="f507c-460">Uvnitř metody akce <xref:Microsoft.AspNetCore.Http.IFormFile> je obsah přístupný <xref:System.IO.Stream>jako .</span><span class="sxs-lookup"><span data-stu-id="f507c-460">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="f507c-461">Kromě místního systému souborů lze soubory uložit do sdílené síťové složky nebo do služby úložiště souborů, jako je [například úložiště objektů blob Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="f507c-461">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="f507c-462">Další příklad, který prochází přes více souborů pro nahrávání a používá bezpečné názvy souborů, najdete *v tématu Stránky/BufferedMultipleFileUploadPhysical.cshtml.csv* ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f507c-462">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="f507c-463">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) vyvolá <xref:System.IO.IOException> pokud více než 65 535 soubory jsou vytvořeny bez odstranění předchozídočasné soubory.</span><span class="sxs-lookup"><span data-stu-id="f507c-463">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="f507c-464">Limit 65 535 souborů je limit pro server.</span><span class="sxs-lookup"><span data-stu-id="f507c-464">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="f507c-465">Další informace o tomto limitu pro operační systém Windows naleznete v poznámkách v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="f507c-465">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="f507c-466">Funkce GetTempFileNameA</span><span class="sxs-lookup"><span data-stu-id="f507c-466">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="f507c-467">Nahrání malých souborů s vazbou modelu ve vyrovnávací paměti do databáze</span><span class="sxs-lookup"><span data-stu-id="f507c-467">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="f507c-468">Chcete-li uložit binární data souborů do <xref:System.Byte> databáze pomocí entity [Framework](/ef/core/index), definujte vlastnost pole na entitě:</span><span class="sxs-lookup"><span data-stu-id="f507c-468">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="f507c-469">Zadejte vlastnost modelu stránky pro <xref:Microsoft.AspNetCore.Http.IFormFile>třídu, která obsahuje :</span><span class="sxs-lookup"><span data-stu-id="f507c-469">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="f507c-470"><xref:Microsoft.AspNetCore.Http.IFormFile>lze použít přímo jako parametr metody akce nebo jako vlastnost vázaného modelu.</span><span class="sxs-lookup"><span data-stu-id="f507c-470"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="f507c-471">Předchozí příklad používá vlastnost vázaného modelu.</span><span class="sxs-lookup"><span data-stu-id="f507c-471">The prior example uses a bound model property.</span></span>

<span data-ttu-id="f507c-472">Používá `FileUpload` se ve formuláři Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="f507c-472">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="f507c-473">Pokud je formulář posted na server, zkopírujte <xref:Microsoft.AspNetCore.Http.IFormFile> do datového proudu a uložte jej jako bajtové pole v databázi.</span><span class="sxs-lookup"><span data-stu-id="f507c-473">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="f507c-474">V následujícím příkladu `_dbContext` ukládá kontext databáze aplikace:</span><span class="sxs-lookup"><span data-stu-id="f507c-474">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="f507c-475">Předchozí příklad je podobný scénáři, který byl ukázán v ukázkové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="f507c-475">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="f507c-476">*Stránky/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f507c-476">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="f507c-477">*Stránky/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="f507c-477">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="f507c-478">Při ukládání binárních dat do relačních databází buďte opatrní, protože to může nepříznivě ovlivnit výkon.</span><span class="sxs-lookup"><span data-stu-id="f507c-478">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="f507c-479">Nespoléhejte na `FileName` vlastnost <xref:Microsoft.AspNetCore.Http.IFormFile> bez ověření ani ji nedůvěřujte.</span><span class="sxs-lookup"><span data-stu-id="f507c-479">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="f507c-480">Vlastnost `FileName` by měla být použita pouze pro účely zobrazení a pouze po kódování HTML.</span><span class="sxs-lookup"><span data-stu-id="f507c-480">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="f507c-481">Uvedené příklady neberou v úvahu aspekty zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="f507c-481">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="f507c-482">Další informace jsou uvedeny v následujících částech a [v ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="f507c-482">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="f507c-483">Důležité informace o zabezpečení</span><span class="sxs-lookup"><span data-stu-id="f507c-483">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="f507c-484">Ověřování</span><span class="sxs-lookup"><span data-stu-id="f507c-484">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="f507c-485">Nahrávání velkých souborů pomocí streamování</span><span class="sxs-lookup"><span data-stu-id="f507c-485">Upload large files with streaming</span></span>

<span data-ttu-id="f507c-486">Následující příklad ukazuje, jak používat JavaScript k streamování souboru do akce řadiče.</span><span class="sxs-lookup"><span data-stu-id="f507c-486">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="f507c-487">Token antiforgery souboru je generován pomocí vlastního atributu filtru a předán klientovi http záhlaví namísto v těle požadavku.</span><span class="sxs-lookup"><span data-stu-id="f507c-487">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="f507c-488">Vzhledem k tomu, že metoda akce zpracovává nahraná data přímo, vazba modelu formuláře je zakázána jiným vlastním filtrem.</span><span class="sxs-lookup"><span data-stu-id="f507c-488">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="f507c-489">V rámci akce je obsah formuláře přečten `MultipartReader`pomocí , `MultipartSection`který čte jednotlivé osoby , zpracovává soubor nebo ukládá obsah podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="f507c-489">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="f507c-490">Po čtení vícedílných oddílů akce provede vlastní vazbu modelu.</span><span class="sxs-lookup"><span data-stu-id="f507c-490">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="f507c-491">Počáteční odpověď na stránce načte formulář a uloží token antiforgery do souboru cookie (prostřednictvím atributu). `GenerateAntiforgeryTokenCookieAttribute`</span><span class="sxs-lookup"><span data-stu-id="f507c-491">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="f507c-492">Atribut používá ASP.NET integrované [antiforgery podpory](xref:security/anti-request-forgery) Core nastavit cookie s tokenem požadavku:</span><span class="sxs-lookup"><span data-stu-id="f507c-492">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="f507c-493">Slouží `DisableFormValueModelBindingAttribute` k zakázání vazby modelu:</span><span class="sxs-lookup"><span data-stu-id="f507c-493">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="f507c-494">V ukázkové `GenerateAntiforgeryTokenCookieAttribute` `DisableFormValueModelBindingAttribute` aplikaci a jsou použity jako `/StreamedSingleFileUploadDb` `/StreamedSingleFileUploadPhysical` filtry na modely aplikací stránky a v `Startup.ConfigureServices` použití [Razor Pages konvence](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="f507c-494">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="f507c-495">Vzhledem k tomu, že vazba modelu nečte formulář, parametry, které jsou vázány z formuláře, se nevážou (dotaz, trasa a záhlaví nadále fungují).</span><span class="sxs-lookup"><span data-stu-id="f507c-495">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="f507c-496">Metoda akce pracuje přímo `Request` s vlastností.</span><span class="sxs-lookup"><span data-stu-id="f507c-496">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="f507c-497">A `MultipartReader` se používá ke čtení jednotlivých oddílů.</span><span class="sxs-lookup"><span data-stu-id="f507c-497">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="f507c-498">Data klíč/hodnota jsou `KeyValueAccumulator`uložena v .</span><span class="sxs-lookup"><span data-stu-id="f507c-498">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="f507c-499">Po čtení vícedílných oddílů se `KeyValueAccumulator` obsah těchto oddílů použije k vytvoření sazby dat formuláře s typem modelu.</span><span class="sxs-lookup"><span data-stu-id="f507c-499">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="f507c-500">Kompletní `StreamingController.UploadDatabase` metoda pro streamování do databáze s EF Core:</span><span class="sxs-lookup"><span data-stu-id="f507c-500">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="f507c-501">`MultipartRequestHelper`(*Utility/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="f507c-501">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="f507c-502">Kompletní `StreamingController.UploadPhysical` metoda pro streamování do fyzického umístění:</span><span class="sxs-lookup"><span data-stu-id="f507c-502">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="f507c-503">Ve vzorové aplikaci jsou `FileHelpers.ProcessStreamedFile`kontroly ověření zpracovávány .</span><span class="sxs-lookup"><span data-stu-id="f507c-503">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="f507c-504">Ověřování</span><span class="sxs-lookup"><span data-stu-id="f507c-504">Validation</span></span>

<span data-ttu-id="f507c-505">Ukázková `FileHelpers` aplikace třídy ukazuje několik kontrol <xref:Microsoft.AspNetCore.Http.IFormFile> pro nahrávání souborů do vyrovnávací paměti a datových proudů.</span><span class="sxs-lookup"><span data-stu-id="f507c-505">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="f507c-506">Zpracování <xref:Microsoft.AspNetCore.Http.IFormFile> nahrávání souborů ve vyrovnávací paměti v `ProcessFormFile` ukázkové aplikaci naleznete v metodě v souboru *Utilities/FileHelpers.cs.*</span><span class="sxs-lookup"><span data-stu-id="f507c-506">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="f507c-507">Zpracování streamovaných souborů naleznete `ProcessStreamedFile` v metodě ve stejném souboru.</span><span class="sxs-lookup"><span data-stu-id="f507c-507">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="f507c-508">Metody zpracování ověření demonstrované v ukázkové aplikaci neprohledává obsah nahraných souborů.</span><span class="sxs-lookup"><span data-stu-id="f507c-508">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="f507c-509">Ve většině produkčních scénářů se v souboru používá rozhraní API pro skener virů a malwaru před zpřístupněním souboru uživatelům nebo jiným systémům.</span><span class="sxs-lookup"><span data-stu-id="f507c-509">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="f507c-510">Přestože ukázka tématu obsahuje funkční příklad technik `FileHelpers` ověření, neimplementujte třídu v produkční aplikaci, pokud:</span><span class="sxs-lookup"><span data-stu-id="f507c-510">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="f507c-511">Plně pochopit implementaci.</span><span class="sxs-lookup"><span data-stu-id="f507c-511">Fully understand the implementation.</span></span>
> * <span data-ttu-id="f507c-512">Upravte implementaci podle potřeby pro prostředí a specifikace aplikace.</span><span class="sxs-lookup"><span data-stu-id="f507c-512">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="f507c-513">**Nikdy bez rozdílu implementovat bezpečnostní kód v aplikaci bez řešení těchto požadavků.**</span><span class="sxs-lookup"><span data-stu-id="f507c-513">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="f507c-514">Ověření obsahu</span><span class="sxs-lookup"><span data-stu-id="f507c-514">Content validation</span></span>

<span data-ttu-id="f507c-515">**U nahraného obsahu použijte rozhraní API pro vyhledávání virů a malwaru třetí strany.**</span><span class="sxs-lookup"><span data-stu-id="f507c-515">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="f507c-516">Prohledávání souborů je náročné na serverové prostředky ve scénářích s velkým objemem.</span><span class="sxs-lookup"><span data-stu-id="f507c-516">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="f507c-517">Pokud je výkon zpracování požadavků snížen v důsledku prohledávání souborů, zvažte přečtení úlohy skenování na [službu na pozadí](xref:fundamentals/host/hosted-services), případně službu spuštěnou na serveru jiném než server aplikace.</span><span class="sxs-lookup"><span data-stu-id="f507c-517">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="f507c-518">Nahrané soubory jsou obvykle uchovávány v karanténě, dokud je nezkontroluje počítačový virus.</span><span class="sxs-lookup"><span data-stu-id="f507c-518">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="f507c-519">Když soubor projde, soubor je přesunut do normálního umístění úložiště souborů.</span><span class="sxs-lookup"><span data-stu-id="f507c-519">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="f507c-520">Tyto kroky jsou obvykle prováděny ve spojení se záznamem databáze, který označuje stav skenování souboru.</span><span class="sxs-lookup"><span data-stu-id="f507c-520">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="f507c-521">Pomocí takového přístupu se aplikace a aplikační server stále zaměřují na odpovědi na požadavky.</span><span class="sxs-lookup"><span data-stu-id="f507c-521">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="f507c-522">Ověření přípony souboru</span><span class="sxs-lookup"><span data-stu-id="f507c-522">File extension validation</span></span>

<span data-ttu-id="f507c-523">Přípona nahraného souboru by měla být zkontrolována podle seznamu povolených rozšíření.</span><span class="sxs-lookup"><span data-stu-id="f507c-523">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="f507c-524">Příklad:</span><span class="sxs-lookup"><span data-stu-id="f507c-524">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="f507c-525">Ověření podpisu souboru</span><span class="sxs-lookup"><span data-stu-id="f507c-525">File signature validation</span></span>

<span data-ttu-id="f507c-526">Podpis souboru je určen několika prvními bajty na začátku souboru.</span><span class="sxs-lookup"><span data-stu-id="f507c-526">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="f507c-527">Tyto bajty lze použít k označení, pokud přípona odpovídá obsahu souboru.</span><span class="sxs-lookup"><span data-stu-id="f507c-527">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="f507c-528">Ukázková aplikace kontroluje podpisy souborů pro několik běžných typů souborů.</span><span class="sxs-lookup"><span data-stu-id="f507c-528">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="f507c-529">V následujícím příkladu je podpis souboru pro obraz JPEG zkontrolován proti souboru:</span><span class="sxs-lookup"><span data-stu-id="f507c-529">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="f507c-530">Další podpisy souborů naleznete v [databázi podpisů souborů](https://www.filesignatures.net/) a oficiálníspecifikace souborů.</span><span class="sxs-lookup"><span data-stu-id="f507c-530">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="f507c-531">Zabezpečení názvu souboru</span><span class="sxs-lookup"><span data-stu-id="f507c-531">File name security</span></span>

<span data-ttu-id="f507c-532">Nikdy nepoužívejte název souboru dodaného klientem pro uložení souboru do fyzického úložiště.</span><span class="sxs-lookup"><span data-stu-id="f507c-532">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="f507c-533">Vytvořte bezpečný název souboru pomocí [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) nebo [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) k vytvoření úplné cesty (včetně názvu souboru) pro dočasné úložiště.</span><span class="sxs-lookup"><span data-stu-id="f507c-533">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="f507c-534">Holicí strojek automaticky kóduje hodnoty vlastností pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="f507c-534">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="f507c-535">Použití následujícího kódu je bezpečné:</span><span class="sxs-lookup"><span data-stu-id="f507c-535">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="f507c-536">Mimo Razor vždy <xref:System.Net.WebUtility.HtmlEncode*> soubor název obsahu z požadavku uživatele.</span><span class="sxs-lookup"><span data-stu-id="f507c-536">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="f507c-537">Mnoho implementací musí obsahovat kontrolu, zda soubor existuje; v opačném případě je soubor přepsán souborem se stejným názvem.</span><span class="sxs-lookup"><span data-stu-id="f507c-537">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="f507c-538">Zadejte další logiku, která vyhovuje specifikacím vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="f507c-538">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="f507c-539">Ověření velikosti</span><span class="sxs-lookup"><span data-stu-id="f507c-539">Size validation</span></span>

<span data-ttu-id="f507c-540">Omezte velikost nahraných souborů.</span><span class="sxs-lookup"><span data-stu-id="f507c-540">Limit the size of uploaded files.</span></span>

<span data-ttu-id="f507c-541">V ukázkové aplikaci je velikost souboru omezena na 2 MB (označeno v bajtech).</span><span class="sxs-lookup"><span data-stu-id="f507c-541">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="f507c-542">Limit je dodáván prostřednictvím [konfigurace](xref:fundamentals/configuration/index) ze souboru *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="f507c-542">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="f507c-543">Injektuje se `FileSizeLimit` do `PageModel` tříd:</span><span class="sxs-lookup"><span data-stu-id="f507c-543">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="f507c-544">Pokud velikost souboru překročí limit, soubor je odmítnut:</span><span class="sxs-lookup"><span data-stu-id="f507c-544">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="f507c-545">Shodovat hodnotu atributu názvu s názvem parametru metody POST</span><span class="sxs-lookup"><span data-stu-id="f507c-545">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="f507c-546">Ve formulářích bez břitvy, které POST `FormData` tvoří data nebo používají javascriptpřímo, název zadaný v elementu formuláře nebo `FormData` musí odpovídat názvu parametru v akci řadiče.</span><span class="sxs-lookup"><span data-stu-id="f507c-546">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="f507c-547">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="f507c-547">In the following example:</span></span>

* <span data-ttu-id="f507c-548">Při použití `<input>` prvku `name` je atribut nastaven `battlePlans`na hodnotu :</span><span class="sxs-lookup"><span data-stu-id="f507c-548">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="f507c-549">Při `FormData` použití v Jazyce JavaScript je `battlePlans`název nastaven na hodnotu :</span><span class="sxs-lookup"><span data-stu-id="f507c-549">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="f507c-550">Použijte odpovídající název pro parametr metody C# (`battlePlans`):</span><span class="sxs-lookup"><span data-stu-id="f507c-550">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="f507c-551">Pro metodu obslužné rutiny stránky Razor Pages s názvem `Upload`:</span><span class="sxs-lookup"><span data-stu-id="f507c-551">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="f507c-552">Pro metodu akce řadiče MVC POST:</span><span class="sxs-lookup"><span data-stu-id="f507c-552">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="f507c-553">Konfigurace serveru a aplikace</span><span class="sxs-lookup"><span data-stu-id="f507c-553">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="f507c-554">Limit délky vícedílné části těla</span><span class="sxs-lookup"><span data-stu-id="f507c-554">Multipart body length limit</span></span>

<span data-ttu-id="f507c-555"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>nastaví limit pro délku každého vícedílného těla.</span><span class="sxs-lookup"><span data-stu-id="f507c-555"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="f507c-556">Oddíly formuláře, které <xref:System.IO.InvalidDataException> překračují tento limit, při analýzě vyhovují.</span><span class="sxs-lookup"><span data-stu-id="f507c-556">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="f507c-557">Výchozí hodnota je 134 217 728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="f507c-557">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="f507c-558">Přizpůsobení limitu <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> pomocí `Startup.ConfigureServices`nastavení v :</span><span class="sxs-lookup"><span data-stu-id="f507c-558">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="f507c-559"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>slouží k nastavení <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> pro jednu stránku nebo akci.</span><span class="sxs-lookup"><span data-stu-id="f507c-559"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="f507c-560">V aplikaci Razor Pages použijte filtr `Startup.ConfigureServices`s [konvencí](xref:razor-pages/razor-pages-conventions) v aplikaci :</span><span class="sxs-lookup"><span data-stu-id="f507c-560">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="f507c-561">V aplikaci Razor Pages nebo MVC použijte filtr na model stránky nebo metodu akce:</span><span class="sxs-lookup"><span data-stu-id="f507c-561">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="f507c-562">Maximální velikost těla požadavku kestrelu</span><span class="sxs-lookup"><span data-stu-id="f507c-562">Kestrel maximum request body size</span></span>

<span data-ttu-id="f507c-563">Pro aplikace hostované kestrel, výchozí maximální velikost těla požadavku je 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="f507c-563">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="f507c-564">Přizpůsobit limit pomocí [maxrequestbodysize](xref:fundamentals/servers/kestrel#maximum-request-body-size) kestrel server možnost:</span><span class="sxs-lookup"><span data-stu-id="f507c-564">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="f507c-565"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>se používá k nastavení [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) pro jednu stránku nebo akci.</span><span class="sxs-lookup"><span data-stu-id="f507c-565"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="f507c-566">V aplikaci Razor Pages použijte filtr `Startup.ConfigureServices`s [konvencí](xref:razor-pages/razor-pages-conventions) v aplikaci :</span><span class="sxs-lookup"><span data-stu-id="f507c-566">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="f507c-567">V aplikaci Razor stránky nebo MVC použít filtr na třídu obslužné rutiny stránky nebo metody akce:</span><span class="sxs-lookup"><span data-stu-id="f507c-567">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="f507c-568">Ostatní limity kestrelu</span><span class="sxs-lookup"><span data-stu-id="f507c-568">Other Kestrel limits</span></span>

<span data-ttu-id="f507c-569">Pro aplikace hostované společností Kestrel se mohou vztahovat i na další limity kestrelu:</span><span class="sxs-lookup"><span data-stu-id="f507c-569">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="f507c-570">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="f507c-570">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="f507c-571">Rychlost dat žádostí a odpovědí</span><span class="sxs-lookup"><span data-stu-id="f507c-571">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="f507c-572">Omezení délky obsahu služby IIS</span><span class="sxs-lookup"><span data-stu-id="f507c-572">IIS content length limit</span></span>

<span data-ttu-id="f507c-573">Výchozí limit požadavku`maxAllowedContentLength`( ) je 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="f507c-573">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="f507c-574">Přizpůsobení limitu v souboru *web.config:*</span><span class="sxs-lookup"><span data-stu-id="f507c-574">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="f507c-575">Toto nastavení platí pouze pro službu IIS.</span><span class="sxs-lookup"><span data-stu-id="f507c-575">This setting only applies to IIS.</span></span> <span data-ttu-id="f507c-576">Chování nedochází ve výchozím nastavení při hostování na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f507c-576">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="f507c-577">Další informace naleznete v [tématu Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="f507c-577">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="f507c-578">Omezení v ASP.NET základního modulu nebo přítomnost modulu filtrování požadavků služby IIS mohou omezit nahrávání na 2 nebo 4 GB.</span><span class="sxs-lookup"><span data-stu-id="f507c-578">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="f507c-579">Další informace naleznete [v tématu Nelze nahrát soubor větší než 2 GB velikosti (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span><span class="sxs-lookup"><span data-stu-id="f507c-579">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="f507c-580">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="f507c-580">Troubleshoot</span></span>

<span data-ttu-id="f507c-581">Níže jsou uvedeny některé běžné problémy při práci s nahráváním souborů a jejich možná řešení.</span><span class="sxs-lookup"><span data-stu-id="f507c-581">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="f507c-582">Při nasazení na server služby IIS nebyla nalezena chyba</span><span class="sxs-lookup"><span data-stu-id="f507c-582">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="f507c-583">Následující chyba označuje, že nahraný soubor přesahuje nakonfigurovanou délku obsahu na serveru:</span><span class="sxs-lookup"><span data-stu-id="f507c-583">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="f507c-584">Další informace o zvýšení limitu naleznete v části [Omezení délky obsahu služby IIS.](#iis-content-length-limit)</span><span class="sxs-lookup"><span data-stu-id="f507c-584">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="f507c-585">Chyba připojení</span><span class="sxs-lookup"><span data-stu-id="f507c-585">Connection failure</span></span>

<span data-ttu-id="f507c-586">Chyba připojení a připojení k serveru resetování pravděpodobně znamená, že nahraný soubor překračuje maximální velikost těla požadavku Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f507c-586">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="f507c-587">Další informace naleznete v části [Maximální velikost těla požadavku Kestrel.](#kestrel-maximum-request-body-size)</span><span class="sxs-lookup"><span data-stu-id="f507c-587">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="f507c-588">Kestrel klienta omezení připojení může také vyžadovat úpravu.</span><span class="sxs-lookup"><span data-stu-id="f507c-588">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="f507c-589">Výjimka nulového odkazu se souborem IFormFile</span><span class="sxs-lookup"><span data-stu-id="f507c-589">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="f507c-590">Pokud řadič přijímá nahrané soubory <xref:Microsoft.AspNetCore.Http.IFormFile> pomocí, `null`ale hodnota je , zkontrolujte, zda formulář HTML určuje `enctype` hodnotu . `multipart/form-data`</span><span class="sxs-lookup"><span data-stu-id="f507c-590">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="f507c-591">Pokud tento atribut není nastaven `<form>` na prvek, nahrávání souboru nedojde <xref:Microsoft.AspNetCore.Http.IFormFile> a `null`všechny vázané argumenty jsou .</span><span class="sxs-lookup"><span data-stu-id="f507c-591">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="f507c-592">Také zkontrolujte, zda [se pojmenování při nahrávání ve formulářových datech shoduje s pojmenováním aplikace](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="f507c-592">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="f507c-593">Stream byl příliš dlouhý</span><span class="sxs-lookup"><span data-stu-id="f507c-593">Stream was too long</span></span>

<span data-ttu-id="f507c-594">Příklady v tomto tématu spoléhají na <xref:System.IO.MemoryStream> uložení obsahu nahraného souboru.</span><span class="sxs-lookup"><span data-stu-id="f507c-594">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="f507c-595">Limit velikosti `MemoryStream` a `int.MaxValue`je .</span><span class="sxs-lookup"><span data-stu-id="f507c-595">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="f507c-596">Pokud scénář nahrávání souborů aplikace vyžaduje držení obsahu souboru většího než 50 MB, `MemoryStream` použijte alternativní přístup, který nespoléhá na jeden pro držení obsahu nahraného souboru.</span><span class="sxs-lookup"><span data-stu-id="f507c-596">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="f507c-597">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f507c-597">Additional resources</span></span>

* [<span data-ttu-id="f507c-598">Neomezené nahrávání souborů</span><span class="sxs-lookup"><span data-stu-id="f507c-598">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
* [<span data-ttu-id="f507c-599">Zabezpečení Azure: Rámec zabezpečení: Ověření vstupu | Skutečnosti snižující závažnost rizika</span><span class="sxs-lookup"><span data-stu-id="f507c-599">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="f507c-600">Vzory návrhu Azure Cloud: Vzor klíče zajištitel</span><span class="sxs-lookup"><span data-stu-id="f507c-600">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
