---
title: Ověřování na základě deklarací identity v ASP.NET Core
author: rick-anderson
description: Naučte se, jak do aplikace ASP.NET Core přidat kontroly deklarací identity pro autorizaci.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/claims
ms.openlocfilehash: 0615e9f13b0eca7d7ac924d90ae2004e41a51586
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632600"
---
# <a name="claims-based-authorization-in-aspnet-core"></a><span data-ttu-id="8ee3f-103">Ověřování na základě deklarací identity v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8ee3f-103">Claims-based authorization in ASP.NET Core</span></span>

<a name="security-authorization-claims-based"></a>

<span data-ttu-id="8ee3f-104">Při vytvoření identity může být přiřazena jedna nebo více deklarací identity vydaných důvěryhodnou stranou.</span><span class="sxs-lookup"><span data-stu-id="8ee3f-104">When an identity is created it may be assigned one or more claims issued by a trusted party.</span></span> <span data-ttu-id="8ee3f-105">Deklarace identity je dvojice název hodnota, která představuje, co je předmět, a ne co může předmět dělat.</span><span class="sxs-lookup"><span data-stu-id="8ee3f-105">A claim is a name value pair that represents what the subject is, not what the subject can do.</span></span> <span data-ttu-id="8ee3f-106">Můžete mít například licenci řidiče, kterou vystavila místní řídící autorita pro vydávání licencí.</span><span class="sxs-lookup"><span data-stu-id="8ee3f-106">For example, you may have a driver's license, issued by a local driving license authority.</span></span> <span data-ttu-id="8ee3f-107">Vaše licence k vašemu ovladači má datum narození.</span><span class="sxs-lookup"><span data-stu-id="8ee3f-107">Your driver's license has your date of birth on it.</span></span> <span data-ttu-id="8ee3f-108">V takovém případě bude název deklarace identity `DateOfBirth` , hodnota deklarace by byla vaše datum narození, například, `8th June 1970` a vystavitelem bude řídící licenční autorita.</span><span class="sxs-lookup"><span data-stu-id="8ee3f-108">In this case the claim name would be `DateOfBirth`, the claim value would be your date of birth, for example `8th June 1970` and the issuer would be the driving license authority.</span></span> <span data-ttu-id="8ee3f-109">Autorizace založená na deklaracích, která je nejjednodušší, kontroluje hodnotu deklarace identity a umožňuje přístup k prostředku na základě této hodnoty.</span><span class="sxs-lookup"><span data-stu-id="8ee3f-109">Claims based authorization, at its simplest, checks the value of a claim and allows access to a resource based upon that value.</span></span> <span data-ttu-id="8ee3f-110">Například pokud chcete mít přístup k nočnímu klubu, může být proces autorizace:</span><span class="sxs-lookup"><span data-stu-id="8ee3f-110">For example if you want access to a night club the authorization process might be:</span></span>

<span data-ttu-id="8ee3f-111">Pracovník zabezpečení dveří vyhodnotí hodnotu vašeho data narození a bez ohledu na to, jestli důvěřuje vystaviteli (řídící licenční autorita) před udělením přístupu.</span><span class="sxs-lookup"><span data-stu-id="8ee3f-111">The door security officer would evaluate the value of your date of birth claim and whether they trust the issuer (the driving license authority) before granting you access.</span></span>

<span data-ttu-id="8ee3f-112">Identita může obsahovat více deklarací s více hodnotami a může obsahovat více deklarací stejného typu.</span><span class="sxs-lookup"><span data-stu-id="8ee3f-112">An identity can contain multiple claims with multiple values and can contain multiple claims of the same type.</span></span>

## <a name="adding-claims-checks"></a><span data-ttu-id="8ee3f-113">Přidání kontrol deklarací identity</span><span class="sxs-lookup"><span data-stu-id="8ee3f-113">Adding claims checks</span></span>

<span data-ttu-id="8ee3f-114">Kontroly autorizace na základě deklarace identity jsou deklarativní – vývojář je vloží do svého kódu, před kontroler nebo akci v rámci kontroleru, určí deklarace identity, které musí mít aktuální uživatel, a volitelně hodnotu, kterou musí deklarace identity držet pro přístup k požadovanému prostředku.</span><span class="sxs-lookup"><span data-stu-id="8ee3f-114">Claim based authorization checks are declarative - the developer embeds them within their code, against a controller or an action within a controller, specifying claims which the current user must possess, and optionally the value the claim must hold to access the requested resource.</span></span> <span data-ttu-id="8ee3f-115">Požadavky na deklarace identity jsou založené na zásadách, vývojář musí sestavit a zaregistrovat zásady, které vyjadřují požadavky na deklarace identity.</span><span class="sxs-lookup"><span data-stu-id="8ee3f-115">Claims requirements are policy based, the developer must build and register a policy expressing the claims requirements.</span></span>

<span data-ttu-id="8ee3f-116">Nejjednodušší typ zásad deklarace identity vyhledá přítomnost deklarace identity a nekontroluje hodnotu.</span><span class="sxs-lookup"><span data-stu-id="8ee3f-116">The simplest type of claim policy looks for the presence of a claim and doesn't check the value.</span></span>

<span data-ttu-id="8ee3f-117">Nejdřív je potřeba tuto zásadu sestavit a zaregistrovat.</span><span class="sxs-lookup"><span data-stu-id="8ee3f-117">First you need to build and register the policy.</span></span> <span data-ttu-id="8ee3f-118">K tomu dochází v rámci konfigurace autorizační služby, která obvykle využívá součást v `ConfigureServices()` souboru *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="8ee3f-118">This takes place as part of the Authorization service configuration, which normally takes part in `ConfigureServices()` in your *Startup.cs* file.</span></span>

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

