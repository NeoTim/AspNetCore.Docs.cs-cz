---
title: Autorizace na základě rolí v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak přidat kontroly deklarací identity pro autorizaci v aplikaci ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/claims
ms.openlocfilehash: 6b60ae5515819b017ab577f655ed91ee4d8ed0dd
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65086157"
---
# <a name="claims-based-authorization-in-aspnet-core"></a><span data-ttu-id="1d391-103">Autorizace na základě rolí v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1d391-103">Claims-based authorization in ASP.NET Core</span></span>

<a name="security-authorization-claims-based"></a>

<span data-ttu-id="1d391-104">Když se vytvoří identitu, která může přiřadit jeden nebo více deklarací identity vystavených důvěryhodná strana.</span><span class="sxs-lookup"><span data-stu-id="1d391-104">When an identity is created it may be assigned one or more claims issued by a trusted party.</span></span> <span data-ttu-id="1d391-105">Deklarace identity je, že je dvojice název-hodnota, která představuje jaké předmětu, můžete provést není co předmět.</span><span class="sxs-lookup"><span data-stu-id="1d391-105">A claim is a name value pair that represents what the subject is, not what the subject can do.</span></span> <span data-ttu-id="1d391-106">Například může mít ovladače licence vydané místní autorita dodávala licence.</span><span class="sxs-lookup"><span data-stu-id="1d391-106">For example, you may have a driver's license, issued by a local driving license authority.</span></span> <span data-ttu-id="1d391-107">Ve vašem ovladači licenci, má vaše datum narození.</span><span class="sxs-lookup"><span data-stu-id="1d391-107">Your driver's license has your date of birth on it.</span></span> <span data-ttu-id="1d391-108">V tomto případě bude název deklarace `DateOfBirth`, hodnota deklarace identity by například vaše datum narození, `8th June 1970` a vystavitele by dodávala licenční oprávnění.</span><span class="sxs-lookup"><span data-stu-id="1d391-108">In this case the claim name would be `DateOfBirth`, the claim value would be your date of birth, for example `8th June 1970` and the issuer would be the driving license authority.</span></span> <span data-ttu-id="1d391-109">Autorizace deklarovaných v nejjednodušším zkontroluje hodnotu vlastnosti deklarace identity a umožňuje přístup k prostředkům na základě této hodnoty.</span><span class="sxs-lookup"><span data-stu-id="1d391-109">Claims based authorization, at its simplest, checks the value of a claim and allows access to a resource based upon that value.</span></span> <span data-ttu-id="1d391-110">Příklad: Pokud chcete, aby přístup k club noční proces autorizace může být:</span><span class="sxs-lookup"><span data-stu-id="1d391-110">For example if you want access to a night club the authorization process might be:</span></span>

<span data-ttu-id="1d391-111">Dvířka knihovny bezpečnostního úředníka by vyhodnotit hodnotu vaše datum narození deklarace identity a určuje, zda důvěřují vystavitele (řízení licenční oprávnění) před udělením přístupu.</span><span class="sxs-lookup"><span data-stu-id="1d391-111">The door security officer would evaluate the value of your date of birth claim and whether they trust the issuer (the driving license authority) before granting you access.</span></span>

<span data-ttu-id="1d391-112">Identitu, která může obsahovat více deklarací identity s více hodnotami a může obsahovat více deklarací stejného typu.</span><span class="sxs-lookup"><span data-stu-id="1d391-112">An identity can contain multiple claims with multiple values and can contain multiple claims of the same type.</span></span>

## <a name="adding-claims-checks"></a><span data-ttu-id="1d391-113">Přidání deklarace identity kontroly</span><span class="sxs-lookup"><span data-stu-id="1d391-113">Adding claims checks</span></span>

<span data-ttu-id="1d391-114">Deklarace identity jsou deklarativní kontroly autorizace na základě – vývojář vloží je do jejich kód proti kontroler nebo akce v kontroleru, určení deklarace identity, která má aktuální uživatel musí mít, a volitelně musí obsahovat hodnotu deklarace identity pro přístup k požadovaný prostředek.</span><span class="sxs-lookup"><span data-stu-id="1d391-114">Claim based authorization checks are declarative - the developer embeds them within their code, against a controller or an action within a controller, specifying claims which the current user must possess, and optionally the value the claim must hold to access the requested resource.</span></span> <span data-ttu-id="1d391-115">Deklarace identity jsou požadavky na základě zásad, vývojář musí sestavení a registrací zásadu vyjádření požadavky deklarací identity.</span><span class="sxs-lookup"><span data-stu-id="1d391-115">Claims requirements are policy based, the developer must build and register a policy expressing the claims requirements.</span></span>

<span data-ttu-id="1d391-116">O nejjednodušší typ deklarace identity zásad hledá přítomnost deklarace identity a neprovádí kontrolu hodnoty.</span><span class="sxs-lookup"><span data-stu-id="1d391-116">The simplest type of claim policy looks for the presence of a claim and doesn't check the value.</span></span>

<span data-ttu-id="1d391-117">Je třeba nejprve pro sestavení a registrací zásady.</span><span class="sxs-lookup"><span data-stu-id="1d391-117">First you need to build and register the policy.</span></span> <span data-ttu-id="1d391-118">Tato akce se provede jako součást konfigurace autorizace služby, které obvykle trvá část v `ConfigureServices()` ve vašich *Startup.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="1d391-118">This takes place as part of the Authorization service configuration, which normally takes part in `ConfigureServices()` in your *Startup.cs* file.</span></span>

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

