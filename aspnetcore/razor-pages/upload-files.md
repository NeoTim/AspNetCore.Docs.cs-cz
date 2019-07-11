---
title: Nahrání souborů do stránky v ASP.NET Core Razor
author: guardrex
description: Zjistěte, jak k nahrání souborů do stránky Razor v ASP.NET Core s využitím třídy FileUpload.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 11/10/2018
ms.custom: mvc, seodec18
uid: razor-pages/upload-files
ms.openlocfilehash: 14d10424951e8ec3c7909d001c6f86e5fcb45d26
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815034"
---
# <a name="upload-files-to-a-razor-page-in-aspnet-core"></a><span data-ttu-id="e6fc6-103">Nahrání souborů do stránky v ASP.NET Core Razor</span><span class="sxs-lookup"><span data-stu-id="e6fc6-103">Upload files to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="e6fc6-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e6fc6-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e6fc6-105">Toto téma staví na [ukázkovou aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample) v <xref:tutorials/razor-pages/razor-pages-start>.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-105">This topic builds upon the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample) in <xref:tutorials/razor-pages/razor-pages-start>.</span></span>

<span data-ttu-id="e6fc6-106">Toto téma ukazuje, jak použít jednoduchý model vazby k nahrání souborů, což funguje dobře pro nahrávání malých souborů.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-106">This topic shows how to use simple model binding to upload files, which works well for uploading small files.</span></span> <span data-ttu-id="e6fc6-107">Informace o datových proudů velkých souborů, najdete v části [nahrávání velkých souborů pomocí streamování](xref:mvc/models/file-uploads#uploading-large-files-with-streaming).</span><span class="sxs-lookup"><span data-stu-id="e6fc6-107">For information on streaming large files, see [Uploading large files with streaming](xref:mvc/models/file-uploads#uploading-large-files-with-streaming).</span></span>

<span data-ttu-id="e6fc6-108">V následujících krocích se funkci odesílání souborů plán video přidá do ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-108">In the following steps, a movie schedule file upload feature is added to the sample app.</span></span> <span data-ttu-id="e6fc6-109">Plán video představuje `Schedule` třídy.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-109">A movie schedule is represented by a `Schedule` class.</span></span> <span data-ttu-id="e6fc6-110">Třída zahrnuje dvě verze plánu.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-110">The class includes two versions of the schedule.</span></span> <span data-ttu-id="e6fc6-111">Jedna verze je poskytováno zákazníkům, `PublicSchedule`.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-111">One version is provided to customers, `PublicSchedule`.</span></span> <span data-ttu-id="e6fc6-112">Jiné verze se používá pro zaměstnance společnosti `PrivateSchedule`.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-112">The other version is used for company employees, `PrivateSchedule`.</span></span> <span data-ttu-id="e6fc6-113">Každá verze je odeslán jako samostatný soubor.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-113">Each version is uploaded as a separate file.</span></span> <span data-ttu-id="e6fc6-114">Tento kurz ukazuje, jak provést dvě nahrávání souborů ze stránky s jeden příspěvek na server.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-114">The tutorial demonstrates how to perform two file uploads from a page with a single POST to the server.</span></span>

<span data-ttu-id="e6fc6-115">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/upload-files/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e6fc6-115">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/upload-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="e6fc6-116">Aspekty zabezpečení</span><span class="sxs-lookup"><span data-stu-id="e6fc6-116">Security considerations</span></span>

