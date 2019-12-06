> [!WARNING]
> <span data-ttu-id="fc3c8-101">Z bezpečnostních důvodů je potřeba, abyste se přihlásíte k vazbě `GET` dat žádostí na vlastnosti modelu stránky.</span><span class="sxs-lookup"><span data-stu-id="fc3c8-101">For security reasons, you must opt in to binding `GET` request data to page model properties.</span></span> <span data-ttu-id="fc3c8-102">Před mapováním na vlastnosti ověřte vstup uživatele.</span><span class="sxs-lookup"><span data-stu-id="fc3c8-102">Verify user input before mapping it to properties.</span></span> <span data-ttu-id="fc3c8-103">Přizpůsobování `GET` vazby je užitečné při adresování scénářů, které spoléhají na řetězec dotazu nebo hodnoty tras.</span><span class="sxs-lookup"><span data-stu-id="fc3c8-103">Opting into `GET` binding is useful when addressing scenarios that rely on query string or route values.</span></span>
>
> <span data-ttu-id="fc3c8-104">Chcete-li vytvořit navázání vlastnosti u požadavků na `GET`, nastavte vlastnost `SupportsGet` atributu [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) na `true`:</span><span class="sxs-lookup"><span data-stu-id="fc3c8-104">To bind a property on `GET` requests, set the [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute's `SupportsGet` property to `true`:</span></span>
>
> ```csharp
> [BindProperty(SupportsGet = true)]
> ```
>
> <span data-ttu-id="fc3c8-105">Další informace najdete v tématu [ASP.NET Core Community rychlou schůzku: Bind on GET diskuze (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).</span><span class="sxs-lookup"><span data-stu-id="fc3c8-105">For more information, see [ASP.NET Core Community Standup: Bind on GET discussion (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).</span></span>
