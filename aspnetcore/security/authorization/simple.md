---
<span data-ttu-id="1dc89-101">title: jednoduché ověřování v ASP.NET Core autor: Rick-Anderson Popis: Naučte se používat atribut autorizovat k omezení přístupu k řadičům ASP.NET Core a akcím.</span><span class="sxs-lookup"><span data-stu-id="1dc89-101">title: Simple authorization in ASP.NET Core author: rick-anderson description: Learn how to use the Authorize attribute to restrict access to ASP.NET Core controllers and actions.</span></span>
<span data-ttu-id="1dc89-102">MS. Author: Riande MS. Date: 10/14/2016 No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1dc89-102">ms.author: riande ms.date: 10/14/2016 no-loc:</span></span>
- <span data-ttu-id="1dc89-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1dc89-103">'Blazor'</span></span>
- <span data-ttu-id="1dc89-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1dc89-104">'Identity'</span></span>
- <span data-ttu-id="1dc89-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1dc89-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="1dc89-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1dc89-106">'Razor'</span></span>
- <span data-ttu-id="1dc89-107">SignalRUID: Security/Authorization/Simple</span><span class="sxs-lookup"><span data-stu-id="1dc89-107">'SignalR' uid: security/authorization/simple</span></span>

---
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="1dc89-108">Jednoduché ověřování v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1dc89-108">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="1dc89-109">Autorizaci v ASP.NET Core řídí s <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> a jeho různými parametry.</span><span class="sxs-lookup"><span data-stu-id="1dc89-109">Authorization in ASP.NET Core is controlled with <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> and its various parameters.</span></span> <span data-ttu-id="1dc89-110">V nejjednodušším formuláři použití `[Authorize]` atributu na kontroler, akce nebo Razor stránku omezuje přístup k této součásti na libovolného ověřeného uživatele.</span><span class="sxs-lookup"><span data-stu-id="1dc89-110">In its simplest form, applying the `[Authorize]` attribute to a controller, action, or Razor Page, limits access to that component to any authenticated user.</span></span>

<span data-ttu-id="1dc89-111">Například následující kód omezuje přístup k `AccountController` libovolnému ověřenému uživateli.</span><span class="sxs-lookup"><span data-stu-id="1dc89-111">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

```csharp
[Authorize]
public class AccountController : Controller
{
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

<span data-ttu-id="1dc89-112">Pokud chcete použít autorizaci pro akci místo kontroleru, použijte `AuthorizeAttribute` atribut na vlastní akci:</span><span class="sxs-lookup"><span data-stu-id="1dc89-112">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

```csharp
public class AccountController : Controller
{
   public ActionResult Login()
   {
   }

   [Authorize]
   public ActionResult Logout()
   {
   }
}
```

<span data-ttu-id="1dc89-113">K této funkci mají přístup jenom ověření uživatelé `Logout` .</span><span class="sxs-lookup"><span data-stu-id="1dc89-113">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="1dc89-114">Atribut můžete použít také `AllowAnonymous` k povolení přístupu neověřeným uživatelům k jednotlivým akcím.</span><span class="sxs-lookup"><span data-stu-id="1dc89-114">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="1dc89-115">Například:</span><span class="sxs-lookup"><span data-stu-id="1dc89-115">For example:</span></span>

```csharp
[Authorize]
public class AccountController : Controller
{
    [AllowAnonymous]
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

<span data-ttu-id="1dc89-116">To umožní pouze ověřeným uživatelům v `AccountController` , s výjimkou `Login` akce, která je přístupná všem, bez ohledu na jejich ověřený nebo neověřený nebo anonymní stav.</span><span class="sxs-lookup"><span data-stu-id="1dc89-116">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="1dc89-117">`[AllowAnonymous]`obchází všechny příkazy autorizace.</span><span class="sxs-lookup"><span data-stu-id="1dc89-117">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="1dc89-118">Pokud zkombinujete `[AllowAnonymous]` a jakýkoliv `[Authorize]` atribut, `[Authorize]` atributy se ignorují.</span><span class="sxs-lookup"><span data-stu-id="1dc89-118">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="1dc89-119">Například pokud použijete `[AllowAnonymous]` na úrovni řadiče, všechny `[Authorize]` atributy na stejném kontroleru (nebo na jakékoli akci v ní) se ignorují.</span><span class="sxs-lookup"><span data-stu-id="1dc89-119">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>