<span data-ttu-id="e6fc6-117">Upozornění musí být provedeny, když zároveň uživatelům poskytují možnost k nahrání souborů do serveru.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-117">Caution must be taken when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="e6fc6-118">Útočníci se dá provádět [útok DoS](/windows-hardware/drivers/ifs/denial-of-service) a dalších útoků na systém.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-118">Attackers may execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) and other attacks on a system.</span></span> <span data-ttu-id="e6fc6-119">Některé kroky zabezpečení, které sníží pravděpodobnost úspěšného útoku, že jsou:</span><span class="sxs-lookup"><span data-stu-id="e6fc6-119">Some security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="e6fc6-120">Nahrání souborů do oblasti nahrávání souboru vyhrazené systému, takže je jednodušší a stanovit bezpečnostní opatření u odeslaný obsah.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-120">Upload files to a dedicated file upload area on the system, which makes it easier to impose security measures on uploaded content.</span></span> <span data-ttu-id="e6fc6-121">Když umožňující nahrávání souborů, ujistěte se, která oprávnění ke spouštění v umístění nahrávání jsou zakázána.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-121">When permitting file uploads, make sure that execute permissions are disabled on the upload location.</span></span>
* <span data-ttu-id="e6fc6-122">Použijte soubor bezpečný název určit aplikace, nikoli ze vstupu uživatele nebo název souboru uloženého souboru.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-122">Use a safe file name determined by the app, not from user input or the file name of the uploaded file.</span></span>
* <span data-ttu-id="e6fc6-123">Povolte pouze konkrétní sadu přípon souborů schválené.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-123">Only allow a specific set of approved file extensions.</span></span>
* <span data-ttu-id="e6fc6-124">Ověřte, že jsou provedeny kontroly na straně klienta na serveru.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-124">Verify client-side checks are performed on the server.</span></span> <span data-ttu-id="e6fc6-125">Je snadné pro obejití kontroly na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-125">Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="e6fc6-126">Zkontrolujte velikost nahrávání a zakázat nahrávání větší, než se očekávalo.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-126">Check the size of the upload and prevent larger uploads than expected.</span></span>
* <span data-ttu-id="e6fc6-127">Spusťte vyhledávání virů a malwaru na odeslaný obsah.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-127">Run a virus/malware scanner on uploaded content.</span></span>

> [!WARNING]
> <span data-ttu-id="e6fc6-128">Nahrává se škodlivý kód do systému je často prvním krokem při provádění kódu, který můžete:</span><span class="sxs-lookup"><span data-stu-id="e6fc6-128">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
> * <span data-ttu-id="e6fc6-129">Úplné převzetí systému.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-129">Completely takeover a system.</span></span>
> * <span data-ttu-id="e6fc6-130">Přetížení systému s výsledkem, který systém zcela selže.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-130">Overload a system with the result that the system completely fails.</span></span>
> * <span data-ttu-id="e6fc6-131">Ohrozit data uživatele nebo systému.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-131">Compromise user or system data.</span></span>
> * <span data-ttu-id="e6fc6-132">Graffiti platí pro veřejné rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-132">Apply graffiti to a public interface.</span></span>

## <a name="add-a-fileupload-class"></a><span data-ttu-id="e6fc6-133">Přidejte třídu FileUpload</span><span class="sxs-lookup"><span data-stu-id="e6fc6-133">Add a FileUpload class</span></span>

<span data-ttu-id="e6fc6-134">Vytvoření stránky Razor pro zpracování pár nahrávání souborů.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-134">Create a Razor Page to handle a pair of file uploads.</span></span> <span data-ttu-id="e6fc6-135">Přidat `FileUpload` třídy, který je vázán na stránce získat data plánu.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-135">Add a `FileUpload` class, which is bound to the page to obtain the schedule data.</span></span> <span data-ttu-id="e6fc6-136">Klikněte pravým tlačítkem myši *modely* složky.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-136">Right click the *Models* folder.</span></span> <span data-ttu-id="e6fc6-137">Vyberte **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-137">Select **Add** > **Class**.</span></span> <span data-ttu-id="e6fc6-138">Název třídy **FileUpload** a přidejte následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="e6fc6-138">Name the class **FileUpload** and add the following properties:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Models/FileUpload.cs)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Models/FileUpload.cs)]

::: moniker-end

<span data-ttu-id="e6fc6-139">Třída nemá vlastnost pro název plánu a vlastnost pro každý dvě verze plán.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-139">The class has a property for the schedule's title and a property for each of the two versions of the schedule.</span></span> <span data-ttu-id="e6fc6-140">Všechny tři vlastnosti jsou povinné a název musí být dlouhý 3 až 60 znaků.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-140">All three properties are required, and the title must be 3-60 characters long.</span></span>

