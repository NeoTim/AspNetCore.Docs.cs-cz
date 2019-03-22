---
ms.openlocfilehash: a0c563f4c58350d069aa789c00081cfe999a2190
ms.sourcegitcommit: a1c43150ed46aa01572399e8aede50d4668745ca
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2019
ms.locfileid: "58327236"
---
<span data-ttu-id="9bd83-101">`<form method="post">` Prvek je [pomocné rutiny značky formuláře](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="9bd83-101">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="9bd83-102">Pomocná rutina značky formuláře se automaticky zahrne [antiforgery token](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="9bd83-102">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="9bd83-103">Modul generování uživatelského rozhraní vytvoří kód Razor pro každé pole v modelu (s výjimkou ID) podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="9bd83-103">The scaffolding engine creates Razor markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="9bd83-104">[Pomocných rutin značek ověření](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` a `<span asp-validation-for`) zobrazení chyb ověřování.</span><span class="sxs-lookup"><span data-stu-id="9bd83-104">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="9bd83-105">Ověření se budeme věnovat jednotlivě podrobněji dále v této sérii.</span><span class="sxs-lookup"><span data-stu-id="9bd83-105">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="9bd83-106">[Pomocné rutiny značky popisek](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generuje titulek popisek a `for` atribut pro `Title` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="9bd83-106">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="9bd83-107">[Pomocné rutiny značky vstup](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control" />`) používá [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) atributy a vytváří atributy HTML, které jsou potřeba pro architekturu jQuery ověření na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="9bd83-107">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control" />`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>