<span data-ttu-id="8ee3f-119">V takovém případě `EmployeeOnly` zásady kontrolují přítomnost `EmployeeNumber` deklarace identity na aktuální identitě.</span><span class="sxs-lookup"><span data-stu-id="8ee3f-119">In this case the `EmployeeOnly` policy checks for the presence of an `EmployeeNumber` claim on the current identity.</span></span>

<span data-ttu-id="8ee3f-120">Pak použijete zásadu pomocí `Policy` vlastnosti u `AuthorizeAttribute` atributu k zadání názvu zásady;</span><span class="sxs-lookup"><span data-stu-id="8ee3f-120">You then apply the policy using the `Policy` property on the `AuthorizeAttribute` attribute to specify the policy name;</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult VacationBalance()
{
    return View();
}
```

<span data-ttu-id="8ee3f-121">`AuthorizeAttribute`Atribut lze použít pro celý kontroler. v této instanci budou mít přístup k jakékoli akci na řadiči povolené jenom identity, které odpovídají zásadám.</span><span class="sxs-lookup"><span data-stu-id="8ee3f-121">The `AuthorizeAttribute` attribute can be applied to an entire controller, in this instance only identities matching the policy will be allowed access to any Action on the controller.</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }
}
```

<span data-ttu-id="8ee3f-122">Pokud máte kontroler, který je chráněný `AuthorizeAttribute` atributem, ale chcete, aby byl anonymní přístup povolen k určitým akcím, které použijete v `AllowAnonymousAttribute` atributu.</span><span class="sxs-lookup"><span data-stu-id="8ee3f-122">If you have a controller that's protected by the `AuthorizeAttribute` attribute, but want to allow anonymous access to particular actions you apply the `AllowAnonymousAttribute` attribute.</span></span>

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

<span data-ttu-id="8ee3f-123">Většina deklarací přináší hodnotu.</span><span class="sxs-lookup"><span data-stu-id="8ee3f-123">Most claims come with a value.</span></span> <span data-ttu-id="8ee3f-124">Při vytváření zásad můžete zadat seznam povolených hodnot.</span><span class="sxs-lookup"><span data-stu-id="8ee3f-124">You can specify a list of allowed values when creating the policy.</span></span> <span data-ttu-id="8ee3f-125">Následující příklad by byl úspěšný jenom pro zaměstnance, jejichž číslo zaměstnance bylo 1, 2, 3, 4 nebo 5.</span><span class="sxs-lookup"><span data-stu-id="8ee3f-125">The following example would only succeed for employees whose employee number was 1, 2, 3, 4 or 5.</span></span>

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
### <a name="add-a-generic-claim-check"></a><span data-ttu-id="8ee3f-126">Přidat obecnou kontrolu deklarací identity</span><span class="sxs-lookup"><span data-stu-id="8ee3f-126">Add a generic claim check</span></span>

<span data-ttu-id="8ee3f-127">Pokud hodnota deklarace identity není jediná hodnota, nebo je nutné transformovat, použijte [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion).</span><span class="sxs-lookup"><span data-stu-id="8ee3f-127">If the claim value isn't a single value or a transformation is required, use [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion).</span></span> <span data-ttu-id="8ee3f-128">Další informace najdete v tématu [použití funkce ke splnění zásad](xref:security/authorization/policies#use-a-func-to-fulfill-a-policy).</span><span class="sxs-lookup"><span data-stu-id="8ee3f-128">For more information, see [Use a func to fulfill a policy](xref:security/authorization/policies#use-a-func-to-fulfill-a-policy).</span></span>

## <a name="multiple-policy-evaluation"></a><span data-ttu-id="8ee3f-129">Vícenásobné vyhodnocení zásad</span><span class="sxs-lookup"><span data-stu-id="8ee3f-129">Multiple Policy Evaluation</span></span>

<span data-ttu-id="8ee3f-130">Pokud pro kontroler nebo akci použijete více zásad, musí všechny zásady před udělením přístupu projít.</span><span class="sxs-lookup"><span data-stu-id="8ee3f-130">If you apply multiple policies to a controller or action, then all policies must pass before access is granted.</span></span> <span data-ttu-id="8ee3f-131">Příklad:</span><span class="sxs-lookup"><span data-stu-id="8ee3f-131">For example:</span></span>

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

<span data-ttu-id="8ee3f-132">V předchozím příkladu bude mít `EmployeeOnly` přístup k `Payslip` akci, když se tato zásada na řadiči vynutila, na základě identity, která tyto zásady splňuje.</span><span class="sxs-lookup"><span data-stu-id="8ee3f-132">In the above example any identity which fulfills the `EmployeeOnly` policy can access the `Payslip` action as that policy is enforced on the controller.</span></span> <span data-ttu-id="8ee3f-133">Pro volání `UpdateSalary` akce musí identita ale *both* splňovat `EmployeeOnly` zásady a `HumanResources` zásady.</span><span class="sxs-lookup"><span data-stu-id="8ee3f-133">However in order to call the `UpdateSalary` action the identity must fulfill *both* the `EmployeeOnly` policy and the `HumanResources` policy.</span></span>

<span data-ttu-id="8ee3f-134">Pokud chcete složitější zásady, jako je třeba vynechání data narození, vypočítání stáří z něj a kontrola stáří 21 nebo starší, budete muset napsat [vlastní obslužné rutiny zásad](xref:security/authorization/policies).</span><span class="sxs-lookup"><span data-stu-id="8ee3f-134">If you want more complicated policies, such as taking a date of birth claim, calculating an age from it then checking the age is 21 or older then you need to write [custom policy handlers](xref:security/authorization/policies).</span></span>