## <a name="add-a-helper-method-to-upload-files"></a><span data-ttu-id="e6fc6-141">Přidejte pomocnou metodu k nahrání souborů</span><span class="sxs-lookup"><span data-stu-id="e6fc6-141">Add a helper method to upload files</span></span>

<span data-ttu-id="e6fc6-142">Aby se zabránilo duplicitě kód pro zpracování souborů odeslané plán, je třeba nejprve přidáte statickou pomocnou metodu.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-142">To avoid code duplication for processing uploaded schedule files, add a static helper method first.</span></span> <span data-ttu-id="e6fc6-143">Vytvoření *nástroje* složky v aplikaci a přidejte *FileHelpers.cs* soubor s následujícím obsahem.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-143">Create a *Utilities* folder in the app and add a *FileHelpers.cs* file with the following content.</span></span> <span data-ttu-id="e6fc6-144">Pomocná metoda `ProcessFormFile`, přebírá [IFormFile](/dotnet/api/microsoft.aspnetcore.http.iformfile) a [ModelStateDictionary](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelstatedictionary) a vrátí řetězec obsahující velikosti a obsahu souboru.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-144">The helper method, `ProcessFormFile`, takes an [IFormFile](/dotnet/api/microsoft.aspnetcore.http.iformfile) and [ModelStateDictionary](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelstatedictionary) and returns a string containing the file's size and content.</span></span> <span data-ttu-id="e6fc6-145">Typ obsahu a délka jsou kontrolovány.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-145">The content type and length are checked.</span></span> <span data-ttu-id="e6fc6-146">Pokud soubor není úspěšný ověření, chyba je přidána do `ModelState`.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-146">If the file doesn't pass a validation check, an error is added to the `ModelState`.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Utilities/FileHelpers.cs)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Utilities/FileHelpers.cs)]

::: moniker-end

### <a name="save-the-file-to-disk"></a><span data-ttu-id="e6fc6-147">Uložte soubor na disk</span><span class="sxs-lookup"><span data-stu-id="e6fc6-147">Save the file to disk</span></span>

<span data-ttu-id="e6fc6-148">Ukázková aplikace ukládá nahraných souborů do pole databáze.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-148">The sample app saves uploaded files into database fields.</span></span> <span data-ttu-id="e6fc6-149">Chcete-li uložit soubor na disk, použijte [FileStream](/dotnet/api/system.io.filestream).</span><span class="sxs-lookup"><span data-stu-id="e6fc6-149">To save a file to disk, use a [FileStream](/dotnet/api/system.io.filestream).</span></span> <span data-ttu-id="e6fc6-150">V následujícím příkladu se zkopíruje soubor ukládaná společností `FileUpload.UploadPublicSchedule` k `FileStream` v `OnPostAsync` metoda.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-150">The following example copies a file held by `FileUpload.UploadPublicSchedule` to a `FileStream` in an `OnPostAsync` method.</span></span> <span data-ttu-id="e6fc6-151">`FileStream` Zapíše soubor na disk na `<PATH-AND-FILE-NAME>` k dispozici:</span><span class="sxs-lookup"><span data-stu-id="e6fc6-151">The `FileStream` writes the file to disk at the `<PATH-AND-FILE-NAME>` provided:</span></span>

```csharp
public async Task<IActionResult> OnPostAsync()
{
    // Perform an initial check to catch FileUpload class attribute violations.
    if (!ModelState.IsValid)
    {
        return Page();
    }

    var filePath = "<PATH-AND-FILE-NAME>";

    using (var fileStream = new FileStream(filePath, FileMode.Create))
    {
        await FileUpload.UploadPublicSchedule.CopyToAsync(fileStream);
    }

    return RedirectToPage("./Index");
}
```

<span data-ttu-id="e6fc6-152">Pracovní proces musí mít oprávnění k zápisu do umístění určeného proměnnou `filePath`.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-152">The worker process must have write permissions to the location specified by `filePath`.</span></span>

