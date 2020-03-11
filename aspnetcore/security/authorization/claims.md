---
title: Ověřování na základě deklarací identity v ASP.NET Core
author: rick-anderson
description: Naučte se, jak do aplikace ASP.NET Core přidat kontroly deklarací identity pro autorizaci.
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/claims
ms.openlocfilehash: e289851aafcbc7e3b3f60ab9fbe4b182a78bdf8a
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78661803"
---
# <a name="claims-based-authorization-in-aspnet-core"></a><span data-ttu-id="b5be0-103">Ověřování na základě deklarací identity v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b5be0-103">Claims-based authorization in ASP.NET Core</span></span>

<a name="security-authorization-claims-based"></a>

<span data-ttu-id="b5be0-104">Při vytvoření identity může být přiřazena jedna nebo více deklarací identity vydaných důvěryhodnou stranou.</span><span class="sxs-lookup"><span data-stu-id="b5be0-104">When an identity is created it may be assigned one or more claims issued by a trusted party.</span></span> <span data-ttu-id="b5be0-105">Deklarace identity je dvojice název hodnota, která představuje, co je předmět, a ne co může předmět dělat.</span><span class="sxs-lookup"><span data-stu-id="b5be0-105">A claim is a name value pair that represents what the subject is, not what the subject can do.</span></span> <span data-ttu-id="b5be0-106">Můžete mít například licenci řidiče, kterou vystavila místní řídící autorita pro vydávání licencí.</span><span class="sxs-lookup"><span data-stu-id="b5be0-106">For example, you may have a driver's license, issued by a local driving license authority.</span></span> <span data-ttu-id="b5be0-107">Vaše licence k vašemu ovladači má datum narození.</span><span class="sxs-lookup"><span data-stu-id="b5be0-107">Your driver's license has your date of birth on it.</span></span> <span data-ttu-id="b5be0-108">V takovém případě by se název deklarace `DateOfBirth`, hodnota deklarace by byla vaše datum narození, například `8th June 1970` a vystavitelem bude řídící licenční autorita.</span><span class="sxs-lookup"><span data-stu-id="b5be0-108">In this case the claim name would be `DateOfBirth`, the claim value would be your date of birth, for example `8th June 1970` and the issuer would be the driving license authority.</span></span> <span data-ttu-id="b5be0-109">Autorizace založená na deklaracích, která je nejjednodušší, kontroluje hodnotu deklarace identity a umožňuje přístup k prostředku na základě této hodnoty.</span><span class="sxs-lookup"><span data-stu-id="b5be0-109">Claims based authorization, at its simplest, checks the value of a claim and allows access to a resource based upon that value.</span></span> <span data-ttu-id="b5be0-110">Například pokud chcete mít přístup k nočnímu klubu, může být proces autorizace:</span><span class="sxs-lookup"><span data-stu-id="b5be0-110">For example if you want access to a night club the authorization process might be:</span></span>

<span data-ttu-id="b5be0-111">Pracovník zabezpečení dveří vyhodnotí hodnotu vašeho data narození a bez ohledu na to, jestli důvěřuje vystaviteli (řídící licenční autorita) před udělením přístupu.</span><span class="sxs-lookup"><span data-stu-id="b5be0-111">The door security officer would evaluate the value of your date of birth claim and whether they trust the issuer (the driving license authority) before granting you access.</span></span>

<span data-ttu-id="b5be0-112">Identita může obsahovat více deklarací s více hodnotami a může obsahovat více deklarací stejného typu.</span><span class="sxs-lookup"><span data-stu-id="b5be0-112">An identity can contain multiple claims with multiple values and can contain multiple claims of the same type.</span></span>

## <a name="adding-claims-checks"></a><span data-ttu-id="b5be0-113">Přidání kontrol deklarací identity</span><span class="sxs-lookup"><span data-stu-id="b5be0-113">Adding claims checks</span></span>