<span data-ttu-id="1d391-119">V tomto případě `EmployeeOnly` zásad kontroly na přítomnost `EmployeeNumber` na aktuální identitu deklarací identity.</span><span class="sxs-lookup"><span data-stu-id="1d391-119">In this case the `EmployeeOnly` policy checks for the presence of an `EmployeeNumber` claim on the current identity.</span></span>

<span data-ttu-id="1d391-120">Pak použijete pomocí zásad `Policy` vlastnost `AuthorizeAttribute` atribut zadejte název zásady;</span><span class="sxs-lookup"><span data-stu-id="1d391-120">You then apply the policy using the `Policy` property on the `AuthorizeAttribute` attribute to specify the policy name;</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult VacationBalance()
{
    return View();
}
```

<span data-ttu-id="1d391-121">`AuthorizeAttribute` Atribut můžete použít pro celý kontroler, v tomto případě jen identit odpovídající zásady bude jim povolen přístup k žádné akci na řadiči.</span><span class="sxs-lookup"><span data-stu-id="1d391-121">The `AuthorizeAttribute` attribute can be applied to an entire controller, in this instance only identities matching the policy will be allowed access to any Action on the controller.</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }
}
```

<span data-ttu-id="1d391-122">Pokud máte kontroler, který je chráněn `AuthorizeAttribute` atribut, ale chcete umožnit anonymní přístup k určité akce, které můžete použít `AllowAnonymousAttribute` atribut.</span><span class="sxs-lookup"><span data-stu-id="1d391-122">If you have a controller that's protected by the `AuthorizeAttribute` attribute, but want to allow anonymous access to particular actions you apply the `AllowAnonymousAttribute` attribute.</span></span>

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

<span data-ttu-id="1d391-123">Většina deklarací identity obsahuje hodnotu.</span><span class="sxs-lookup"><span data-stu-id="1d391-123">Most claims come with a value.</span></span> <span data-ttu-id="1d391-124">Seznam povolených hodnot můžete zadat při vytváření zásad.</span><span class="sxs-lookup"><span data-stu-id="1d391-124">You can specify a list of allowed values when creating the policy.</span></span> <span data-ttu-id="1d391-125">V následujícím příkladu by pouze uspěl pro zaměstnance jehož číslo bylo 1, 2, 3, 4 nebo 5.</span><span class="sxs-lookup"><span data-stu-id="1d391-125">The following example would only succeed for employees whose employee number was 1, 2, 3, 4 or 5.</span></span>

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

### <a name="add-a-generic-claim-check"></a><span data-ttu-id="1d391-126">Přidat kontrolu obecné deklarace identity</span><span class="sxs-lookup"><span data-stu-id="1d391-126">Add a generic claim check</span></span>

<span data-ttu-id="1d391-127">Pokud hodnota deklarace identity není jedinou hodnotu nebo transformace je povinný, použijte [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion).</span><span class="sxs-lookup"><span data-stu-id="1d391-127">If the claim value isn't a single value or a transformation is required, use [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion).</span></span> <span data-ttu-id="1d391-128">Další informace najdete v tématu [pomocí funkci func pro splnění zásad](xref:security/authorization/policies#using-a-func-to-fulfill-a-policy).</span><span class="sxs-lookup"><span data-stu-id="1d391-128">For more information, see [Using a func to fulfill a policy](xref:security/authorization/policies#using-a-func-to-fulfill-a-policy).</span></span>

## <a name="multiple-policy-evaluation"></a><span data-ttu-id="1d391-129">Vícenásobné vyhodnocení zásad</span><span class="sxs-lookup"><span data-stu-id="1d391-129">Multiple Policy Evaluation</span></span>

<span data-ttu-id="1d391-130">Pokud použijete víc zásad na kontroler nebo akce, musíte předat všechny zásady, před udělením přístupu.</span><span class="sxs-lookup"><span data-stu-id="1d391-130">If you apply multiple policies to a controller or action, then all policies must pass before access is granted.</span></span> <span data-ttu-id="1d391-131">Příklad:</span><span class="sxs-lookup"><span data-stu-id="1d391-131">For example:</span></span>

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

<span data-ttu-id="1d391-132">V předchozím příkladu všechny identity, který splňuje požadavky `EmployeeOnly` zásad se dostanete `Payslip` akci, jako tyto zásady se nevynutí na řadiči.</span><span class="sxs-lookup"><span data-stu-id="1d391-132">In the above example any identity which fulfills the `EmployeeOnly` policy can access the `Payslip` action as that policy is enforced on the controller.</span></span> <span data-ttu-id="1d391-133">Ale pro volání `UpdateSalary` akce musí splňovat identitu *obě* `EmployeeOnly` zásad a `HumanResources` zásad.</span><span class="sxs-lookup"><span data-stu-id="1d391-133">However in order to call the `UpdateSalary` action the identity must fulfill *both* the `EmployeeOnly` policy and the `HumanResources` policy.</span></span>

<span data-ttu-id="1d391-134">Pokud chcete složitější zásady, jako je například trvá datum narození deklarace identity, výpočtu stáří z něj pak kontroluje stáří 21 nebo starší pak budete muset napsat [obslužné rutiny vlastních zásad](xref:security/authorization/policies).</span><span class="sxs-lookup"><span data-stu-id="1d391-134">If you want more complicated policies, such as taking a date of birth claim, calculating an age from it then checking the age is 21 or older then you need to write [custom policy handlers](xref:security/authorization/policies).</span></span>