> [!NOTE]
> <span data-ttu-id="e6fc6-153">`filePath` *Musí* zahrnovat název souboru.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-153">The `filePath` *must* include the file name.</span></span> <span data-ttu-id="e6fc6-154">Pokud není zadaný název souboru, [UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception) je vyvolána výjimka za běhu.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-154">If the file name isn't provided, an [UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception) is thrown at runtime.</span></span>

> [!WARNING]
> <span data-ttu-id="e6fc6-155">Nikdy neměli zachovat nahrané soubory ve stromové struktuře stejného adresáře jako aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-155">Never persist uploaded files in the same directory tree as the app.</span></span>
>
> <span data-ttu-id="e6fc6-156">Vzorový kód poskytuje žádné serverové ochranu proti nahrávání škodlivých souborů.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-156">The code sample provides no server-side protection against malicious file uploads.</span></span> <span data-ttu-id="e6fc6-157">Informace o omezení možností útoku, při přijetí soubory od uživatelů najdete v následujících zdrojích:</span><span class="sxs-lookup"><span data-stu-id="e6fc6-157">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="e6fc6-158">Nahrání souboru bez omezení</span><span class="sxs-lookup"><span data-stu-id="e6fc6-158">Unrestricted File Upload</span></span>](https://www.owasp.org/index.php/Unrestricted_File_Upload)
> * [<span data-ttu-id="e6fc6-159">Zabezpečení Azure: Ujistěte se, že odpovídající ovládací prvky jsou na místě při přijetí soubory od uživatelů</span><span class="sxs-lookup"><span data-stu-id="e6fc6-159">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

### <a name="save-the-file-to-azure-blob-storage"></a><span data-ttu-id="e6fc6-160">Uložte soubor do úložiště objektů Blob v Azure</span><span class="sxs-lookup"><span data-stu-id="e6fc6-160">Save the file to Azure Blob Storage</span></span>

<span data-ttu-id="e6fc6-161">Nahrát obsah souboru do úložiště objektů Blob v Azure, najdete v článku [Začínáme s Azure Blob Storage pomocí .NET](/azure/storage/blobs/storage-dotnet-how-to-use-blobs).</span><span class="sxs-lookup"><span data-stu-id="e6fc6-161">To upload file content to Azure Blob Storage, see [Get started with Azure Blob Storage using .NET](/azure/storage/blobs/storage-dotnet-how-to-use-blobs).</span></span> <span data-ttu-id="e6fc6-162">Téma ukazuje, jak používat [UploadFromStream](/dotnet/api/microsoft.azure.storage.file.cloudfile.uploadfromstreamasync) uložte [FileStream](/dotnet/api/system.io.filestream) do úložiště objektů blob.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-162">The topic demonstrates how to use [UploadFromStream](/dotnet/api/microsoft.azure.storage.file.cloudfile.uploadfromstreamasync) to save a [FileStream](/dotnet/api/system.io.filestream) to blob storage.</span></span>

## <a name="add-the-schedule-class"></a><span data-ttu-id="e6fc6-163">Přidat třídu plán</span><span class="sxs-lookup"><span data-stu-id="e6fc6-163">Add the Schedule class</span></span>

<span data-ttu-id="e6fc6-164">Klikněte pravým tlačítkem myši *modely* složky.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-164">Right click the *Models* folder.</span></span> <span data-ttu-id="e6fc6-165">Vyberte **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-165">Select **Add** > **Class**.</span></span> <span data-ttu-id="e6fc6-166">Název třídy **plán** a přidejte následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="e6fc6-166">Name the class **Schedule** and add the following properties:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Models/Schedule.cs)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Models/Schedule.cs)]

::: moniker-end

<span data-ttu-id="e6fc6-167">Tato třída používá `Display` a `DisplayFormat` atributy, které vytvářejí popisné názvy a formátování při vykreslování dat plánu.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-167">The class uses `Display` and `DisplayFormat` attributes, which produce friendly titles and formatting when the schedule data is rendered.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="update-the-razorpagesmoviecontext"></a><span data-ttu-id="e6fc6-168">Aktualizace RazorPagesMovieContext</span><span class="sxs-lookup"><span data-stu-id="e6fc6-168">Update the RazorPagesMovieContext</span></span>