<span data-ttu-id="b5be0-114">Kontroly autorizace na základě deklarace identity jsou deklarativní – vývojář je vloží do svého kódu, před kontroler nebo akci v rámci kontroleru, určí deklarace identity, které musí mít aktuální uživatel, a volitelně hodnotu, kterou musí deklarace identity uchovávat pro přístup k požadovaný prostředek.</span><span class="sxs-lookup"><span data-stu-id="b5be0-114">Claim based authorization checks are declarative - the developer embeds them within their code, against a controller or an action within a controller, specifying claims which the current user must possess, and optionally the value the claim must hold to access the requested resource.</span></span> <span data-ttu-id="b5be0-115">Požadavky na deklarace identity jsou založené na zásadách, vývojář musí sestavit a zaregistrovat zásady, které vyjadřují požadavky na deklarace identity.</span><span class="sxs-lookup"><span data-stu-id="b5be0-115">Claims requirements are policy based, the developer must build and register a policy expressing the claims requirements.</span></span>

<span data-ttu-id="b5be0-116">Nejjednodušší typ zásad deklarace identity vyhledá přítomnost deklarace identity a nekontroluje hodnotu.</span><span class="sxs-lookup"><span data-stu-id="b5be0-116">The simplest type of claim policy looks for the presence of a claim and doesn't check the value.</span></span>

<span data-ttu-id="b5be0-117">Nejdřív je potřeba tuto zásadu sestavit a zaregistrovat.</span><span class="sxs-lookup"><span data-stu-id="b5be0-117">First you need to build and register the policy.</span></span> <span data-ttu-id="b5be0-118">K tomu dochází v rámci konfigurace autorizační služby, která obvykle využívá `ConfigureServices()` v souboru *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="b5be0-118">This takes place as part of the Authorization service configuration, which normally takes part in `ConfigureServices()` in your *Startup.cs* file.</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
    });
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
    });
}
```

::: moniker-end

<span data-ttu-id="b5be0-119">V takovém případě zásada `EmployeeOnly` kontroluje přítomnost `EmployeeNumber` deklarace identity na aktuální identitě.</span><span class="sxs-lookup"><span data-stu-id="b5be0-119">In this case the `EmployeeOnly` policy checks for the presence of an `EmployeeNumber` claim on the current identity.</span></span>

<span data-ttu-id="b5be0-120">Potom zásadu použijete pomocí vlastnosti `Policy` v atributu `AuthorizeAttribute` k zadání názvu zásady;</span><span class="sxs-lookup"><span data-stu-id="b5be0-120">You then apply the policy using the `Policy` property on the `AuthorizeAttribute` attribute to specify the policy name;</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult VacationBalance()
{
    return View();
}
```

<span data-ttu-id="b5be0-121">Atribut `AuthorizeAttribute` lze použít pro celý kontroler, v této instanci budou mít přístup k jakékoli akci na řadiči povolené jenom identity, které odpovídají zásadám.</span><span class="sxs-lookup"><span data-stu-id="b5be0-121">The `AuthorizeAttribute` attribute can be applied to an entire controller, in this instance only identities matching the policy will be allowed access to any Action on the controller.</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }
}
```

<span data-ttu-id="b5be0-122">Máte-li kontroler, který je chráněn atributem `AuthorizeAttribute`, ale chcete anonymnímu přístupu udělit konkrétní akce, které použijete atribut `AllowAnonymousAttribute`.</span><span class="sxs-lookup"><span data-stu-id="b5be0-122">If you have a controller that's protected by the `AuthorizeAttribute` attribute, but want to allow anonymous access to particular actions you apply the `AllowAnonymousAttribute` attribute.</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }

    [AllowAnonymous]
    public ActionResult VacationPolicy()
    {
    }
}
```

