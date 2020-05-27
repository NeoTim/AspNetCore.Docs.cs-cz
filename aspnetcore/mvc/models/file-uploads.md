---
<span data-ttu-id="315ee-101">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="315ee-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="315ee-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="315ee-102">'Blazor'</span></span>
- <span data-ttu-id="315ee-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="315ee-103">'Identity'</span></span>
- <span data-ttu-id="315ee-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="315ee-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="315ee-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="315ee-105">'Razor'</span></span>
- <span data-ttu-id="315ee-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="315ee-106">'SignalR' uid:</span></span> 

---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="315ee-107">Nahrání souborů v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="315ee-107">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="315ee-108">[Steve Smith](https://ardalis.com/) a [Rutgerá](https://github.com/rutix) zaplavení</span><span class="sxs-lookup"><span data-stu-id="315ee-108">By [Steve Smith](https://ardalis.com/) and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="315ee-109">ASP.NET Core podporuje nahrávání jednoho nebo více souborů pomocí vazby modelu ve vyrovnávací paměti pro menší soubory a streamování bez vyrovnávací paměti pro větší soubory.</span><span class="sxs-lookup"><span data-stu-id="315ee-109">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="315ee-110">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="315ee-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="315ee-111">Aspekty zabezpečení</span><span class="sxs-lookup"><span data-stu-id="315ee-111">Security considerations</span></span>

<span data-ttu-id="315ee-112">Pokud chcete uživatelům poskytnout možnost nahrávat soubory na server, buďte opatrní.</span><span class="sxs-lookup"><span data-stu-id="315ee-112">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="315ee-113">Útočníci se můžou pokusit:</span><span class="sxs-lookup"><span data-stu-id="315ee-113">Attackers may attempt to:</span></span>

* <span data-ttu-id="315ee-114">Vykoná útok [DOS (Denial of Service](/windows-hardware/drivers/ifs/denial-of-service) ).</span><span class="sxs-lookup"><span data-stu-id="315ee-114">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="315ee-115">Nahrání virů nebo malwaru</span><span class="sxs-lookup"><span data-stu-id="315ee-115">Upload viruses or malware.</span></span>
* <span data-ttu-id="315ee-116">Narušit sítě a servery jinými způsoby.</span><span class="sxs-lookup"><span data-stu-id="315ee-116">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="315ee-117">Bezpečnostní kroky, které snižují pravděpodobnost úspěšného útoku, jsou:</span><span class="sxs-lookup"><span data-stu-id="315ee-117">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="315ee-118">Nahrajte soubory do vyhrazené oblasti pro nahrávání souborů, nejlépe do nesystémové jednotky.</span><span class="sxs-lookup"><span data-stu-id="315ee-118">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="315ee-119">Vyhrazené umístění usnadňuje omezení zabezpečení pro nahrané soubory.</span><span class="sxs-lookup"><span data-stu-id="315ee-119">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="315ee-120">Zakažte oprávnění EXECUTE pro umístění pro nahrání souboru.&dagger;</span><span class="sxs-lookup"><span data-stu-id="315ee-120">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="315ee-121">Neuchovávat nahrané soubory ve stejném stromu adresářů jako aplikace. **not**&dagger;</span><span class="sxs-lookup"><span data-stu-id="315ee-121">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="315ee-122">Použijte název bezpečného souboru určený aplikací.</span><span class="sxs-lookup"><span data-stu-id="315ee-122">Use a safe file name determined by the app.</span></span> <span data-ttu-id="315ee-123">Nepoužívejte název souboru poskytnutý uživatelem nebo nedůvěryhodného názvu nahraného souboru. &dagger; HTML při zobrazení kódování názvu nedůvěryhodného souboru.</span><span class="sxs-lookup"><span data-stu-id="315ee-123">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="315ee-124">Například protokolování názvu souboru nebo zobrazení v uživatelském rozhraní ( Razor Automatické kódování HTML kódování).</span><span class="sxs-lookup"><span data-stu-id="315ee-124">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="315ee-125">Povolte pro specifikaci návrhu aplikace jenom schválené přípony souborů.&dagger;</span><span class="sxs-lookup"><span data-stu-id="315ee-125">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="315ee-126">Ověřte, zda jsou na serveru provedeny kontroly na straně klienta. &dagger; Kontroly na straně klienta je snadné obejít.</span><span class="sxs-lookup"><span data-stu-id="315ee-126">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="315ee-127">Ověřte velikost nahraného souboru.</span><span class="sxs-lookup"><span data-stu-id="315ee-127">Check the size of an uploaded file.</span></span> <span data-ttu-id="315ee-128">Nastavte limit maximální velikosti, aby se zabránilo velkým nahrávání.&dagger;</span><span class="sxs-lookup"><span data-stu-id="315ee-128">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="315ee-129">Pokud by soubory neměly být přepsány nahraným souborem se stejným názvem, před nahráním souboru ověřte název souboru proti databázi nebo fyzickému úložišti.</span><span class="sxs-lookup"><span data-stu-id="315ee-129">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="315ee-130">**Před uložením souboru spusťte v nahraném obsahu skener virů nebo malwaru.**</span><span class="sxs-lookup"><span data-stu-id="315ee-130">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="315ee-131">&dagger;Ukázková aplikace předvádí přístup, který splňuje kritéria.</span><span class="sxs-lookup"><span data-stu-id="315ee-131">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="315ee-132">Nahrávání škodlivého kódu do systému je často prvním krokem ke spuštění kódu, který může:</span><span class="sxs-lookup"><span data-stu-id="315ee-132">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="315ee-133">Zcela získá kontrolu nad systémem.</span><span class="sxs-lookup"><span data-stu-id="315ee-133">Completely gain control of a system.</span></span>
> * <span data-ttu-id="315ee-134">Přetížit systém s výsledkem, že dojde k chybě systému.</span><span class="sxs-lookup"><span data-stu-id="315ee-134">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="315ee-135">Napadnout data uživatelů nebo systémových dat.</span><span class="sxs-lookup"><span data-stu-id="315ee-135">Compromise user or system data.</span></span>
> * <span data-ttu-id="315ee-136">Použijte graffiti pro veřejné uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="315ee-136">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="315ee-137">Informace o omezení oblasti útoku při přijímání souborů uživateli najdete v následujících zdrojích informací:</span><span class="sxs-lookup"><span data-stu-id="315ee-137">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="315ee-138">Neomezená nahrávání souboru</span><span class="sxs-lookup"><span data-stu-id="315ee-138">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="315ee-139">Zabezpečení Azure: Ujistěte se, že jsou při přijímání souborů od uživatelů k dismístě správné ovládací prvky.</span><span class="sxs-lookup"><span data-stu-id="315ee-139">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="315ee-140">Další informace o implementaci bezpečnostních opatření, včetně příkladů z ukázkové aplikace, najdete v části [ověření](#validation) .</span><span class="sxs-lookup"><span data-stu-id="315ee-140">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="315ee-141">Scénáře úložiště</span><span class="sxs-lookup"><span data-stu-id="315ee-141">Storage scenarios</span></span>

<span data-ttu-id="315ee-142">Mezi běžné možnosti úložiště pro soubory patří:</span><span class="sxs-lookup"><span data-stu-id="315ee-142">Common storage options for files include:</span></span>

* <span data-ttu-id="315ee-143">databáze</span><span class="sxs-lookup"><span data-stu-id="315ee-143">Database</span></span>

  * <span data-ttu-id="315ee-144">U malých nahrávání souborů je databáze často rychlejší než možnosti fyzického úložiště (systému souborů nebo síťového sdílení).</span><span class="sxs-lookup"><span data-stu-id="315ee-144">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="315ee-145">Databáze je často pohodlnější než možnosti fyzického úložiště, protože načtení záznamu databáze pro uživatelská data může současně poskytovat obsah souboru (například obrázek miniatury).</span><span class="sxs-lookup"><span data-stu-id="315ee-145">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="315ee-146">Databáze je potenciálně levnější než použití služby úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="315ee-146">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="315ee-147">Fyzické úložiště (systém souborů nebo síťová sdílená složka)</span><span class="sxs-lookup"><span data-stu-id="315ee-147">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="315ee-148">Pro nahrávání velkých souborů:</span><span class="sxs-lookup"><span data-stu-id="315ee-148">For large file uploads:</span></span>
    * <span data-ttu-id="315ee-149">Omezení databáze mohou omezit velikost nahrávání.</span><span class="sxs-lookup"><span data-stu-id="315ee-149">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="315ee-150">Fyzické úložiště je často méně hospodárné než úložiště v databázi.</span><span class="sxs-lookup"><span data-stu-id="315ee-150">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="315ee-151">Fyzické úložiště je potenciálně levnější než použití služby úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="315ee-151">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="315ee-152">Proces aplikace musí mít oprávnění ke čtení a zápisu do umístění úložiště.</span><span class="sxs-lookup"><span data-stu-id="315ee-152">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="315ee-153">**Nikdy neudělujte oprávnění EXECUTE.**</span><span class="sxs-lookup"><span data-stu-id="315ee-153">**Never grant execute permission.**</span></span>

* <span data-ttu-id="315ee-154">Služba úložiště dat (například [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="315ee-154">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="315ee-155">Služby obvykle nabízejí vylepšenou škálovatelnost a odolnost proti místním řešením, které obvykle podléhají jednomu bodu selhání.</span><span class="sxs-lookup"><span data-stu-id="315ee-155">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="315ee-156">Služby jsou potenciálně nižší náklady ve scénářích infrastruktury velkých úložišť.</span><span class="sxs-lookup"><span data-stu-id="315ee-156">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="315ee-157">Další informace najdete v tématu [rychlý Start: použití .NET k vytvoření objektu BLOB v úložišti objektů](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="315ee-157">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="315ee-158">Scénáře nahrávání souborů</span><span class="sxs-lookup"><span data-stu-id="315ee-158">File upload scenarios</span></span>

<span data-ttu-id="315ee-159">Dva obecné přístupy k nahrávání souborů jsou ukládání do vyrovnávací paměti a streamování.</span><span class="sxs-lookup"><span data-stu-id="315ee-159">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="315ee-160">**Do vyrovnávací paměti**</span><span class="sxs-lookup"><span data-stu-id="315ee-160">**Buffering**</span></span>

<span data-ttu-id="315ee-161">Celý soubor je načten do <xref:Microsoft.AspNetCore.Http.IFormFile> , což je reprezentace souboru, který se používá ke zpracování nebo uložení souboru v jazyce C#.</span><span class="sxs-lookup"><span data-stu-id="315ee-161">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="315ee-162">Prostředky (disk, paměť) používané při nahrávání souborů závisí na počtu a velikosti souběžných nahrávání souborů.</span><span class="sxs-lookup"><span data-stu-id="315ee-162">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="315ee-163">Pokud se aplikace pokusí do vyrovnávací paměti příliš mnoho nahrávání, dojde k selhání lokality, když dojde k vynechání paměti nebo místa na disku.</span><span class="sxs-lookup"><span data-stu-id="315ee-163">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="315ee-164">Pokud velikost nebo frekvence nahrávání souborů vyčerpá prostředky aplikace, použijte streamování.</span><span class="sxs-lookup"><span data-stu-id="315ee-164">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="315ee-165">Z paměti do dočasného souboru na disku se přesune libovolný soubor s vyrovnávací pamětí větší než 64 KB.</span><span class="sxs-lookup"><span data-stu-id="315ee-165">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="315ee-166">Ukládání malých souborů do vyrovnávací paměti je popsáno v následujících částech tohoto tématu:</span><span class="sxs-lookup"><span data-stu-id="315ee-166">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="315ee-167">Fyzické úložiště</span><span class="sxs-lookup"><span data-stu-id="315ee-167">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="315ee-168">Databáze</span><span class="sxs-lookup"><span data-stu-id="315ee-168">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="315ee-169">**Streamování**</span><span class="sxs-lookup"><span data-stu-id="315ee-169">**Streaming**</span></span>

<span data-ttu-id="315ee-170">Soubor se přijímá z požadavku na více částí a přímo se zpracovává nebo ukládá v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="315ee-170">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="315ee-171">Streamování nijak významně nezvyšuje výkon.</span><span class="sxs-lookup"><span data-stu-id="315ee-171">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="315ee-172">Streamování snižuje nároky na paměť nebo místo na disku při nahrávání souborů.</span><span class="sxs-lookup"><span data-stu-id="315ee-172">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="315ee-173">Streamování velkých souborů je zahrnuté v části [nahrávání velkých souborů pomocí streamování](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="315ee-173">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="315ee-174">Nahrávání malých souborů s vazbou modelu ve vyrovnávací paměti na fyzické úložiště</span><span class="sxs-lookup"><span data-stu-id="315ee-174">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="315ee-175">Pro nahrání malých souborů použijte formulář s více částmi nebo sestavte požadavek POST pomocí JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="315ee-175">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="315ee-176">Následující příklad ukazuje použití Razor formuláře stránky k nahrání jednoho souboru (*Pages/BufferedSingleFileUploadPhysical. cshtml* do ukázkové aplikace):</span><span class="sxs-lookup"><span data-stu-id="315ee-176">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="315ee-177">Následující příklad je podobný předchozímu příkladu s tím rozdílem, že:</span><span class="sxs-lookup"><span data-stu-id="315ee-177">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="315ee-178">K odeslání dat formuláře se používá JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)).</span><span class="sxs-lookup"><span data-stu-id="315ee-178">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="315ee-179">Neexistuje žádné ověření.</span><span class="sxs-lookup"><span data-stu-id="315ee-179">There's no validation.</span></span>

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

<span data-ttu-id="315ee-180">Chcete-li provést příspěvek formuláře v jazyce JavaScript pro klienty, kteří [nepodporují rozhraní API pro načítání](https://caniuse.com/#feat=fetch), použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="315ee-180">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="315ee-181">Použijte načtenou výplň (například [window. Fetch Fill (GitHub/Fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="315ee-181">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="315ee-182">Použijte `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="315ee-182">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="315ee-183">Příklad:</span><span class="sxs-lookup"><span data-stu-id="315ee-183">For example:</span></span>

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

<span data-ttu-id="315ee-184">Aby bylo možné podporovat nahrávání souborů, musí formuláře HTML určovat typ kódování ( `enctype` ) `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="315ee-184">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="315ee-185">Pro `files` vstupní element, který podporuje nahrávání více souborů, poskytněte `multiple` atribut `<input>` elementu:</span><span class="sxs-lookup"><span data-stu-id="315ee-185">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="315ee-186">Jednotlivé soubory nahrané na server jsou k dispozici prostřednictvím [vazby modelu](xref:mvc/models/model-binding) pomocí <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="315ee-186">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="315ee-187">Ukázková aplikace ukazuje více ukládání souborů do vyrovnávací paměti pro scénáře databáze a fyzických úložišť.</span><span class="sxs-lookup"><span data-stu-id="315ee-187">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="315ee-188">Nepoužívejte **not** `FileName` vlastnost <xref:Microsoft.AspNetCore.Http.IFormFile> jinou než pro zobrazení a protokolování.</span><span class="sxs-lookup"><span data-stu-id="315ee-188">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="315ee-189">Při zobrazení nebo protokolování je název souboru kódován HTML.</span><span class="sxs-lookup"><span data-stu-id="315ee-189">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="315ee-190">Útočník může poskytnout škodlivý název souboru, včetně úplných cest nebo relativních cest.</span><span class="sxs-lookup"><span data-stu-id="315ee-190">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="315ee-191">Aplikace by měly:</span><span class="sxs-lookup"><span data-stu-id="315ee-191">Applications should:</span></span>
>
> * <span data-ttu-id="315ee-192">Odeberte cestu z názvu souboru zadaného uživatelem.</span><span class="sxs-lookup"><span data-stu-id="315ee-192">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="315ee-193">Uložte název souboru s příponou PATH s kódováním HTML pro uživatelské rozhraní nebo protokolování.</span><span class="sxs-lookup"><span data-stu-id="315ee-193">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="315ee-194">Vygenerujte nový náhodný název souboru pro úložiště.</span><span class="sxs-lookup"><span data-stu-id="315ee-194">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="315ee-195">Následující kód odstraní cestu z názvu souboru:</span><span class="sxs-lookup"><span data-stu-id="315ee-195">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="315ee-196">Zde uvedené příklady neberou ohled na zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="315ee-196">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="315ee-197">Další informace jsou k dispozici v následujících částech a [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="315ee-197">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="315ee-198">Aspekty zabezpečení</span><span class="sxs-lookup"><span data-stu-id="315ee-198">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="315ee-199">Ověřování</span><span class="sxs-lookup"><span data-stu-id="315ee-199">Validation</span></span>](#validation)

<span data-ttu-id="315ee-200">Při nahrávání souborů pomocí vazby modelu a <xref:Microsoft.AspNetCore.Http.IFormFile> může metoda Action přijmout:</span><span class="sxs-lookup"><span data-stu-id="315ee-200">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="315ee-201">Jedna <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="315ee-201">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="315ee-202">Kterákoli z následujících kolekcí, které reprezentují několik souborů:</span><span class="sxs-lookup"><span data-stu-id="315ee-202">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="315ee-203">[Seznamu](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="315ee-203">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="315ee-204">Vazba odpovídá souborům formuláře podle názvu.</span><span class="sxs-lookup"><span data-stu-id="315ee-204">Binding matches form files by name.</span></span> <span data-ttu-id="315ee-205">Například hodnota HTML v se `name` `<input type="file" name="formFile">` musí shodovat s parametrem nebo vazbou vlastnosti jazyka C# ( `FormFile` ).</span><span class="sxs-lookup"><span data-stu-id="315ee-205">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="315ee-206">Další informace naleznete v části [název atributu matched na název parametru metody post](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="315ee-206">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="315ee-207">Následující příklad:</span><span class="sxs-lookup"><span data-stu-id="315ee-207">The following example:</span></span>

* <span data-ttu-id="315ee-208">Projde jedním nebo více nahranými soubory.</span><span class="sxs-lookup"><span data-stu-id="315ee-208">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="315ee-209">Pomocí [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) vrátí úplnou cestu k souboru, včetně názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="315ee-209">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="315ee-210">Uloží soubory do místního systému souborů pomocí názvu souboru generovaného aplikací.</span><span class="sxs-lookup"><span data-stu-id="315ee-210">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="315ee-211">Vrátí celkový počet nahraných souborů a jejich velikost.</span><span class="sxs-lookup"><span data-stu-id="315ee-211">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="315ee-212">Slouží `Path.GetRandomFileName` k vygenerování názvu souboru bez cesty.</span><span class="sxs-lookup"><span data-stu-id="315ee-212">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="315ee-213">V následujícím příkladu je cesta získána z konfigurace:</span><span class="sxs-lookup"><span data-stu-id="315ee-213">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="315ee-214">Cesta předaná do <xref:System.IO.FileStream> *musí* zahrnovat název souboru.</span><span class="sxs-lookup"><span data-stu-id="315ee-214">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="315ee-215">Pokud není zadán název souboru, <xref:System.UnauthorizedAccessException> je vyvolána za běhu.</span><span class="sxs-lookup"><span data-stu-id="315ee-215">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="315ee-216">Soubory odeslané pomocí <xref:Microsoft.AspNetCore.Http.IFormFile> techniky jsou v paměti nebo na disku na serveru před zpracováním uloženy do vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="315ee-216">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="315ee-217">V rámci metody Action je <xref:Microsoft.AspNetCore.Http.IFormFile> obsah přístupný jako <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="315ee-217">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="315ee-218">Kromě místního systému souborů je možné soubory ukládat do síťové sdílené složky nebo do služby úložiště souborů, jako je [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="315ee-218">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="315ee-219">Další příklad, který projde několik souborů pro nahrání a používá bezpečné názvy souborů, najdete v ukázkové aplikaci v části *Pages/BufferedMultipleFileUploadPhysical. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="315ee-219">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="315ee-220">[Cesta. GetTempFileName](xref:System.IO.Path.GetTempFileName*) vyvolá výjimku, <xref:System.IO.IOException> Pokud jsou vytvořeny více než 65 535 souborů bez odstranění předchozích dočasných souborů.</span><span class="sxs-lookup"><span data-stu-id="315ee-220">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="315ee-221">Limit 65 535 souborů je omezen na server.</span><span class="sxs-lookup"><span data-stu-id="315ee-221">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="315ee-222">Další informace o tomto limitu pro operační systém Windows najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="315ee-222">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="315ee-223">GetTempFileNameA – funkce</span><span class="sxs-lookup"><span data-stu-id="315ee-223">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="315ee-224">Nahrávání malých souborů s vazbou modelu s vyrovnávací pamětí do databáze</span><span class="sxs-lookup"><span data-stu-id="315ee-224">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="315ee-225">Chcete-li uložit data binárního souboru do databáze pomocí [Entity Framework](/ef/core/index), definujte v <xref:System.Byte> entitě vlastnost Array:</span><span class="sxs-lookup"><span data-stu-id="315ee-225">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="315ee-226">Zadejte vlastnost modelu stránky pro třídu, která obsahuje <xref:Microsoft.AspNetCore.Http.IFormFile> :</span><span class="sxs-lookup"><span data-stu-id="315ee-226">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="315ee-227"><xref:Microsoft.AspNetCore.Http.IFormFile>lze ji použít přímo jako parametr metody akce nebo jako vlastnost vázaného modelu.</span><span class="sxs-lookup"><span data-stu-id="315ee-227"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="315ee-228">Předchozí příklad používá vlastnost vázaného modelu.</span><span class="sxs-lookup"><span data-stu-id="315ee-228">The prior example uses a bound model property.</span></span>

<span data-ttu-id="315ee-229">`FileUpload`Je použit ve Razor formuláři stránky:</span><span class="sxs-lookup"><span data-stu-id="315ee-229">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="315ee-230">Když je formulář publikovaný na serveru, zkopírujte <xref:Microsoft.AspNetCore.Http.IFormFile> ho do datového proudu a uložte ho jako pole bajtů v databázi.</span><span class="sxs-lookup"><span data-stu-id="315ee-230">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="315ee-231">V následujícím příkladu `_dbContext` ukládá kontext databáze aplikace:</span><span class="sxs-lookup"><span data-stu-id="315ee-231">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="315ee-232">Předchozí příklad je podobný scénáři, který je znázorněný v ukázkové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="315ee-232">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="315ee-233">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="315ee-233">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="315ee-234">*Pages/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="315ee-234">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="315ee-235">Při ukládání binárních dat do relačních databází buďte opatrní, protože to může mít nepříznivý vliv na výkon.</span><span class="sxs-lookup"><span data-stu-id="315ee-235">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="315ee-236">Nespoléhá se na nebo důvěřujete `FileName` vlastnosti <xref:Microsoft.AspNetCore.Http.IFormFile> bez ověření.</span><span class="sxs-lookup"><span data-stu-id="315ee-236">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="315ee-237">`FileName`Vlastnost by měla být použita pouze pro účely zobrazení a pouze po kódování HTML.</span><span class="sxs-lookup"><span data-stu-id="315ee-237">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="315ee-238">Uvedené příklady nevezmou ohled na zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="315ee-238">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="315ee-239">Další informace jsou k dispozici v následujících částech a [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="315ee-239">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="315ee-240">Aspekty zabezpečení</span><span class="sxs-lookup"><span data-stu-id="315ee-240">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="315ee-241">Ověřování</span><span class="sxs-lookup"><span data-stu-id="315ee-241">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="315ee-242">Nahrávání velkých souborů pomocí streamování</span><span class="sxs-lookup"><span data-stu-id="315ee-242">Upload large files with streaming</span></span>

<span data-ttu-id="315ee-243">Následující příklad ukazuje, jak použít JavaScript ke streamování souboru do akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="315ee-243">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="315ee-244">Token proti padělání souboru se generuje pomocí vlastního atributu filtru a předává se do hlaviček protokolu HTTP klienta místo v textu žádosti.</span><span class="sxs-lookup"><span data-stu-id="315ee-244">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="315ee-245">Vzhledem k tomu, že metoda akce zpracovává nahraná data přímo, vazba modelu formuláře je zakázána jiným vlastním filtrem.</span><span class="sxs-lookup"><span data-stu-id="315ee-245">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="315ee-246">V rámci akce je obsah formuláře čten pomocí `MultipartReader` , který čte každou jednotlivou osobu `MultipartSection` , zpracovává soubor nebo ukládá obsah podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="315ee-246">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="315ee-247">Po načtení oddílů s více částmi provede akce vlastní vazbu modelu.</span><span class="sxs-lookup"><span data-stu-id="315ee-247">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="315ee-248">Počáteční odpověď stránky načte formulář a uloží token proti padělání do souboru cookie (prostřednictvím `GenerateAntiforgeryTokenCookieAttribute` atributu).</span><span class="sxs-lookup"><span data-stu-id="315ee-248">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="315ee-249">Atribut používá ASP.NET Core integrovanou [podporu proti padělání](xref:security/anti-request-forgery) pro nastavení souboru cookie s tokenem žádosti:</span><span class="sxs-lookup"><span data-stu-id="315ee-249">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="315ee-250">`DisableFormValueModelBindingAttribute`Slouží k zakázání vazby modelu:</span><span class="sxs-lookup"><span data-stu-id="315ee-250">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="315ee-251">V ukázkové aplikaci a používá `GenerateAntiforgeryTokenCookieAttribute` se `DisableFormValueModelBindingAttribute` jako filtr pro modelové aplikace stránky `/StreamedSingleFileUploadDb` a `/StreamedSingleFileUploadPhysical` v `Startup.ConfigureServices` [ Razor konvencích použití stránek](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="315ee-251">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=8-11,17-20)]

<span data-ttu-id="315ee-252">Vzhledem k tomu, že vazba modelu nepřečte formulář, parametry, které jsou svázané z formuláře, se nezobrazují (budou pokračovat v práci s dotazem, trasou a hlavičkou).</span><span class="sxs-lookup"><span data-stu-id="315ee-252">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="315ee-253">Metoda Action pracuje přímo s `Request` vlastností.</span><span class="sxs-lookup"><span data-stu-id="315ee-253">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="315ee-254">`MultipartReader`Slouží ke čtení jednotlivých oddílů.</span><span class="sxs-lookup"><span data-stu-id="315ee-254">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="315ee-255">Data klíč/hodnota jsou uložena v `KeyValueAccumulator` .</span><span class="sxs-lookup"><span data-stu-id="315ee-255">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="315ee-256">Po načtení oddílů s více částmi se obsah `KeyValueAccumulator` používá pro svázání dat formuláře s typem modelu.</span><span class="sxs-lookup"><span data-stu-id="315ee-256">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="315ee-257">Úplná `StreamingController.UploadDatabase` metoda pro streamování do databáze s EF Core:</span><span class="sxs-lookup"><span data-stu-id="315ee-257">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="315ee-258">`MultipartRequestHelper`(*Nástroje/MultipartRequestHelper. cs*):</span><span class="sxs-lookup"><span data-stu-id="315ee-258">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="315ee-259">Úplná `StreamingController.UploadPhysical` metoda pro streamování do fyzického umístění:</span><span class="sxs-lookup"><span data-stu-id="315ee-259">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="315ee-260">V ukázkové aplikaci jsou kontroly ověřování zpracovávány nástrojem `FileHelpers.ProcessStreamedFile` .</span><span class="sxs-lookup"><span data-stu-id="315ee-260">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="315ee-261">Ověřování</span><span class="sxs-lookup"><span data-stu-id="315ee-261">Validation</span></span>

<span data-ttu-id="315ee-262">Třída ukázkové aplikace `FileHelpers` ukazuje několik kontrol ukládání souborů do vyrovnávací paměti <xref:Microsoft.AspNetCore.Http.IFormFile> a datových proudů při nahrávání.</span><span class="sxs-lookup"><span data-stu-id="315ee-262">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="315ee-263">Informace o zpracování <xref:Microsoft.AspNetCore.Http.IFormFile> ukládání souborů do vyrovnávací paměti v ukázkové aplikaci naleznete v `ProcessFormFile` metodě v souboru *Utilities/Helper. cs* .</span><span class="sxs-lookup"><span data-stu-id="315ee-263">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="315ee-264">Pro zpracování streamované soubory se podívejte na `ProcessStreamedFile` metodu ve stejném souboru.</span><span class="sxs-lookup"><span data-stu-id="315ee-264">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="315ee-265">Metody zpracování ověřování, které jsou znázorněné v ukázkové aplikaci, nekontrolují obsah nahraných souborů.</span><span class="sxs-lookup"><span data-stu-id="315ee-265">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="315ee-266">Ve většině produkčních scénářů se v souboru používá rozhraní API pro skenování virů nebo malwaru, než je soubor dostupný uživatelům nebo jiným systémům.</span><span class="sxs-lookup"><span data-stu-id="315ee-266">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="315ee-267">I když ukázka tématu poskytuje pracovní příklad technik ověřování, Neimplementujte `FileHelpers` třídu v produkční aplikaci, pokud:</span><span class="sxs-lookup"><span data-stu-id="315ee-267">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="315ee-268">Plně rozumíte implementaci.</span><span class="sxs-lookup"><span data-stu-id="315ee-268">Fully understand the implementation.</span></span>
> * <span data-ttu-id="315ee-269">Upravte implementaci podle potřeby pro prostředí a specifikace aplikace.</span><span class="sxs-lookup"><span data-stu-id="315ee-269">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="315ee-270">**Nikdy nepoužívejte nerozlišený kód zabezpečení v aplikaci bez nutnosti řešit tyto požadavky.**</span><span class="sxs-lookup"><span data-stu-id="315ee-270">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="315ee-271">Ověření obsahu</span><span class="sxs-lookup"><span data-stu-id="315ee-271">Content validation</span></span>

<span data-ttu-id="315ee-272">**Pro nahraný obsah použijte rozhraní API pro kontrolu virů a malwaru třetí strany.**</span><span class="sxs-lookup"><span data-stu-id="315ee-272">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="315ee-273">Prohledávání souborů je náročné na prostředky serveru ve scénářích s vysokým objemem.</span><span class="sxs-lookup"><span data-stu-id="315ee-273">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="315ee-274">Pokud dojde ke snížení výkonu zpracování požadavků z důvodu kontroly souborů, zvažte přesměrování práce skenování na službu na [pozadí](xref:fundamentals/host/hosted-services), případně služby spuštěné na serveru, který se liší od serveru aplikace.</span><span class="sxs-lookup"><span data-stu-id="315ee-274">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="315ee-275">Nahrané soubory se obvykle uchovávají v oblasti v karanténě, dokud je kontrola virů na pozadí nevrátí.</span><span class="sxs-lookup"><span data-stu-id="315ee-275">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="315ee-276">Když soubor projde, soubor se přesune do normálního umístění úložiště souborů.</span><span class="sxs-lookup"><span data-stu-id="315ee-276">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="315ee-277">Tyto kroky se obvykle provádí ve spojení s databázovým záznamem, který indikuje stav kontroly souboru.</span><span class="sxs-lookup"><span data-stu-id="315ee-277">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="315ee-278">Při použití takového přístupu zůstane aplikace a Server App zaměřené na reakci na požadavky.</span><span class="sxs-lookup"><span data-stu-id="315ee-278">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="315ee-279">Ověření přípony souboru</span><span class="sxs-lookup"><span data-stu-id="315ee-279">File extension validation</span></span>

<span data-ttu-id="315ee-280">Přípona nahraného souboru by měla být zaškrtnutá na seznamu povolených rozšíření.</span><span class="sxs-lookup"><span data-stu-id="315ee-280">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="315ee-281">Příklad:</span><span class="sxs-lookup"><span data-stu-id="315ee-281">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="315ee-282">Ověření podpisu souboru</span><span class="sxs-lookup"><span data-stu-id="315ee-282">File signature validation</span></span>

<span data-ttu-id="315ee-283">Podpis souboru se určuje na prvních několika bajtech na začátku souboru.</span><span class="sxs-lookup"><span data-stu-id="315ee-283">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="315ee-284">Tyto bajty lze použít k určení, zda přípona odpovídá obsahu souboru.</span><span class="sxs-lookup"><span data-stu-id="315ee-284">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="315ee-285">Ukázková aplikace zkontroluje podpisy souborů pro několik běžných typů souborů.</span><span class="sxs-lookup"><span data-stu-id="315ee-285">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="315ee-286">V následujícím příkladu se pro tento soubor kontroluje podpis souboru obrázku JPEG:</span><span class="sxs-lookup"><span data-stu-id="315ee-286">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="315ee-287">Další signatury souborů získáte v dokumentaci [signatury souborů](https://www.filesignatures.net/) a oficiálních souborů.</span><span class="sxs-lookup"><span data-stu-id="315ee-287">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="315ee-288">Zabezpečení názvu souboru</span><span class="sxs-lookup"><span data-stu-id="315ee-288">File name security</span></span>

<span data-ttu-id="315ee-289">Nikdy nepoužívejte název souboru dodaný klientem pro uložení souboru do fyzického úložiště.</span><span class="sxs-lookup"><span data-stu-id="315ee-289">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="315ee-290">Vytvořte bezpečný název souboru pro soubor pomocí [cesty. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) nebo [cesty. GetTempFileName](xref:System.IO.Path.GetTempFileName*) a vytvořte úplnou cestu (včetně názvu souboru) pro dočasné úložiště.</span><span class="sxs-lookup"><span data-stu-id="315ee-290">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

Razor<span data-ttu-id="315ee-291">Automatické kódování HTML kóduje hodnoty vlastností pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="315ee-291"> automatically HTML encodes property values for display.</span></span> <span data-ttu-id="315ee-292">Následující kód je bezpečné použít:</span><span class="sxs-lookup"><span data-stu-id="315ee-292">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="315ee-293">Mimo Razor , vždy <xref:System.Net.WebUtility.HtmlEncode*> obsah názvu souboru z požadavku uživatele.</span><span class="sxs-lookup"><span data-stu-id="315ee-293">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="315ee-294">Mnoho implementací musí zahrnovat kontrolu, že soubor existuje. v opačném případě je soubor přepsán souborem se stejným názvem.</span><span class="sxs-lookup"><span data-stu-id="315ee-294">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="315ee-295">Poskytněte další logiku pro splnění specifikací vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="315ee-295">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="315ee-296">Ověřování velikosti</span><span class="sxs-lookup"><span data-stu-id="315ee-296">Size validation</span></span>

<span data-ttu-id="315ee-297">Omezte velikost nahraných souborů.</span><span class="sxs-lookup"><span data-stu-id="315ee-297">Limit the size of uploaded files.</span></span>

<span data-ttu-id="315ee-298">V ukázkové aplikaci je velikost souboru omezená na 2 MB (uvedené v bajtech).</span><span class="sxs-lookup"><span data-stu-id="315ee-298">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="315ee-299">Limit je zadán prostřednictvím [Konfigurace](xref:fundamentals/configuration/index) ze souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="315ee-299">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="315ee-300">`FileSizeLimit`Třída je vložena do `PageModel` tříd:</span><span class="sxs-lookup"><span data-stu-id="315ee-300">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="315ee-301">Když velikost souboru překročí limit, soubor se odmítne:</span><span class="sxs-lookup"><span data-stu-id="315ee-301">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="315ee-302">Porovnává hodnotu atributu name s parametrem název metody POST</span><span class="sxs-lookup"><span data-stu-id="315ee-302">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="315ee-303">V Razor neformulářích, které publikují data formuláře nebo přímo využívají JavaScript `FormData` , název zadaný v prvku formuláře nebo `FormData` musí odpovídat názvu parametru v akci kontroleru.</span><span class="sxs-lookup"><span data-stu-id="315ee-303">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="315ee-304">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="315ee-304">In the following example:</span></span>

* <span data-ttu-id="315ee-305">Při použití `<input>` elementu `name` je atribut nastaven na hodnotu `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="315ee-305">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="315ee-306">Při použití `FormData` v jazyce JavaScript je název nastaven na hodnotu `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="315ee-306">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="315ee-307">Pro parametr metody jazyka C# () použijte stejný název `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="315ee-307">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="315ee-308">Pro Razor metodu obslužné rutiny stránky stránky s názvem `Upload` :</span><span class="sxs-lookup"><span data-stu-id="315ee-308">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="315ee-309">Pro metodu akce po kontroléru MVC:</span><span class="sxs-lookup"><span data-stu-id="315ee-309">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="315ee-310">Konfigurace serveru a aplikace</span><span class="sxs-lookup"><span data-stu-id="315ee-310">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="315ee-311">Omezení délky těla částí</span><span class="sxs-lookup"><span data-stu-id="315ee-311">Multipart body length limit</span></span>

<span data-ttu-id="315ee-312"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>nastaví limit délky jednotlivých částí části.</span><span class="sxs-lookup"><span data-stu-id="315ee-312"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="315ee-313">Oddíly formuláře, které překračují toto omezení, vyvolávají <xref:System.IO.InvalidDataException> při analýze.</span><span class="sxs-lookup"><span data-stu-id="315ee-313">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="315ee-314">Výchozí hodnota je 134 217 728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="315ee-314">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="315ee-315">Upravte limit pomocí <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> nastavení v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="315ee-315">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="315ee-316"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>slouží k nastavení <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> pro jednu stránku nebo akci.</span><span class="sxs-lookup"><span data-stu-id="315ee-316"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="315ee-317">V Razor aplikaci Pages použijte filtr s [konvencí](xref:razor-pages/razor-pages-conventions) v nástroji `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="315ee-317">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="315ee-318">V Razor aplikaci Pages nebo aplikaci MVC použijte filtr na model stránky nebo metodu akce:</span><span class="sxs-lookup"><span data-stu-id="315ee-318">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="315ee-319">Kestrel maximální velikost textu požadavku</span><span class="sxs-lookup"><span data-stu-id="315ee-319">Kestrel maximum request body size</span></span>

<span data-ttu-id="315ee-320">Pro aplikace hostované v Kestrel je výchozí maximální velikost textu požadavku 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="315ee-320">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="315ee-321">Přizpůsobte limit pomocí možnosti serveru [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:</span><span class="sxs-lookup"><span data-stu-id="315ee-321">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="315ee-322"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>slouží k nastavení [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) pro jednu stránku nebo akci.</span><span class="sxs-lookup"><span data-stu-id="315ee-322"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="315ee-323">V Razor aplikaci Pages použijte filtr s [konvencí](xref:razor-pages/razor-pages-conventions) v nástroji `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="315ee-323">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="315ee-324">V Razor aplikaci Pages nebo aplikaci MVC použijte filtr na třídu obslužné rutiny stránky nebo na metodu akce:</span><span class="sxs-lookup"><span data-stu-id="315ee-324">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="315ee-325">`RequestSizeLimitAttribute`Lze také použít pomocí [`@attribute`](xref:mvc/views/razor#attribute) Razor direktivy:</span><span class="sxs-lookup"><span data-stu-id="315ee-325">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="315ee-326">Další omezení Kestrel</span><span class="sxs-lookup"><span data-stu-id="315ee-326">Other Kestrel limits</span></span>

<span data-ttu-id="315ee-327">Pro aplikace hostované v Kestrel se můžou vztahovat další omezení Kestrel:</span><span class="sxs-lookup"><span data-stu-id="315ee-327">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="315ee-328">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="315ee-328">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="315ee-329">Sazby za data požadavků a odpovědí</span><span class="sxs-lookup"><span data-stu-id="315ee-329">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="315ee-330">Omezení délky obsahu služby IIS</span><span class="sxs-lookup"><span data-stu-id="315ee-330">IIS content length limit</span></span>

<span data-ttu-id="315ee-331">Výchozí limit počtu požadavků ( `maxAllowedContentLength` ) je 30 000 000 bajtů, což je přibližně 28.6 MB.</span><span class="sxs-lookup"><span data-stu-id="315ee-331">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="315ee-332">Upravte limit v souboru *Web. config* :</span><span class="sxs-lookup"><span data-stu-id="315ee-332">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="315ee-333">Toto nastavení platí pouze pro službu IIS.</span><span class="sxs-lookup"><span data-stu-id="315ee-333">This setting only applies to IIS.</span></span> <span data-ttu-id="315ee-334">K tomuto chování nedochází ve výchozím nastavení při hostování v Kestrel.</span><span class="sxs-lookup"><span data-stu-id="315ee-334">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="315ee-335">Další informace najdete v tématu [omezení požadavků \< requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="315ee-335">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="315ee-336">Omezení v modulu ASP.NET Core nebo přítomnosti modulu filtrování požadavků služby IIS mohou omezit nahrávání na 2 nebo 4 GB.</span><span class="sxs-lookup"><span data-stu-id="315ee-336">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="315ee-337">Další informace najdete v tématu [nelze odeslat soubor o velikosti větší než 2 GB (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span><span class="sxs-lookup"><span data-stu-id="315ee-337">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="315ee-338">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="315ee-338">Troubleshoot</span></span>

<span data-ttu-id="315ee-339">Níže jsou uvedeny některé běžné problémy, které se vyskytly při práci s nahráváním souborů a jejich možnými řešeními.</span><span class="sxs-lookup"><span data-stu-id="315ee-339">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="315ee-340">Při nasazení na server služby IIS se nenašla chyba.</span><span class="sxs-lookup"><span data-stu-id="315ee-340">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="315ee-341">Následující chyba znamená, že nahraný soubor překračuje délku nakonfigurovaného obsahu serveru:</span><span class="sxs-lookup"><span data-stu-id="315ee-341">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="315ee-342">Další informace o zvýšení limitu najdete v části [omezení délky obsahu služby IIS](#iis-content-length-limit) .</span><span class="sxs-lookup"><span data-stu-id="315ee-342">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="315ee-343">Chyba připojení</span><span class="sxs-lookup"><span data-stu-id="315ee-343">Connection failure</span></span>

<span data-ttu-id="315ee-344">Chyba připojení a připojení k serveru pro resetování pravděpodobně znamená, že nahraný soubor překračuje maximální velikost textu požadavku Kestrel.</span><span class="sxs-lookup"><span data-stu-id="315ee-344">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="315ee-345">Další informace najdete v části [Kestrel maximální velikost textu požadavku](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="315ee-345">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="315ee-346">Omezení připojení klientů Kestrel mohou také vyžadovat úpravu.</span><span class="sxs-lookup"><span data-stu-id="315ee-346">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="315ee-347">Výjimka odkazu s hodnotou null s IFormFile</span><span class="sxs-lookup"><span data-stu-id="315ee-347">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="315ee-348">Pokud kontroler přijímá odeslané soubory pomocí <xref:Microsoft.AspNetCore.Http.IFormFile> , ale hodnota je `null` , potvrďte, že formulář HTML určuje `enctype` hodnotu `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="315ee-348">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="315ee-349">Pokud tento atribut není nastaven na `<form>` elementu, odeslání souboru neproběhne a jakékoli vázané <xref:Microsoft.AspNetCore.Http.IFormFile> argumenty jsou `null` .</span><span class="sxs-lookup"><span data-stu-id="315ee-349">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="315ee-350">Ujistěte se také, že [nahrávání názvů v datech formuláře odpovídá pojmenování aplikace](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="315ee-350">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="315ee-351">Proud je příliš dlouhý.</span><span class="sxs-lookup"><span data-stu-id="315ee-351">Stream was too long</span></span>

<span data-ttu-id="315ee-352">Příklady v tomto tématu <xref:System.IO.MemoryStream> se spoléhají na uchovávání obsahu nahraného souboru.</span><span class="sxs-lookup"><span data-stu-id="315ee-352">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="315ee-353">Omezení velikosti `MemoryStream` je `int.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="315ee-353">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="315ee-354">Pokud scénář nahrávání souborů aplikace vyžaduje, aby byl obsah souboru větší než 50 MB, použijte alternativní přístup, který nespoléhá na jednu z nich `MemoryStream` pro uchovávání obsahu nahraného souboru.</span><span class="sxs-lookup"><span data-stu-id="315ee-354">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="315ee-355">ASP.NET Core podporuje nahrávání jednoho nebo více souborů pomocí vazby modelu ve vyrovnávací paměti pro menší soubory a streamování bez vyrovnávací paměti pro větší soubory.</span><span class="sxs-lookup"><span data-stu-id="315ee-355">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="315ee-356">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="315ee-356">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="315ee-357">Aspekty zabezpečení</span><span class="sxs-lookup"><span data-stu-id="315ee-357">Security considerations</span></span>

<span data-ttu-id="315ee-358">Pokud chcete uživatelům poskytnout možnost nahrávat soubory na server, buďte opatrní.</span><span class="sxs-lookup"><span data-stu-id="315ee-358">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="315ee-359">Útočníci se můžou pokusit:</span><span class="sxs-lookup"><span data-stu-id="315ee-359">Attackers may attempt to:</span></span>

* <span data-ttu-id="315ee-360">Vykoná útok [DOS (Denial of Service](/windows-hardware/drivers/ifs/denial-of-service) ).</span><span class="sxs-lookup"><span data-stu-id="315ee-360">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="315ee-361">Nahrání virů nebo malwaru</span><span class="sxs-lookup"><span data-stu-id="315ee-361">Upload viruses or malware.</span></span>
* <span data-ttu-id="315ee-362">Narušit sítě a servery jinými způsoby.</span><span class="sxs-lookup"><span data-stu-id="315ee-362">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="315ee-363">Bezpečnostní kroky, které snižují pravděpodobnost úspěšného útoku, jsou:</span><span class="sxs-lookup"><span data-stu-id="315ee-363">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="315ee-364">Nahrajte soubory do vyhrazené oblasti pro nahrávání souborů, nejlépe do nesystémové jednotky.</span><span class="sxs-lookup"><span data-stu-id="315ee-364">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="315ee-365">Vyhrazené umístění usnadňuje omezení zabezpečení pro nahrané soubory.</span><span class="sxs-lookup"><span data-stu-id="315ee-365">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="315ee-366">Zakažte oprávnění EXECUTE pro umístění pro nahrání souboru.&dagger;</span><span class="sxs-lookup"><span data-stu-id="315ee-366">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="315ee-367">Neuchovávat nahrané soubory ve stejném stromu adresářů jako aplikace. **not**&dagger;</span><span class="sxs-lookup"><span data-stu-id="315ee-367">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="315ee-368">Použijte název bezpečného souboru určený aplikací.</span><span class="sxs-lookup"><span data-stu-id="315ee-368">Use a safe file name determined by the app.</span></span> <span data-ttu-id="315ee-369">Nepoužívejte název souboru poskytnutý uživatelem nebo nedůvěryhodného názvu nahraného souboru. &dagger; HTML při zobrazení kódování názvu nedůvěryhodného souboru.</span><span class="sxs-lookup"><span data-stu-id="315ee-369">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="315ee-370">Například protokolování názvu souboru nebo zobrazení v uživatelském rozhraní ( Razor Automatické kódování HTML kódování).</span><span class="sxs-lookup"><span data-stu-id="315ee-370">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="315ee-371">Povolte pro specifikaci návrhu aplikace jenom schválené přípony souborů.&dagger;</span><span class="sxs-lookup"><span data-stu-id="315ee-371">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="315ee-372">Ověřte, zda jsou na serveru provedeny kontroly na straně klienta. &dagger; Kontroly na straně klienta je snadné obejít.</span><span class="sxs-lookup"><span data-stu-id="315ee-372">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="315ee-373">Ověřte velikost nahraného souboru.</span><span class="sxs-lookup"><span data-stu-id="315ee-373">Check the size of an uploaded file.</span></span> <span data-ttu-id="315ee-374">Nastavte limit maximální velikosti, aby se zabránilo velkým nahrávání.&dagger;</span><span class="sxs-lookup"><span data-stu-id="315ee-374">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="315ee-375">Pokud by soubory neměly být přepsány nahraným souborem se stejným názvem, před nahráním souboru ověřte název souboru proti databázi nebo fyzickému úložišti.</span><span class="sxs-lookup"><span data-stu-id="315ee-375">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="315ee-376">**Před uložením souboru spusťte v nahraném obsahu skener virů nebo malwaru.**</span><span class="sxs-lookup"><span data-stu-id="315ee-376">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="315ee-377">&dagger;Ukázková aplikace předvádí přístup, který splňuje kritéria.</span><span class="sxs-lookup"><span data-stu-id="315ee-377">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="315ee-378">Nahrávání škodlivého kódu do systému je často prvním krokem ke spuštění kódu, který může:</span><span class="sxs-lookup"><span data-stu-id="315ee-378">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="315ee-379">Zcela získá kontrolu nad systémem.</span><span class="sxs-lookup"><span data-stu-id="315ee-379">Completely gain control of a system.</span></span>
> * <span data-ttu-id="315ee-380">Přetížit systém s výsledkem, že dojde k chybě systému.</span><span class="sxs-lookup"><span data-stu-id="315ee-380">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="315ee-381">Napadnout data uživatelů nebo systémových dat.</span><span class="sxs-lookup"><span data-stu-id="315ee-381">Compromise user or system data.</span></span>
> * <span data-ttu-id="315ee-382">Použijte graffiti pro veřejné uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="315ee-382">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="315ee-383">Informace o omezení oblasti útoku při přijímání souborů uživateli najdete v následujících zdrojích informací:</span><span class="sxs-lookup"><span data-stu-id="315ee-383">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="315ee-384">Neomezená nahrávání souboru</span><span class="sxs-lookup"><span data-stu-id="315ee-384">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="315ee-385">Zabezpečení Azure: Ujistěte se, že jsou při přijímání souborů od uživatelů k dismístě správné ovládací prvky.</span><span class="sxs-lookup"><span data-stu-id="315ee-385">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="315ee-386">Další informace o implementaci bezpečnostních opatření, včetně příkladů z ukázkové aplikace, najdete v části [ověření](#validation) .</span><span class="sxs-lookup"><span data-stu-id="315ee-386">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="315ee-387">Scénáře úložiště</span><span class="sxs-lookup"><span data-stu-id="315ee-387">Storage scenarios</span></span>

<span data-ttu-id="315ee-388">Mezi běžné možnosti úložiště pro soubory patří:</span><span class="sxs-lookup"><span data-stu-id="315ee-388">Common storage options for files include:</span></span>

* <span data-ttu-id="315ee-389">databáze</span><span class="sxs-lookup"><span data-stu-id="315ee-389">Database</span></span>

  * <span data-ttu-id="315ee-390">U malých nahrávání souborů je databáze často rychlejší než možnosti fyzického úložiště (systému souborů nebo síťového sdílení).</span><span class="sxs-lookup"><span data-stu-id="315ee-390">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="315ee-391">Databáze je často pohodlnější než možnosti fyzického úložiště, protože načtení záznamu databáze pro uživatelská data může současně poskytovat obsah souboru (například obrázek miniatury).</span><span class="sxs-lookup"><span data-stu-id="315ee-391">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="315ee-392">Databáze je potenciálně levnější než použití služby úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="315ee-392">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="315ee-393">Fyzické úložiště (systém souborů nebo síťová sdílená složka)</span><span class="sxs-lookup"><span data-stu-id="315ee-393">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="315ee-394">Pro nahrávání velkých souborů:</span><span class="sxs-lookup"><span data-stu-id="315ee-394">For large file uploads:</span></span>
    * <span data-ttu-id="315ee-395">Omezení databáze mohou omezit velikost nahrávání.</span><span class="sxs-lookup"><span data-stu-id="315ee-395">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="315ee-396">Fyzické úložiště je často méně hospodárné než úložiště v databázi.</span><span class="sxs-lookup"><span data-stu-id="315ee-396">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="315ee-397">Fyzické úložiště je potenciálně levnější než použití služby úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="315ee-397">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="315ee-398">Proces aplikace musí mít oprávnění ke čtení a zápisu do umístění úložiště.</span><span class="sxs-lookup"><span data-stu-id="315ee-398">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="315ee-399">**Nikdy neudělujte oprávnění EXECUTE.**</span><span class="sxs-lookup"><span data-stu-id="315ee-399">**Never grant execute permission.**</span></span>

* <span data-ttu-id="315ee-400">Služba úložiště dat (například [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="315ee-400">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="315ee-401">Služby obvykle nabízejí vylepšenou škálovatelnost a odolnost proti místním řešením, které obvykle podléhají jednomu bodu selhání.</span><span class="sxs-lookup"><span data-stu-id="315ee-401">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="315ee-402">Služby jsou potenciálně nižší náklady ve scénářích infrastruktury velkých úložišť.</span><span class="sxs-lookup"><span data-stu-id="315ee-402">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="315ee-403">Další informace najdete v tématu [rychlý Start: použití .NET k vytvoření objektu BLOB v úložišti objektů](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="315ee-403">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="315ee-404">Téma ukazuje <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*> , ale <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> dá se použít k uložení do <xref:System.IO.FileStream> úložiště objektů BLOB při práci s <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="315ee-404">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="315ee-405">Scénáře nahrávání souborů</span><span class="sxs-lookup"><span data-stu-id="315ee-405">File upload scenarios</span></span>

<span data-ttu-id="315ee-406">Dva obecné přístupy k nahrávání souborů jsou ukládání do vyrovnávací paměti a streamování.</span><span class="sxs-lookup"><span data-stu-id="315ee-406">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="315ee-407">**Do vyrovnávací paměti**</span><span class="sxs-lookup"><span data-stu-id="315ee-407">**Buffering**</span></span>

<span data-ttu-id="315ee-408">Celý soubor je načten do <xref:Microsoft.AspNetCore.Http.IFormFile> , což je reprezentace souboru, který se používá ke zpracování nebo uložení souboru v jazyce C#.</span><span class="sxs-lookup"><span data-stu-id="315ee-408">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="315ee-409">Prostředky (disk, paměť) používané při nahrávání souborů závisí na počtu a velikosti souběžných nahrávání souborů.</span><span class="sxs-lookup"><span data-stu-id="315ee-409">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="315ee-410">Pokud se aplikace pokusí do vyrovnávací paměti příliš mnoho nahrávání, dojde k selhání lokality, když dojde k vynechání paměti nebo místa na disku.</span><span class="sxs-lookup"><span data-stu-id="315ee-410">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="315ee-411">Pokud velikost nebo frekvence nahrávání souborů vyčerpá prostředky aplikace, použijte streamování.</span><span class="sxs-lookup"><span data-stu-id="315ee-411">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="315ee-412">Z paměti do dočasného souboru na disku se přesune libovolný soubor s vyrovnávací pamětí větší než 64 KB.</span><span class="sxs-lookup"><span data-stu-id="315ee-412">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="315ee-413">Ukládání malých souborů do vyrovnávací paměti je popsáno v následujících částech tohoto tématu:</span><span class="sxs-lookup"><span data-stu-id="315ee-413">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="315ee-414">Fyzické úložiště</span><span class="sxs-lookup"><span data-stu-id="315ee-414">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="315ee-415">Databáze</span><span class="sxs-lookup"><span data-stu-id="315ee-415">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="315ee-416">**Streamování**</span><span class="sxs-lookup"><span data-stu-id="315ee-416">**Streaming**</span></span>

<span data-ttu-id="315ee-417">Soubor se přijímá z požadavku na více částí a přímo se zpracovává nebo ukládá v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="315ee-417">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="315ee-418">Streamování nijak významně nezvyšuje výkon.</span><span class="sxs-lookup"><span data-stu-id="315ee-418">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="315ee-419">Streamování snižuje nároky na paměť nebo místo na disku při nahrávání souborů.</span><span class="sxs-lookup"><span data-stu-id="315ee-419">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="315ee-420">Streamování velkých souborů je zahrnuté v části [nahrávání velkých souborů pomocí streamování](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="315ee-420">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="315ee-421">Nahrávání malých souborů s vazbou modelu ve vyrovnávací paměti na fyzické úložiště</span><span class="sxs-lookup"><span data-stu-id="315ee-421">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="315ee-422">Pro nahrání malých souborů použijte formulář s více částmi nebo sestavte požadavek POST pomocí JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="315ee-422">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="315ee-423">Následující příklad ukazuje použití Razor formuláře stránky k nahrání jednoho souboru (*Pages/BufferedSingleFileUploadPhysical. cshtml* do ukázkové aplikace):</span><span class="sxs-lookup"><span data-stu-id="315ee-423">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="315ee-424">Následující příklad je podobný předchozímu příkladu s tím rozdílem, že:</span><span class="sxs-lookup"><span data-stu-id="315ee-424">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="315ee-425">K odeslání dat formuláře se používá JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)).</span><span class="sxs-lookup"><span data-stu-id="315ee-425">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="315ee-426">Neexistuje žádné ověření.</span><span class="sxs-lookup"><span data-stu-id="315ee-426">There's no validation.</span></span>

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

<span data-ttu-id="315ee-427">Chcete-li provést příspěvek formuláře v jazyce JavaScript pro klienty, kteří [nepodporují rozhraní API pro načítání](https://caniuse.com/#feat=fetch), použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="315ee-427">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="315ee-428">Použijte načtenou výplň (například [window. Fetch Fill (GitHub/Fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="315ee-428">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="315ee-429">Použijte `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="315ee-429">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="315ee-430">Příklad:</span><span class="sxs-lookup"><span data-stu-id="315ee-430">For example:</span></span>

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

<span data-ttu-id="315ee-431">Aby bylo možné podporovat nahrávání souborů, musí formuláře HTML určovat typ kódování ( `enctype` ) `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="315ee-431">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="315ee-432">Pro `files` vstupní element, který podporuje nahrávání více souborů, poskytněte `multiple` atribut `<input>` elementu:</span><span class="sxs-lookup"><span data-stu-id="315ee-432">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="315ee-433">Jednotlivé soubory nahrané na server jsou k dispozici prostřednictvím [vazby modelu](xref:mvc/models/model-binding) pomocí <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="315ee-433">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="315ee-434">Ukázková aplikace ukazuje více ukládání souborů do vyrovnávací paměti pro scénáře databáze a fyzických úložišť.</span><span class="sxs-lookup"><span data-stu-id="315ee-434">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="315ee-435">Nepoužívejte **not** `FileName` vlastnost <xref:Microsoft.AspNetCore.Http.IFormFile> jinou než pro zobrazení a protokolování.</span><span class="sxs-lookup"><span data-stu-id="315ee-435">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="315ee-436">Při zobrazení nebo protokolování je název souboru kódován HTML.</span><span class="sxs-lookup"><span data-stu-id="315ee-436">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="315ee-437">Útočník může poskytnout škodlivý název souboru, včetně úplných cest nebo relativních cest.</span><span class="sxs-lookup"><span data-stu-id="315ee-437">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="315ee-438">Aplikace by měly:</span><span class="sxs-lookup"><span data-stu-id="315ee-438">Applications should:</span></span>
>
> * <span data-ttu-id="315ee-439">Odeberte cestu z názvu souboru zadaného uživatelem.</span><span class="sxs-lookup"><span data-stu-id="315ee-439">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="315ee-440">Uložte název souboru s příponou PATH s kódováním HTML pro uživatelské rozhraní nebo protokolování.</span><span class="sxs-lookup"><span data-stu-id="315ee-440">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="315ee-441">Vygenerujte nový náhodný název souboru pro úložiště.</span><span class="sxs-lookup"><span data-stu-id="315ee-441">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="315ee-442">Následující kód odstraní cestu z názvu souboru:</span><span class="sxs-lookup"><span data-stu-id="315ee-442">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="315ee-443">Zde uvedené příklady neberou ohled na zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="315ee-443">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="315ee-444">Další informace jsou k dispozici v následujících částech a [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="315ee-444">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="315ee-445">Aspekty zabezpečení</span><span class="sxs-lookup"><span data-stu-id="315ee-445">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="315ee-446">Ověřování</span><span class="sxs-lookup"><span data-stu-id="315ee-446">Validation</span></span>](#validation)

<span data-ttu-id="315ee-447">Při nahrávání souborů pomocí vazby modelu a <xref:Microsoft.AspNetCore.Http.IFormFile> může metoda Action přijmout:</span><span class="sxs-lookup"><span data-stu-id="315ee-447">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="315ee-448">Jedna <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="315ee-448">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="315ee-449">Kterákoli z následujících kolekcí, které reprezentují několik souborů:</span><span class="sxs-lookup"><span data-stu-id="315ee-449">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="315ee-450">[Seznamu](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="315ee-450">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="315ee-451">Vazba odpovídá souborům formuláře podle názvu.</span><span class="sxs-lookup"><span data-stu-id="315ee-451">Binding matches form files by name.</span></span> <span data-ttu-id="315ee-452">Například hodnota HTML v se `name` `<input type="file" name="formFile">` musí shodovat s parametrem nebo vazbou vlastnosti jazyka C# ( `FormFile` ).</span><span class="sxs-lookup"><span data-stu-id="315ee-452">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="315ee-453">Další informace naleznete v části [název atributu matched na název parametru metody post](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="315ee-453">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="315ee-454">Následující příklad:</span><span class="sxs-lookup"><span data-stu-id="315ee-454">The following example:</span></span>

* <span data-ttu-id="315ee-455">Projde jedním nebo více nahranými soubory.</span><span class="sxs-lookup"><span data-stu-id="315ee-455">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="315ee-456">Pomocí [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) vrátí úplnou cestu k souboru, včetně názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="315ee-456">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="315ee-457">Uloží soubory do místního systému souborů pomocí názvu souboru generovaného aplikací.</span><span class="sxs-lookup"><span data-stu-id="315ee-457">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="315ee-458">Vrátí celkový počet nahraných souborů a jejich velikost.</span><span class="sxs-lookup"><span data-stu-id="315ee-458">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="315ee-459">Slouží `Path.GetRandomFileName` k vygenerování názvu souboru bez cesty.</span><span class="sxs-lookup"><span data-stu-id="315ee-459">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="315ee-460">V následujícím příkladu je cesta získána z konfigurace:</span><span class="sxs-lookup"><span data-stu-id="315ee-460">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="315ee-461">Cesta předaná do <xref:System.IO.FileStream> *musí* zahrnovat název souboru.</span><span class="sxs-lookup"><span data-stu-id="315ee-461">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="315ee-462">Pokud není zadán název souboru, <xref:System.UnauthorizedAccessException> je vyvolána za běhu.</span><span class="sxs-lookup"><span data-stu-id="315ee-462">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="315ee-463">Soubory odeslané pomocí <xref:Microsoft.AspNetCore.Http.IFormFile> techniky jsou v paměti nebo na disku na serveru před zpracováním uloženy do vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="315ee-463">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="315ee-464">V rámci metody Action je <xref:Microsoft.AspNetCore.Http.IFormFile> obsah přístupný jako <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="315ee-464">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="315ee-465">Kromě místního systému souborů je možné soubory ukládat do síťové sdílené složky nebo do služby úložiště souborů, jako je [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="315ee-465">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="315ee-466">Další příklad, který projde několik souborů pro nahrání a používá bezpečné názvy souborů, najdete v ukázkové aplikaci v části *Pages/BufferedMultipleFileUploadPhysical. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="315ee-466">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="315ee-467">[Cesta. GetTempFileName](xref:System.IO.Path.GetTempFileName*) vyvolá výjimku, <xref:System.IO.IOException> Pokud jsou vytvořeny více než 65 535 souborů bez odstranění předchozích dočasných souborů.</span><span class="sxs-lookup"><span data-stu-id="315ee-467">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="315ee-468">Limit 65 535 souborů je omezen na server.</span><span class="sxs-lookup"><span data-stu-id="315ee-468">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="315ee-469">Další informace o tomto limitu pro operační systém Windows najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="315ee-469">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="315ee-470">GetTempFileNameA – funkce</span><span class="sxs-lookup"><span data-stu-id="315ee-470">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="315ee-471">Nahrávání malých souborů s vazbou modelu s vyrovnávací pamětí do databáze</span><span class="sxs-lookup"><span data-stu-id="315ee-471">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="315ee-472">Chcete-li uložit data binárního souboru do databáze pomocí [Entity Framework](/ef/core/index), definujte v <xref:System.Byte> entitě vlastnost Array:</span><span class="sxs-lookup"><span data-stu-id="315ee-472">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="315ee-473">Zadejte vlastnost modelu stránky pro třídu, která obsahuje <xref:Microsoft.AspNetCore.Http.IFormFile> :</span><span class="sxs-lookup"><span data-stu-id="315ee-473">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="315ee-474"><xref:Microsoft.AspNetCore.Http.IFormFile>lze ji použít přímo jako parametr metody akce nebo jako vlastnost vázaného modelu.</span><span class="sxs-lookup"><span data-stu-id="315ee-474"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="315ee-475">Předchozí příklad používá vlastnost vázaného modelu.</span><span class="sxs-lookup"><span data-stu-id="315ee-475">The prior example uses a bound model property.</span></span>

<span data-ttu-id="315ee-476">`FileUpload`Je použit ve Razor formuláři stránky:</span><span class="sxs-lookup"><span data-stu-id="315ee-476">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="315ee-477">Když je formulář publikovaný na serveru, zkopírujte <xref:Microsoft.AspNetCore.Http.IFormFile> ho do datového proudu a uložte ho jako pole bajtů v databázi.</span><span class="sxs-lookup"><span data-stu-id="315ee-477">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="315ee-478">V následujícím příkladu `_dbContext` ukládá kontext databáze aplikace:</span><span class="sxs-lookup"><span data-stu-id="315ee-478">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="315ee-479">Předchozí příklad je podobný scénáři, který je znázorněný v ukázkové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="315ee-479">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="315ee-480">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="315ee-480">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="315ee-481">*Pages/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="315ee-481">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="315ee-482">Při ukládání binárních dat do relačních databází buďte opatrní, protože to může mít nepříznivý vliv na výkon.</span><span class="sxs-lookup"><span data-stu-id="315ee-482">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="315ee-483">Nespoléhá se na nebo důvěřujete `FileName` vlastnosti <xref:Microsoft.AspNetCore.Http.IFormFile> bez ověření.</span><span class="sxs-lookup"><span data-stu-id="315ee-483">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="315ee-484">`FileName`Vlastnost by měla být použita pouze pro účely zobrazení a pouze po kódování HTML.</span><span class="sxs-lookup"><span data-stu-id="315ee-484">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="315ee-485">Uvedené příklady nevezmou ohled na zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="315ee-485">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="315ee-486">Další informace jsou k dispozici v následujících částech a [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="315ee-486">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="315ee-487">Aspekty zabezpečení</span><span class="sxs-lookup"><span data-stu-id="315ee-487">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="315ee-488">Ověřování</span><span class="sxs-lookup"><span data-stu-id="315ee-488">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="315ee-489">Nahrávání velkých souborů pomocí streamování</span><span class="sxs-lookup"><span data-stu-id="315ee-489">Upload large files with streaming</span></span>

<span data-ttu-id="315ee-490">Následující příklad ukazuje, jak použít JavaScript ke streamování souboru do akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="315ee-490">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="315ee-491">Token proti padělání souboru se generuje pomocí vlastního atributu filtru a předává se do hlaviček protokolu HTTP klienta místo v textu žádosti.</span><span class="sxs-lookup"><span data-stu-id="315ee-491">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="315ee-492">Vzhledem k tomu, že metoda akce zpracovává nahraná data přímo, vazba modelu formuláře je zakázána jiným vlastním filtrem.</span><span class="sxs-lookup"><span data-stu-id="315ee-492">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="315ee-493">V rámci akce je obsah formuláře čten pomocí `MultipartReader` , který čte každou jednotlivou osobu `MultipartSection` , zpracovává soubor nebo ukládá obsah podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="315ee-493">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="315ee-494">Po načtení oddílů s více částmi provede akce vlastní vazbu modelu.</span><span class="sxs-lookup"><span data-stu-id="315ee-494">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="315ee-495">Počáteční odpověď stránky načte formulář a uloží token proti padělání do souboru cookie (prostřednictvím `GenerateAntiforgeryTokenCookieAttribute` atributu).</span><span class="sxs-lookup"><span data-stu-id="315ee-495">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="315ee-496">Atribut používá ASP.NET Core integrovanou [podporu proti padělání](xref:security/anti-request-forgery) pro nastavení souboru cookie s tokenem žádosti:</span><span class="sxs-lookup"><span data-stu-id="315ee-496">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="315ee-497">`DisableFormValueModelBindingAttribute`Slouží k zakázání vazby modelu:</span><span class="sxs-lookup"><span data-stu-id="315ee-497">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="315ee-498">V ukázkové aplikaci a používá `GenerateAntiforgeryTokenCookieAttribute` se `DisableFormValueModelBindingAttribute` jako filtr pro modelové aplikace stránky `/StreamedSingleFileUploadDb` a `/StreamedSingleFileUploadPhysical` v `Startup.ConfigureServices` [ Razor konvencích použití stránek](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="315ee-498">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="315ee-499">Vzhledem k tomu, že vazba modelu nepřečte formulář, parametry, které jsou svázané z formuláře, se nezobrazují (budou pokračovat v práci s dotazem, trasou a hlavičkou).</span><span class="sxs-lookup"><span data-stu-id="315ee-499">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="315ee-500">Metoda Action pracuje přímo s `Request` vlastností.</span><span class="sxs-lookup"><span data-stu-id="315ee-500">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="315ee-501">`MultipartReader`Slouží ke čtení jednotlivých oddílů.</span><span class="sxs-lookup"><span data-stu-id="315ee-501">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="315ee-502">Data klíč/hodnota jsou uložena v `KeyValueAccumulator` .</span><span class="sxs-lookup"><span data-stu-id="315ee-502">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="315ee-503">Po načtení oddílů s více částmi se obsah `KeyValueAccumulator` používá pro svázání dat formuláře s typem modelu.</span><span class="sxs-lookup"><span data-stu-id="315ee-503">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="315ee-504">Úplná `StreamingController.UploadDatabase` metoda pro streamování do databáze s EF Core:</span><span class="sxs-lookup"><span data-stu-id="315ee-504">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="315ee-505">`MultipartRequestHelper`(*Nástroje/MultipartRequestHelper. cs*):</span><span class="sxs-lookup"><span data-stu-id="315ee-505">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="315ee-506">Úplná `StreamingController.UploadPhysical` metoda pro streamování do fyzického umístění:</span><span class="sxs-lookup"><span data-stu-id="315ee-506">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="315ee-507">V ukázkové aplikaci jsou kontroly ověřování zpracovávány nástrojem `FileHelpers.ProcessStreamedFile` .</span><span class="sxs-lookup"><span data-stu-id="315ee-507">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="315ee-508">Ověřování</span><span class="sxs-lookup"><span data-stu-id="315ee-508">Validation</span></span>

<span data-ttu-id="315ee-509">Třída ukázkové aplikace `FileHelpers` ukazuje několik kontrol ukládání souborů do vyrovnávací paměti <xref:Microsoft.AspNetCore.Http.IFormFile> a datových proudů při nahrávání.</span><span class="sxs-lookup"><span data-stu-id="315ee-509">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="315ee-510">Informace o zpracování <xref:Microsoft.AspNetCore.Http.IFormFile> ukládání souborů do vyrovnávací paměti v ukázkové aplikaci naleznete v `ProcessFormFile` metodě v souboru *Utilities/Helper. cs* .</span><span class="sxs-lookup"><span data-stu-id="315ee-510">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="315ee-511">Pro zpracování streamované soubory se podívejte na `ProcessStreamedFile` metodu ve stejném souboru.</span><span class="sxs-lookup"><span data-stu-id="315ee-511">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="315ee-512">Metody zpracování ověřování, které jsou znázorněné v ukázkové aplikaci, nekontrolují obsah nahraných souborů.</span><span class="sxs-lookup"><span data-stu-id="315ee-512">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="315ee-513">Ve většině produkčních scénářů se v souboru používá rozhraní API pro skenování virů nebo malwaru, než je soubor dostupný uživatelům nebo jiným systémům.</span><span class="sxs-lookup"><span data-stu-id="315ee-513">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="315ee-514">I když ukázka tématu poskytuje pracovní příklad technik ověřování, Neimplementujte `FileHelpers` třídu v produkční aplikaci, pokud:</span><span class="sxs-lookup"><span data-stu-id="315ee-514">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="315ee-515">Plně rozumíte implementaci.</span><span class="sxs-lookup"><span data-stu-id="315ee-515">Fully understand the implementation.</span></span>
> * <span data-ttu-id="315ee-516">Upravte implementaci podle potřeby pro prostředí a specifikace aplikace.</span><span class="sxs-lookup"><span data-stu-id="315ee-516">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="315ee-517">**Nikdy nepoužívejte nerozlišený kód zabezpečení v aplikaci bez nutnosti řešit tyto požadavky.**</span><span class="sxs-lookup"><span data-stu-id="315ee-517">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="315ee-518">Ověření obsahu</span><span class="sxs-lookup"><span data-stu-id="315ee-518">Content validation</span></span>

<span data-ttu-id="315ee-519">**Pro nahraný obsah použijte rozhraní API pro kontrolu virů a malwaru třetí strany.**</span><span class="sxs-lookup"><span data-stu-id="315ee-519">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="315ee-520">Prohledávání souborů je náročné na prostředky serveru ve scénářích s vysokým objemem.</span><span class="sxs-lookup"><span data-stu-id="315ee-520">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="315ee-521">Pokud dojde ke snížení výkonu zpracování požadavků z důvodu kontroly souborů, zvažte přesměrování práce skenování na službu na [pozadí](xref:fundamentals/host/hosted-services), případně služby spuštěné na serveru, který se liší od serveru aplikace.</span><span class="sxs-lookup"><span data-stu-id="315ee-521">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="315ee-522">Nahrané soubory se obvykle uchovávají v oblasti v karanténě, dokud je kontrola virů na pozadí nevrátí.</span><span class="sxs-lookup"><span data-stu-id="315ee-522">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="315ee-523">Když soubor projde, soubor se přesune do normálního umístění úložiště souborů.</span><span class="sxs-lookup"><span data-stu-id="315ee-523">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="315ee-524">Tyto kroky se obvykle provádí ve spojení s databázovým záznamem, který indikuje stav kontroly souboru.</span><span class="sxs-lookup"><span data-stu-id="315ee-524">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="315ee-525">Při použití takového přístupu zůstane aplikace a Server App zaměřené na reakci na požadavky.</span><span class="sxs-lookup"><span data-stu-id="315ee-525">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="315ee-526">Ověření přípony souboru</span><span class="sxs-lookup"><span data-stu-id="315ee-526">File extension validation</span></span>

<span data-ttu-id="315ee-527">Přípona nahraného souboru by měla být zaškrtnutá na seznamu povolených rozšíření.</span><span class="sxs-lookup"><span data-stu-id="315ee-527">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="315ee-528">Příklad:</span><span class="sxs-lookup"><span data-stu-id="315ee-528">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="315ee-529">Ověření podpisu souboru</span><span class="sxs-lookup"><span data-stu-id="315ee-529">File signature validation</span></span>

<span data-ttu-id="315ee-530">Podpis souboru se určuje na prvních několika bajtech na začátku souboru.</span><span class="sxs-lookup"><span data-stu-id="315ee-530">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="315ee-531">Tyto bajty lze použít k určení, zda přípona odpovídá obsahu souboru.</span><span class="sxs-lookup"><span data-stu-id="315ee-531">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="315ee-532">Ukázková aplikace zkontroluje podpisy souborů pro několik běžných typů souborů.</span><span class="sxs-lookup"><span data-stu-id="315ee-532">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="315ee-533">V následujícím příkladu se pro tento soubor kontroluje podpis souboru obrázku JPEG:</span><span class="sxs-lookup"><span data-stu-id="315ee-533">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="315ee-534">Další signatury souborů získáte v dokumentaci [signatury souborů](https://www.filesignatures.net/) a oficiálních souborů.</span><span class="sxs-lookup"><span data-stu-id="315ee-534">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="315ee-535">Zabezpečení názvu souboru</span><span class="sxs-lookup"><span data-stu-id="315ee-535">File name security</span></span>

<span data-ttu-id="315ee-536">Nikdy nepoužívejte název souboru dodaný klientem pro uložení souboru do fyzického úložiště.</span><span class="sxs-lookup"><span data-stu-id="315ee-536">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="315ee-537">Vytvořte bezpečný název souboru pro soubor pomocí [cesty. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) nebo [cesty. GetTempFileName](xref:System.IO.Path.GetTempFileName*) a vytvořte úplnou cestu (včetně názvu souboru) pro dočasné úložiště.</span><span class="sxs-lookup"><span data-stu-id="315ee-537">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

Razor<span data-ttu-id="315ee-538">Automatické kódování HTML kóduje hodnoty vlastností pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="315ee-538"> automatically HTML encodes property values for display.</span></span> <span data-ttu-id="315ee-539">Následující kód je bezpečné použít:</span><span class="sxs-lookup"><span data-stu-id="315ee-539">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="315ee-540">Mimo Razor , vždy <xref:System.Net.WebUtility.HtmlEncode*> obsah názvu souboru z požadavku uživatele.</span><span class="sxs-lookup"><span data-stu-id="315ee-540">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="315ee-541">Mnoho implementací musí zahrnovat kontrolu, že soubor existuje. v opačném případě je soubor přepsán souborem se stejným názvem.</span><span class="sxs-lookup"><span data-stu-id="315ee-541">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="315ee-542">Poskytněte další logiku pro splnění specifikací vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="315ee-542">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="315ee-543">Ověřování velikosti</span><span class="sxs-lookup"><span data-stu-id="315ee-543">Size validation</span></span>

<span data-ttu-id="315ee-544">Omezte velikost nahraných souborů.</span><span class="sxs-lookup"><span data-stu-id="315ee-544">Limit the size of uploaded files.</span></span>

<span data-ttu-id="315ee-545">V ukázkové aplikaci je velikost souboru omezená na 2 MB (uvedené v bajtech).</span><span class="sxs-lookup"><span data-stu-id="315ee-545">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="315ee-546">Limit je zadán prostřednictvím [Konfigurace](xref:fundamentals/configuration/index) ze souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="315ee-546">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="315ee-547">`FileSizeLimit`Třída je vložena do `PageModel` tříd:</span><span class="sxs-lookup"><span data-stu-id="315ee-547">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="315ee-548">Když velikost souboru překročí limit, soubor se odmítne:</span><span class="sxs-lookup"><span data-stu-id="315ee-548">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="315ee-549">Porovnává hodnotu atributu name s parametrem název metody POST</span><span class="sxs-lookup"><span data-stu-id="315ee-549">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="315ee-550">V Razor neformulářích, které publikují data formuláře nebo přímo využívají JavaScript `FormData` , název zadaný v prvku formuláře nebo `FormData` musí odpovídat názvu parametru v akci kontroleru.</span><span class="sxs-lookup"><span data-stu-id="315ee-550">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="315ee-551">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="315ee-551">In the following example:</span></span>

* <span data-ttu-id="315ee-552">Při použití `<input>` elementu `name` je atribut nastaven na hodnotu `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="315ee-552">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="315ee-553">Při použití `FormData` v jazyce JavaScript je název nastaven na hodnotu `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="315ee-553">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="315ee-554">Pro parametr metody jazyka C# () použijte stejný název `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="315ee-554">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="315ee-555">Pro Razor metodu obslužné rutiny stránky stránky s názvem `Upload` :</span><span class="sxs-lookup"><span data-stu-id="315ee-555">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="315ee-556">Pro metodu akce po kontroléru MVC:</span><span class="sxs-lookup"><span data-stu-id="315ee-556">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="315ee-557">Konfigurace serveru a aplikace</span><span class="sxs-lookup"><span data-stu-id="315ee-557">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="315ee-558">Omezení délky těla částí</span><span class="sxs-lookup"><span data-stu-id="315ee-558">Multipart body length limit</span></span>

<span data-ttu-id="315ee-559"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>nastaví limit délky jednotlivých částí části.</span><span class="sxs-lookup"><span data-stu-id="315ee-559"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="315ee-560">Oddíly formuláře, které překračují toto omezení, vyvolávají <xref:System.IO.InvalidDataException> při analýze.</span><span class="sxs-lookup"><span data-stu-id="315ee-560">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="315ee-561">Výchozí hodnota je 134 217 728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="315ee-561">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="315ee-562">Upravte limit pomocí <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> nastavení v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="315ee-562">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="315ee-563"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>slouží k nastavení <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> pro jednu stránku nebo akci.</span><span class="sxs-lookup"><span data-stu-id="315ee-563"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="315ee-564">V Razor aplikaci Pages použijte filtr s [konvencí](xref:razor-pages/razor-pages-conventions) v nástroji `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="315ee-564">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="315ee-565">V Razor aplikaci Pages nebo aplikaci MVC použijte filtr na model stránky nebo metodu akce:</span><span class="sxs-lookup"><span data-stu-id="315ee-565">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="315ee-566">Kestrel maximální velikost textu požadavku</span><span class="sxs-lookup"><span data-stu-id="315ee-566">Kestrel maximum request body size</span></span>

<span data-ttu-id="315ee-567">Pro aplikace hostované v Kestrel je výchozí maximální velikost textu požadavku 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="315ee-567">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="315ee-568">Přizpůsobte limit pomocí možnosti serveru [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:</span><span class="sxs-lookup"><span data-stu-id="315ee-568">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="315ee-569"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>slouží k nastavení [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) pro jednu stránku nebo akci.</span><span class="sxs-lookup"><span data-stu-id="315ee-569"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="315ee-570">V Razor aplikaci Pages použijte filtr s [konvencí](xref:razor-pages/razor-pages-conventions) v nástroji `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="315ee-570">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="315ee-571">V Razor aplikaci Pages nebo aplikaci MVC použijte filtr na třídu obslužné rutiny stránky nebo na metodu akce:</span><span class="sxs-lookup"><span data-stu-id="315ee-571">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="315ee-572">Další omezení Kestrel</span><span class="sxs-lookup"><span data-stu-id="315ee-572">Other Kestrel limits</span></span>

<span data-ttu-id="315ee-573">Pro aplikace hostované v Kestrel se můžou vztahovat další omezení Kestrel:</span><span class="sxs-lookup"><span data-stu-id="315ee-573">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="315ee-574">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="315ee-574">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="315ee-575">Sazby za data požadavků a odpovědí</span><span class="sxs-lookup"><span data-stu-id="315ee-575">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="315ee-576">Omezení délky obsahu služby IIS</span><span class="sxs-lookup"><span data-stu-id="315ee-576">IIS content length limit</span></span>

<span data-ttu-id="315ee-577">Výchozí limit počtu požadavků ( `maxAllowedContentLength` ) je 30 000 000 bajtů, což je přibližně 28.6 MB.</span><span class="sxs-lookup"><span data-stu-id="315ee-577">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="315ee-578">Upravte limit v souboru *Web. config* :</span><span class="sxs-lookup"><span data-stu-id="315ee-578">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="315ee-579">Toto nastavení platí pouze pro službu IIS.</span><span class="sxs-lookup"><span data-stu-id="315ee-579">This setting only applies to IIS.</span></span> <span data-ttu-id="315ee-580">K tomuto chování nedochází ve výchozím nastavení při hostování v Kestrel.</span><span class="sxs-lookup"><span data-stu-id="315ee-580">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="315ee-581">Další informace najdete v tématu [omezení požadavků \< requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="315ee-581">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="315ee-582">Omezení v modulu ASP.NET Core nebo přítomnosti modulu filtrování požadavků služby IIS mohou omezit nahrávání na 2 nebo 4 GB.</span><span class="sxs-lookup"><span data-stu-id="315ee-582">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="315ee-583">Další informace najdete v tématu [nelze odeslat soubor o velikosti větší než 2 GB (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span><span class="sxs-lookup"><span data-stu-id="315ee-583">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="315ee-584">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="315ee-584">Troubleshoot</span></span>

<span data-ttu-id="315ee-585">Níže jsou uvedeny některé běžné problémy, které se vyskytly při práci s nahráváním souborů a jejich možnými řešeními.</span><span class="sxs-lookup"><span data-stu-id="315ee-585">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="315ee-586">Při nasazení na server služby IIS se nenašla chyba.</span><span class="sxs-lookup"><span data-stu-id="315ee-586">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="315ee-587">Následující chyba znamená, že nahraný soubor překračuje délku nakonfigurovaného obsahu serveru:</span><span class="sxs-lookup"><span data-stu-id="315ee-587">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="315ee-588">Další informace o zvýšení limitu najdete v části [omezení délky obsahu služby IIS](#iis-content-length-limit) .</span><span class="sxs-lookup"><span data-stu-id="315ee-588">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="315ee-589">Chyba připojení</span><span class="sxs-lookup"><span data-stu-id="315ee-589">Connection failure</span></span>

<span data-ttu-id="315ee-590">Chyba připojení a připojení k serveru pro resetování pravděpodobně znamená, že nahraný soubor překračuje maximální velikost textu požadavku Kestrel.</span><span class="sxs-lookup"><span data-stu-id="315ee-590">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="315ee-591">Další informace najdete v části [Kestrel maximální velikost textu požadavku](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="315ee-591">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="315ee-592">Omezení připojení klientů Kestrel mohou také vyžadovat úpravu.</span><span class="sxs-lookup"><span data-stu-id="315ee-592">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="315ee-593">Výjimka odkazu s hodnotou null s IFormFile</span><span class="sxs-lookup"><span data-stu-id="315ee-593">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="315ee-594">Pokud kontroler přijímá odeslané soubory pomocí <xref:Microsoft.AspNetCore.Http.IFormFile> , ale hodnota je `null` , potvrďte, že formulář HTML určuje `enctype` hodnotu `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="315ee-594">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="315ee-595">Pokud tento atribut není nastaven na `<form>` elementu, odeslání souboru neproběhne a jakékoli vázané <xref:Microsoft.AspNetCore.Http.IFormFile> argumenty jsou `null` .</span><span class="sxs-lookup"><span data-stu-id="315ee-595">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="315ee-596">Ujistěte se také, že [nahrávání názvů v datech formuláře odpovídá pojmenování aplikace](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="315ee-596">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="315ee-597">Proud je příliš dlouhý.</span><span class="sxs-lookup"><span data-stu-id="315ee-597">Stream was too long</span></span>

<span data-ttu-id="315ee-598">Příklady v tomto tématu <xref:System.IO.MemoryStream> se spoléhají na uchovávání obsahu nahraného souboru.</span><span class="sxs-lookup"><span data-stu-id="315ee-598">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="315ee-599">Omezení velikosti `MemoryStream` je `int.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="315ee-599">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="315ee-600">Pokud scénář nahrávání souborů aplikace vyžaduje, aby byl obsah souboru větší než 50 MB, použijte alternativní přístup, který nespoléhá na jednu z nich `MemoryStream` pro uchovávání obsahu nahraného souboru.</span><span class="sxs-lookup"><span data-stu-id="315ee-600">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="315ee-601">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="315ee-601">Additional resources</span></span>

* [<span data-ttu-id="315ee-602">Vyprazdňování žádosti o připojení HTTP</span><span class="sxs-lookup"><span data-stu-id="315ee-602">HTTP connection request draining</span></span>](xref:fundamentals/servers/kestrel#http11-request-draining)
* [<span data-ttu-id="315ee-603">Neomezená nahrávání souboru</span><span class="sxs-lookup"><span data-stu-id="315ee-603">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
* [<span data-ttu-id="315ee-604">Zabezpečení Azure: rámec zabezpečení: ověření vstupu | Hrozeb</span><span class="sxs-lookup"><span data-stu-id="315ee-604">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="315ee-605">Vzory návrhu cloudu Azure: vzor osobního Key</span><span class="sxs-lookup"><span data-stu-id="315ee-605">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