<span data-ttu-id="e6fc6-169">Zadejte `DbSet` v `RazorPagesMovieContext` (*Data/RazorPagesMovieContext.cs*) pro plány:</span><span class="sxs-lookup"><span data-stu-id="e6fc6-169">Specify a `DbSet` in the `RazorPagesMovieContext` (*Data/RazorPagesMovieContext.cs*) for the schedules:</span></span>

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Data/RazorPagesMovieContext.cs?highlight=17)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

## <a name="update-the-moviecontext"></a><span data-ttu-id="e6fc6-170">Aktualizace MovieContext</span><span class="sxs-lookup"><span data-stu-id="e6fc6-170">Update the MovieContext</span></span>

<span data-ttu-id="e6fc6-171">Zadejte `DbSet` v `MovieContext` (*Models/MovieContext.cs*) pro plány:</span><span class="sxs-lookup"><span data-stu-id="e6fc6-171">Specify a `DbSet` in the `MovieContext` (*Models/MovieContext.cs*) for the schedules:</span></span>

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Models/MovieContext.cs?highlight=13)]

::: moniker-end

## <a name="add-the-schedule-table-to-the-database"></a><span data-ttu-id="e6fc6-172">Přidat plán tabulku do databáze</span><span class="sxs-lookup"><span data-stu-id="e6fc6-172">Add the Schedule table to the database</span></span>

<span data-ttu-id="e6fc6-173">Otevřete konzoly Správce balíčků (PMC): **Nástroje** > **Správce balíčků NuGet** > **Konzola správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-173">Open the Package Manger Console (PMC): **Tools** > **NuGet Package Manager** > **Package Manager Console**.</span></span>

![PMC nabídky](upload-files/_static/pmc.png)

<span data-ttu-id="e6fc6-175">V konzole PMC spusťte následující příkazy.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-175">In the PMC, execute the following commands.</span></span> <span data-ttu-id="e6fc6-176">Přidat tyto příkazy `Schedule` tabulky v databázi:</span><span class="sxs-lookup"><span data-stu-id="e6fc6-176">These commands add a `Schedule` table to the database:</span></span>

```powershell
Add-Migration AddScheduleTable
Update-Database
```

## <a name="add-a-file-upload-razor-page"></a><span data-ttu-id="e6fc6-177">Přidat stránky Razor odesílání souborů</span><span class="sxs-lookup"><span data-stu-id="e6fc6-177">Add a file upload Razor Page</span></span>

<span data-ttu-id="e6fc6-178">V *stránky* složku, vytvořte *plány* složky.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-178">In the *Pages* folder, create a *Schedules* folder.</span></span> <span data-ttu-id="e6fc6-179">V *plány* složku, vytvořte stránku s názvem *Index.cshtml* pro nahrávání plánu s následujícím obsahem:</span><span class="sxs-lookup"><span data-stu-id="e6fc6-179">In the *Schedules* folder, create a page named *Index.cshtml* for uploading a schedule with the following content:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-cshtml[](upload-files/samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-cshtml[](upload-files/samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml)]

::: moniker-end

<span data-ttu-id="e6fc6-180">Každý formulář skupinou  **\<popisek >** , který zobrazí název každé vlastnosti třídy.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-180">Each form group includes a **\<label>** that displays the name of each class property.</span></span> <span data-ttu-id="e6fc6-181">`Display` Atributů `FileUpload` nabízí model zobrazované hodnoty popisků.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-181">The `Display` attributes in the `FileUpload` model provide the display values for the labels.</span></span> <span data-ttu-id="e6fc6-182">Například `UploadPublicSchedule` zobrazovaného názvu vlastnosti se nastaví pomocí `[Display(Name="Public Schedule")]` a proto zobrazí "Veřejný plán" v popisku při vykreslení formuláři.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-182">For example, the `UploadPublicSchedule` property's display name is set with `[Display(Name="Public Schedule")]` and thus displays "Public Schedule" in the label when the form renders.</span></span>