<span data-ttu-id="b5be0-123">Většina deklarací přináší hodnotu.</span><span class="sxs-lookup"><span data-stu-id="b5be0-123">Most claims come with a value.</span></span> <span data-ttu-id="b5be0-124">Při vytváření zásad můžete zadat seznam povolených hodnot.</span><span class="sxs-lookup"><span data-stu-id="b5be0-124">You can specify a list of allowed values when creating the policy.</span></span> <span data-ttu-id="b5be0-125">Následující příklad by byl úspěšný jenom pro zaměstnance, jejichž číslo zaměstnance bylo 1, 2, 3, 4 nebo 5.</span><span class="sxs-lookup"><span data-stu-id="b5be0-125">The following example would only succeed for employees whose employee number was 1, 2, 3, 4 or 5.</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("Founders", policy =>
                          policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("Founders", policy =>
                          policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
}
```

::: moniker-end
### <a name="add-a-generic-claim-check"></a><span data-ttu-id="b5be0-126">Přidat obecnou kontrolu deklarací identity</span><span class="sxs-lookup"><span data-stu-id="b5be0-126">Add a generic claim check</span></span>

<span data-ttu-id="b5be0-127">Pokud hodnota deklarace identity není jediná hodnota, nebo je nutné transformovat, použijte [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion).</span><span class="sxs-lookup"><span data-stu-id="b5be0-127">If the claim value isn't a single value or a transformation is required, use [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion).</span></span> <span data-ttu-id="b5be0-128">Další informace najdete v tématu [použití funkce Func ke splnění zásad](xref:security/authorization/policies#using-a-func-to-fulfill-a-policy).</span><span class="sxs-lookup"><span data-stu-id="b5be0-128">For more information, see [Using a func to fulfill a policy](xref:security/authorization/policies#using-a-func-to-fulfill-a-policy).</span></span>

## <a name="multiple-policy-evaluation"></a><span data-ttu-id="b5be0-129">Vícenásobné vyhodnocení zásad</span><span class="sxs-lookup"><span data-stu-id="b5be0-129">Multiple Policy Evaluation</span></span>

<span data-ttu-id="b5be0-130">Pokud pro kontroler nebo akci použijete více zásad, musí všechny zásady před udělením přístupu projít.</span><span class="sxs-lookup"><span data-stu-id="b5be0-130">If you apply multiple policies to a controller or action, then all policies must pass before access is granted.</span></span> <span data-ttu-id="b5be0-131">Příklad:</span><span class="sxs-lookup"><span data-stu-id="b5be0-131">For example:</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class SalaryController : Controller
{
    public ActionResult Payslip()
    {
    }

    [Authorize(Policy = "HumanResources")]
    public ActionResult UpdateSalary()
    {
    }
}
```

<span data-ttu-id="b5be0-132">V předchozím příkladu má každá identita, která splňuje zásady `EmployeeOnly`, přístup k akci `Payslip`, protože tato zásada je na řadiči vynutila.</span><span class="sxs-lookup"><span data-stu-id="b5be0-132">In the above example any identity which fulfills the `EmployeeOnly` policy can access the `Payslip` action as that policy is enforced on the controller.</span></span> <span data-ttu-id="b5be0-133">Chcete-li však zavolat akci `UpdateSalary`, musí *Identita splňovat zásady* `EmployeeOnly` a zásady `HumanResources`.</span><span class="sxs-lookup"><span data-stu-id="b5be0-133">However in order to call the `UpdateSalary` action the identity must fulfill *both* the `EmployeeOnly` policy and the `HumanResources` policy.</span></span>

<span data-ttu-id="b5be0-134">Pokud chcete složitější zásady, jako je třeba vynechání data narození, vypočítání stáří z něj a kontrola stáří 21 nebo starší, budete muset napsat [vlastní obslužné rutiny zásad](xref:security/authorization/policies).</span><span class="sxs-lookup"><span data-stu-id="b5be0-134">If you want more complicated policies, such as taking a date of birth claim, calculating an age from it then checking the age is 21 or older then you need to write [custom policy handlers](xref:security/authorization/policies).</span></span>
