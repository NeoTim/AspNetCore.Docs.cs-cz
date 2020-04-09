> [!WARNING]
> <span data-ttu-id="2a352-101">Z bezpečnostních důvodů se musíte `GET` přihlásit k vazby dat požadavku na vlastnosti modelu stránky.</span><span class="sxs-lookup"><span data-stu-id="2a352-101">For security reasons, you must opt in to binding `GET` request data to page model properties.</span></span> <span data-ttu-id="2a352-102">Před mapováním na vlastnosti ověřte vstup uživatele.</span><span class="sxs-lookup"><span data-stu-id="2a352-102">Verify user input before mapping it to properties.</span></span> <span data-ttu-id="2a352-103">Přihlášení do `GET` vazby je užitečné při adresování scénářů, které spoléhají na řetězec dotazu nebo hodnoty postupu.</span><span class="sxs-lookup"><span data-stu-id="2a352-103">Opting into `GET` binding is useful when addressing scenarios that rely on query string or route values.</span></span>
>
> <span data-ttu-id="2a352-104">Chcete-li vytvořit `GET` vazbu vlastnost [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) na `SupportsGet` požadavky, `true`nastavte vlastnost atributu na :</span><span class="sxs-lookup"><span data-stu-id="2a352-104">To bind a property on `GET` requests, set the [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute's `SupportsGet` property to `true`:</span></span>
>
> ```csharp
> [BindProperty(SupportsGet = true)]
> ```
>
> <span data-ttu-id="2a352-105">Další informace naleznete [v tématu ASP.NET Základní komunitní standup: Vazba na diskuzi GET (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).</span><span class="sxs-lookup"><span data-stu-id="2a352-105">For more information, see [ASP.NET Core Community Standup: Bind on GET discussion (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).</span></span>