<span data-ttu-id="e6fc6-183">Každá skupina formuláře zahrnuje ověření  **\<span >** .</span><span class="sxs-lookup"><span data-stu-id="e6fc6-183">Each form group includes a validation **\<span>**.</span></span> <span data-ttu-id="e6fc6-184">Uživatelský vstup nesplňuje-li nastavit atributy vlastnosti `FileUpload` třídy nebo zda má některý `ProcessFormFile` metoda soubor ověřování selže, modelu se nepodařilo ověřit.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-184">If the user's input fails to meet the property attributes set in the `FileUpload` class or if any of the `ProcessFormFile` method file validation checks fail, the model fails to validate.</span></span> <span data-ttu-id="e6fc6-185">Pokud selže ověření modelu se vykreslí užitečné ověřovací zprávu pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-185">When model validation fails, a helpful validation message is rendered to the user.</span></span> <span data-ttu-id="e6fc6-186">Například `Title` vlastnost je opatřen poznámkou `[Required]` a `[StringLength(60, MinimumLength = 3)]`.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-186">For example, the `Title` property is annotated with `[Required]` and `[StringLength(60, MinimumLength = 3)]`.</span></span> <span data-ttu-id="e6fc6-187">Pokud se uživateli nepodaří zadat název, obdrží zprávu s oznámením, že je vyžadována hodnota.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-187">If the user fails to supply a title, they receive a message indicating that a value is required.</span></span> <span data-ttu-id="e6fc6-188">Pokud uživatel zadá hodnotu menší než tři znaky nebo více než 60 znaků, obdrží zprávu s oznámením, že hodnota má nesprávnou délku.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-188">If the user enters a value less than three characters or more than sixty characters, they receive a message indicating that the value has an incorrect length.</span></span> <span data-ttu-id="e6fc6-189">Pokud soubor uvedený, který nemá žádný obsah, zobrazí se zpráva označující, že soubor je prázdný.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-189">If a file is provided that has no content, a message appears indicating that the file is empty.</span></span>

## <a name="add-the-page-model"></a><span data-ttu-id="e6fc6-190">Přidání modelu stránky</span><span class="sxs-lookup"><span data-stu-id="e6fc6-190">Add the page model</span></span>

<span data-ttu-id="e6fc6-191">Přidat model stránky (*Index.cshtml.cs*) k *plány* složky:</span><span class="sxs-lookup"><span data-stu-id="e6fc6-191">Add the page model (*Index.cshtml.cs*) to the *Schedules* folder:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs)]

::: moniker-end

<span data-ttu-id="e6fc6-192">Model stránky (`IndexModel` v *Index.cshtml.cs*) vytvoří vazbu `FileUpload` třídy:</span><span class="sxs-lookup"><span data-stu-id="e6fc6-192">The page model (`IndexModel` in *Index.cshtml.cs*) binds the `FileUpload` class:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/snapshot_samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/snapshot_samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="e6fc6-193">Seznam plánů používá model (`IList<Schedule>`) Chcete-li zobrazit plány, které jsou uloženy v databázi na stránce:</span><span class="sxs-lookup"><span data-stu-id="e6fc6-193">The model also uses a list of the schedules (`IList<Schedule>`) to display the schedules stored in the database on the page:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/snapshot_samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet2)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/snapshot_samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet2)]

::: moniker-end

<span data-ttu-id="e6fc6-194">Při načtení stránky s `OnGetAsync`, `Schedules` je vyplněný z databáze a sloužící ke generování tabulku HTML načíst plánů:</span><span class="sxs-lookup"><span data-stu-id="e6fc6-194">When the page loads with `OnGetAsync`, `Schedules` is populated from the database and used to generate an HTML table of loaded schedules:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/snapshot_samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet3)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/snapshot_samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet3)]

::: moniker-end

