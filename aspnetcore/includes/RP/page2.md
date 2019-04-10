---
ms.openlocfilehash: 45fb8a8c0d6e98b002789a6fb7b2b0fce893ad63
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2019
ms.locfileid: "59468586"
---
<span data-ttu-id="f2e7a-101">`<form method="post">` Prvek je [pomocné rutiny značky formuláře](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="f2e7a-101">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="f2e7a-102">Pomocná rutina značky formuláře se automaticky zahrne [antiforgery token](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="f2e7a-102">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="f2e7a-103">Modul generování uživatelského rozhraní vytvoří kód Razor pro každé pole v modelu (s výjimkou ID) podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="f2e7a-103">The scaffolding engine creates Razor markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="f2e7a-104">[Pomocných rutin značek ověření](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` a `<span asp-validation-for`) zobrazení chyb ověřování.</span><span class="sxs-lookup"><span data-stu-id="f2e7a-104">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="f2e7a-105">Ověření se budeme věnovat jednotlivě podrobněji dále v této sérii.</span><span class="sxs-lookup"><span data-stu-id="f2e7a-105">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="f2e7a-106">[Pomocné rutiny značky popisek](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generuje titulek popisek a `for` atribut pro `Title` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="f2e7a-106">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="f2e7a-107">[Pomocné rutiny značky vstup](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) používá [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) atributy a vytváří atributy HTML, které jsou potřeba pro architekturu jQuery ověření na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="f2e7a-107">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>