<span data-ttu-id="e6fc6-195">Při odeslání formuláře k serveru, `ModelState` je zaškrtnuté políčko.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-195">When the form is posted to the server, the `ModelState` is checked.</span></span> <span data-ttu-id="e6fc6-196">Pokud není platný, `Schedule` znovu sestaví, a na stránce vykresluje se s jeden nebo více ověřovacích zpráv s informacemi o tom, proč se nezdařilo ověření stránky.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-196">If invalid, `Schedule` is rebuilt, and the page renders with one or more validation messages stating why page validation failed.</span></span> <span data-ttu-id="e6fc6-197">Pokud je platný, `FileUpload` vlastnosti jsou používány v *OnPostAsync* k dokončení nahrávání souboru pro dvě verze plán a vytvořit nový `Schedule` objekt pro uložení data.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-197">If valid, the `FileUpload` properties are used in *OnPostAsync* to complete the file upload for the two versions of the schedule and to create a new `Schedule` object to store the data.</span></span> <span data-ttu-id="e6fc6-198">Plán se pak uloží do databáze:</span><span class="sxs-lookup"><span data-stu-id="e6fc6-198">The schedule is then saved to the database:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/snapshot_samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet4)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/snapshot_samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet4)]

::: moniker-end

## <a name="link-the-file-upload-razor-page"></a><span data-ttu-id="e6fc6-199">Propojit samotné nahrávání souborů stránky Razor</span><span class="sxs-lookup"><span data-stu-id="e6fc6-199">Link the file upload Razor Page</span></span>

<span data-ttu-id="e6fc6-200">Otevřít *Pages/Shared/_Layout.cshtml* a přidat odkaz na navigačním panelu na stránce plány:</span><span class="sxs-lookup"><span data-stu-id="e6fc6-200">Open *Pages/Shared/_Layout.cshtml* and add a link to the navigation bar to reach the Schedules page:</span></span>

```cshtml
<div class="navbar-collapse collapse">
    <ul class="nav navbar-nav">
        <li><a asp-page="/Index">Home</a></li>
        <li><a asp-page="/Schedules/Index">Schedules</a></li>
        <li><a asp-page="/About">About</a></li>
        <li><a asp-page="/Contact">Contact</a></li>
    </ul>
</div>
```

## <a name="add-a-page-to-confirm-schedule-deletion"></a><span data-ttu-id="e6fc6-201">Přidat stránku pro potvrzení odstranění plánu</span><span class="sxs-lookup"><span data-stu-id="e6fc6-201">Add a page to confirm schedule deletion</span></span>

<span data-ttu-id="e6fc6-202">Když uživatel klikne na odstranění plánu, je k dispozici možnost zrušit operaci.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-202">When the user clicks to delete a schedule, a chance to cancel the operation is provided.</span></span> <span data-ttu-id="e6fc6-203">Přidat stránku potvrzení odstranění (*Delete.cshtml*) k *plány* složky:</span><span class="sxs-lookup"><span data-stu-id="e6fc6-203">Add a delete confirmation page (*Delete.cshtml*) to the *Schedules* folder:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-cshtml[](upload-files/samples/2.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-cshtml[](upload-files/samples/1.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml)]

::: moniker-end

<span data-ttu-id="e6fc6-204">Model stránky (*Delete.cshtml.cs*) načte jeden plán identifikovaný `id` v datech trasy žádost.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-204">The page model (*Delete.cshtml.cs*) loads a single schedule identified by `id` in the request's route data.</span></span> <span data-ttu-id="e6fc6-205">Přidat *Delete.cshtml.cs* do souboru *plány* složky:</span><span class="sxs-lookup"><span data-stu-id="e6fc6-205">Add the *Delete.cshtml.cs* file to the *Schedules* folder:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs)]

::: moniker-end

<span data-ttu-id="e6fc6-206">`OnPostAsync` Obsluhovala odstraňuje se plán, podle jeho `id`:</span><span class="sxs-lookup"><span data-stu-id="e6fc6-206">The `OnPostAsync` method handles deleting the schedule by its `id`:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/snapshot_samples/2.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs?name=snippet1&highlight=8,12-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/snapshot_samples/1.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs?name=snippet1&highlight=8,12-13)]

::: moniker-end

<span data-ttu-id="e6fc6-207">Po úspěšném odstranění plánu, `RedirectToPage` uživatel odešle zpět plány *Index.cshtml* stránky.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-207">After successfully deleting the schedule, the `RedirectToPage` sends the user back to the schedules *Index.cshtml* page.</span></span>

## <a name="the-working-schedules-razor-page"></a><span data-ttu-id="e6fc6-208">Funkční plány stránky Razor</span><span class="sxs-lookup"><span data-stu-id="e6fc6-208">The working Schedules Razor Page</span></span>

<span data-ttu-id="e6fc6-209">Když se stránka načte, popisků a vstupy pro název plánu, plán veřejné a privátní plánu jsou vykreslovány pomocí tlačítka Odeslat:</span><span class="sxs-lookup"><span data-stu-id="e6fc6-209">When the page loads, labels and inputs for schedule title, public schedule, and private schedule are rendered with a submit button:</span></span>

![Stránka Razor plány, jak je vidět na počátečním načtení bez chyb ověření a prázdná pole](upload-files/_static/browser1.png)

<span data-ttu-id="e6fc6-211">Výběr **nahrát** tlačítko bez naplnění libovolné pole je v rozporu `[Required]` atributů v modelu.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-211">Selecting the **Upload** button without populating any of the fields violates the `[Required]` attributes on the model.</span></span> <span data-ttu-id="e6fc6-212">`ModelState` Je neplatný.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-212">The `ModelState` is invalid.</span></span> <span data-ttu-id="e6fc6-213">Uživateli se zobrazí chybové zprávy ověření:</span><span class="sxs-lookup"><span data-stu-id="e6fc6-213">The validation error messages are displayed to the user:</span></span>

![Chybové zprávy ověření se zobrazí vedle každého vstupního ovládacího prvku](upload-files/_static/browser2.png)

<span data-ttu-id="e6fc6-215">Zadejte dvě písmena do **název** pole.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-215">Type two letters into the **Title** field.</span></span> <span data-ttu-id="e6fc6-216">Změny ověření zpráva označující, že název musí být dlouhý 3 až 60 znaků:</span><span class="sxs-lookup"><span data-stu-id="e6fc6-216">The validation message changes to indicate that the title must be between 3-60 characters:</span></span>

![Změnit název ověřovací zpráva](upload-files/_static/browser3.png)

<span data-ttu-id="e6fc6-218">Když se nahrají nejmíň jeden plán, **načíst plány** vykreslí část načíst plány:</span><span class="sxs-lookup"><span data-stu-id="e6fc6-218">When one or more schedules are uploaded, the **Loaded Schedules** section renders the loaded schedules:</span></span>

![Tabulka načíst plány, zobrazuje název každého plánu, nahrát data ve standardu UTC, veřejné verze souboru velikost a velikost souboru privátní verze](upload-files/_static/browser4.png)

<span data-ttu-id="e6fc6-220">Uživatel může kliknout **odstranit** odkaz z něj k dosažení zobrazení potvrzení odstranění, ve kterých budou mít možnost potvrdit nebo zrušit operaci odstranění zopakovat.</span><span class="sxs-lookup"><span data-stu-id="e6fc6-220">The user can click the **Delete** link from there to reach the delete confirmation view, where they have an opportunity to confirm or cancel the delete operation.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="e6fc6-221">Poradce při potížích</span><span class="sxs-lookup"><span data-stu-id="e6fc6-221">Troubleshooting</span></span>

<span data-ttu-id="e6fc6-222">Informace o odstraňování potíží `IFormFile` nahrát, najdete v článku [nahrání souborů v ASP.NET Core: Řešení potíží s](xref:mvc/models/file-uploads#troubleshooting).</span><span class="sxs-lookup"><span data-stu-id="e6fc6-222">For troubleshooting information with `IFormFile` uploading, see [File uploads in ASP.NET Core: Troubleshooting](xref:mvc/models/file-uploads#troubleshooting).</span></span>
