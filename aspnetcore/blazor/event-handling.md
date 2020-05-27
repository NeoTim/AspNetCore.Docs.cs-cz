---
<span data-ttu-id="ba389-101">title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '</span><span class="sxs-lookup"><span data-stu-id="ba389-101">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="ba389-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ba389-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba389-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba389-103">'Blazor'</span></span>
- <span data-ttu-id="ba389-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba389-104">'Identity'</span></span>
- <span data-ttu-id="ba389-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba389-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba389-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba389-106">'Razor'</span></span>
- <span data-ttu-id="ba389-107">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ba389-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="ba389-108">BlazorZpracování událostí ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ba389-108">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="ba389-109">Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="ba389-109">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="ba389-110">komponenty poskytují funkce pro zpracování událostí.</span><span class="sxs-lookup"><span data-stu-id="ba389-110"> components provide event handling features.</span></span> <span data-ttu-id="ba389-111">Pro atribut elementu HTML s názvem [`@on{EVENT}`](xref:mvc/views/razor#onevent) (například `@onclick` ) s hodnotou delegovaný typ Razor Komponenta považuje hodnotu atributu za obslužnou rutinu události.</span><span class="sxs-lookup"><span data-stu-id="ba389-111">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="ba389-112">Následující kód volá metodu, `UpdateHeading` Pokud je vybráno tlačítko v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="ba389-112">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

<span data-ttu-id="ba389-113">Následující kód volá metodu, `CheckChanged` když je zaškrtávací políčko změněno v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="ba389-113">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="ba389-114">Obslužné rutiny událostí mohou být také asynchronní a vracet <xref:System.Threading.Tasks.Task> .</span><span class="sxs-lookup"><span data-stu-id="ba389-114">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="ba389-115">Není nutné ručně volat [StateHasChanged](xref:blazor/lifecycle#state-changes).</span><span class="sxs-lookup"><span data-stu-id="ba389-115">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="ba389-116">Výjimky jsou protokolovány, když k nim dojde.</span><span class="sxs-lookup"><span data-stu-id="ba389-116">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="ba389-117">V následujícím příkladu `UpdateHeading` se volá asynchronně po výběru tlačítka:</span><span class="sxs-lookup"><span data-stu-id="ba389-117">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

## <a name="event-argument-types"></a><span data-ttu-id="ba389-118">Typy argumentů události</span><span class="sxs-lookup"><span data-stu-id="ba389-118">Event argument types</span></span>

<span data-ttu-id="ba389-119">U některých událostí jsou povoleny typy argumentů události.</span><span class="sxs-lookup"><span data-stu-id="ba389-119">For some events, event argument types are permitted.</span></span> <span data-ttu-id="ba389-120">Zadání typu události ve volání metody je nezbytné pouze v případě, že je typ události použit v metodě.</span><span class="sxs-lookup"><span data-stu-id="ba389-120">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="ba389-121">Podporované <xref:System.EventArgs> jsou uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="ba389-121">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

| <span data-ttu-id="ba389-122">Událost</span><span class="sxs-lookup"><span data-stu-id="ba389-122">Event</span></span>            | <span data-ttu-id="ba389-123">Třída</span><span class="sxs-lookup"><span data-stu-id="ba389-123">Class</span></span>                | <span data-ttu-id="ba389-124">Události a poznámky modelu DOM</span><span class="sxs-lookup"><span data-stu-id="ba389-124">DOM events and notes</span></span> |
| ---
<span data-ttu-id="ba389-125">title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '</span><span class="sxs-lookup"><span data-stu-id="ba389-125">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="ba389-126">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ba389-126">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba389-127">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba389-127">'Blazor'</span></span>
- <span data-ttu-id="ba389-128">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba389-128">'Identity'</span></span>
- <span data-ttu-id="ba389-129">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba389-129">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba389-130">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba389-130">'Razor'</span></span>
- <span data-ttu-id="ba389-131">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ba389-131">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ba389-132">title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '</span><span class="sxs-lookup"><span data-stu-id="ba389-132">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="ba389-133">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ba389-133">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba389-134">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba389-134">'Blazor'</span></span>
- <span data-ttu-id="ba389-135">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba389-135">'Identity'</span></span>
- <span data-ttu-id="ba389-136">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba389-136">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba389-137">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba389-137">'Razor'</span></span>
- <span data-ttu-id="ba389-138">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ba389-138">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ba389-139">title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '</span><span class="sxs-lookup"><span data-stu-id="ba389-139">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="ba389-140">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ba389-140">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba389-141">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba389-141">'Blazor'</span></span>
- <span data-ttu-id="ba389-142">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba389-142">'Identity'</span></span>
- <span data-ttu-id="ba389-143">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba389-143">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba389-144">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba389-144">'Razor'</span></span>
- <span data-ttu-id="ba389-145">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ba389-145">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ba389-146">title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '</span><span class="sxs-lookup"><span data-stu-id="ba389-146">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="ba389-147">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ba389-147">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba389-148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba389-148">'Blazor'</span></span>
- <span data-ttu-id="ba389-149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba389-149">'Identity'</span></span>
- <span data-ttu-id="ba389-150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba389-150">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba389-151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba389-151">'Razor'</span></span>
- <span data-ttu-id="ba389-152">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ba389-152">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ba389-153">title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '</span><span class="sxs-lookup"><span data-stu-id="ba389-153">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="ba389-154">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ba389-154">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba389-155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba389-155">'Blazor'</span></span>
- <span data-ttu-id="ba389-156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba389-156">'Identity'</span></span>
- <span data-ttu-id="ba389-157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba389-157">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba389-158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba389-158">'Razor'</span></span>
- <span data-ttu-id="ba389-159">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ba389-159">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ba389-160">title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '</span><span class="sxs-lookup"><span data-stu-id="ba389-160">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="ba389-161">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ba389-161">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba389-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba389-162">'Blazor'</span></span>
- <span data-ttu-id="ba389-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba389-163">'Identity'</span></span>
- <span data-ttu-id="ba389-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba389-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba389-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba389-165">'Razor'</span></span>
- <span data-ttu-id="ba389-166">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ba389-166">'SignalR' uid:</span></span> 

<span data-ttu-id="ba389-167">-------- | ---Název: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '</span><span class="sxs-lookup"><span data-stu-id="ba389-167">-------- | --- title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="ba389-168">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ba389-168">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba389-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba389-169">'Blazor'</span></span>
- <span data-ttu-id="ba389-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba389-170">'Identity'</span></span>
- <span data-ttu-id="ba389-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba389-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba389-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba389-172">'Razor'</span></span>
- <span data-ttu-id="ba389-173">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ba389-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ba389-174">title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '</span><span class="sxs-lookup"><span data-stu-id="ba389-174">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="ba389-175">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ba389-175">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba389-176">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba389-176">'Blazor'</span></span>
- <span data-ttu-id="ba389-177">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba389-177">'Identity'</span></span>
- <span data-ttu-id="ba389-178">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba389-178">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba389-179">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba389-179">'Razor'</span></span>
- <span data-ttu-id="ba389-180">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ba389-180">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ba389-181">title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '</span><span class="sxs-lookup"><span data-stu-id="ba389-181">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="ba389-182">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ba389-182">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba389-183">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba389-183">'Blazor'</span></span>
- <span data-ttu-id="ba389-184">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba389-184">'Identity'</span></span>
- <span data-ttu-id="ba389-185">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba389-185">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba389-186">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba389-186">'Razor'</span></span>
- <span data-ttu-id="ba389-187">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ba389-187">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ba389-188">title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '</span><span class="sxs-lookup"><span data-stu-id="ba389-188">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="ba389-189">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ba389-189">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba389-190">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba389-190">'Blazor'</span></span>
- <span data-ttu-id="ba389-191">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba389-191">'Identity'</span></span>
- <span data-ttu-id="ba389-192">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba389-192">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba389-193">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba389-193">'Razor'</span></span>
- <span data-ttu-id="ba389-194">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ba389-194">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ba389-195">title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '</span><span class="sxs-lookup"><span data-stu-id="ba389-195">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="ba389-196">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ba389-196">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba389-197">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba389-197">'Blazor'</span></span>
- <span data-ttu-id="ba389-198">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba389-198">'Identity'</span></span>
- <span data-ttu-id="ba389-199">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba389-199">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba389-200">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba389-200">'Razor'</span></span>
- <span data-ttu-id="ba389-201">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ba389-201">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ba389-202">title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '</span><span class="sxs-lookup"><span data-stu-id="ba389-202">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="ba389-203">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ba389-203">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba389-204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba389-204">'Blazor'</span></span>
- <span data-ttu-id="ba389-205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba389-205">'Identity'</span></span>
- <span data-ttu-id="ba389-206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba389-206">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba389-207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba389-207">'Razor'</span></span>
- <span data-ttu-id="ba389-208">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ba389-208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ba389-209">title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '</span><span class="sxs-lookup"><span data-stu-id="ba389-209">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="ba389-210">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ba389-210">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba389-211">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba389-211">'Blazor'</span></span>
- <span data-ttu-id="ba389-212">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba389-212">'Identity'</span></span>
- <span data-ttu-id="ba389-213">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba389-213">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba389-214">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba389-214">'Razor'</span></span>
- <span data-ttu-id="ba389-215">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ba389-215">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ba389-216">title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '</span><span class="sxs-lookup"><span data-stu-id="ba389-216">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="ba389-217">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ba389-217">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba389-218">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba389-218">'Blazor'</span></span>
- <span data-ttu-id="ba389-219">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba389-219">'Identity'</span></span>
- <span data-ttu-id="ba389-220">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba389-220">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba389-221">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba389-221">'Razor'</span></span>
- <span data-ttu-id="ba389-222">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ba389-222">'SignalR' uid:</span></span> 

<span data-ttu-id="ba389-223">---------- | ---Název: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '</span><span class="sxs-lookup"><span data-stu-id="ba389-223">---------- | --- title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="ba389-224">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ba389-224">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba389-225">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba389-225">'Blazor'</span></span>
- <span data-ttu-id="ba389-226">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba389-226">'Identity'</span></span>
- <span data-ttu-id="ba389-227">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba389-227">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba389-228">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba389-228">'Razor'</span></span>
- <span data-ttu-id="ba389-229">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ba389-229">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ba389-230">title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '</span><span class="sxs-lookup"><span data-stu-id="ba389-230">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="ba389-231">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ba389-231">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba389-232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba389-232">'Blazor'</span></span>
- <span data-ttu-id="ba389-233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba389-233">'Identity'</span></span>
- <span data-ttu-id="ba389-234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba389-234">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba389-235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba389-235">'Razor'</span></span>
- <span data-ttu-id="ba389-236">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ba389-236">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ba389-237">title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '</span><span class="sxs-lookup"><span data-stu-id="ba389-237">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="ba389-238">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ba389-238">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba389-239">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba389-239">'Blazor'</span></span>
- <span data-ttu-id="ba389-240">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba389-240">'Identity'</span></span>
- <span data-ttu-id="ba389-241">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba389-241">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba389-242">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba389-242">'Razor'</span></span>
- <span data-ttu-id="ba389-243">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ba389-243">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ba389-244">title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '</span><span class="sxs-lookup"><span data-stu-id="ba389-244">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="ba389-245">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ba389-245">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba389-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba389-246">'Blazor'</span></span>
- <span data-ttu-id="ba389-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba389-247">'Identity'</span></span>
- <span data-ttu-id="ba389-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba389-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba389-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba389-249">'Razor'</span></span>
- <span data-ttu-id="ba389-250">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ba389-250">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ba389-251">title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '</span><span class="sxs-lookup"><span data-stu-id="ba389-251">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="ba389-252">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ba389-252">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba389-253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba389-253">'Blazor'</span></span>
- <span data-ttu-id="ba389-254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba389-254">'Identity'</span></span>
- <span data-ttu-id="ba389-255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba389-255">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba389-256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba389-256">'Razor'</span></span>
- <span data-ttu-id="ba389-257">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ba389-257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ba389-258">title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '</span><span class="sxs-lookup"><span data-stu-id="ba389-258">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="ba389-259">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ba389-259">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba389-260">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba389-260">'Blazor'</span></span>
- <span data-ttu-id="ba389-261">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba389-261">'Identity'</span></span>
- <span data-ttu-id="ba389-262">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba389-262">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba389-263">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba389-263">'Razor'</span></span>
- <span data-ttu-id="ba389-264">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ba389-264">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ba389-265">title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '</span><span class="sxs-lookup"><span data-stu-id="ba389-265">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="ba389-266">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ba389-266">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba389-267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba389-267">'Blazor'</span></span>
- <span data-ttu-id="ba389-268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba389-268">'Identity'</span></span>
- <span data-ttu-id="ba389-269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba389-269">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba389-270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba389-270">'Razor'</span></span>
- <span data-ttu-id="ba389-271">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ba389-271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ba389-272">title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '</span><span class="sxs-lookup"><span data-stu-id="ba389-272">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="ba389-273">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="ba389-273">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba389-274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba389-274">'Blazor'</span></span>
- <span data-ttu-id="ba389-275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba389-275">'Identity'</span></span>
- <span data-ttu-id="ba389-276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba389-276">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba389-277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba389-277">'Razor'</span></span>
- <span data-ttu-id="ba389-278">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="ba389-278">'SignalR' uid:</span></span> 

<span data-ttu-id="ba389-279">---------- | | Schránka | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | `oncut`, `oncopy`, `onpaste` | | Přetáhněte | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`,`ondragend`</span><span class="sxs-lookup"><span data-stu-id="ba389-279">---------- | | Clipboard        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | `oncut`, `oncopy`, `onpaste` | | Drag             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="ba389-280"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer>a <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> uchovávají přetažená data položky.</span><span class="sxs-lookup"><span data-stu-id="ba389-280"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span> <span data-ttu-id="ba389-281">| | Chyba | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` | | Událost | <xref:System.EventArgs>  |  *Obecné*</span><span class="sxs-lookup"><span data-stu-id="ba389-281">| | Error            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` | | Event            | <xref:System.EventArgs> | *General*</span></span><br><span data-ttu-id="ba389-282">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="ba389-282">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="ba389-283">*Schránka*</span><span class="sxs-lookup"><span data-stu-id="ba389-283">*Clipboard*</span></span><br><span data-ttu-id="ba389-284">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="ba389-284">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="ba389-285">*Vstup*</span><span class="sxs-lookup"><span data-stu-id="ba389-285">*Input*</span></span><br><span data-ttu-id="ba389-286">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span><span class="sxs-lookup"><span data-stu-id="ba389-286">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span></span><br><br><span data-ttu-id="ba389-287">*Média*</span><span class="sxs-lookup"><span data-stu-id="ba389-287">*Media*</span></span><br><span data-ttu-id="ba389-288">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="ba389-288">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="ba389-289"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers>obsahuje atributy pro konfiguraci mapování mezi názvy událostí a typy argumentů události.</span><span class="sxs-lookup"><span data-stu-id="ba389-289"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> <span data-ttu-id="ba389-290">| | Zaměření | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | `onfocus`, `onblur`, `onfocusin`,`onfocusout`</span><span class="sxs-lookup"><span data-stu-id="ba389-290">| | Focus            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | `onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="ba389-291">Nezahrnuje podporu pro `relatedTarget` .</span><span class="sxs-lookup"><span data-stu-id="ba389-291">Doesn't include support for `relatedTarget`.</span></span> <span data-ttu-id="ba389-292">| | Vstup | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | `onchange`, `oninput` | | Klávesnice | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | `onkeydown`, `onkeypress`, `onkeyup` | | Myš | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` | | Ukazatel myši | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` | | Kolečko myši | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | `onwheel`, `onmousewheel` | | Průběh | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` | | Dotykové ovládání | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`,`ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="ba389-292">| | Input            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | `onchange`, `oninput` | | Keyboard         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | `onkeydown`, `onkeypress`, `onkeyup` | | Mouse            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` | | Mouse pointer    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` | | Mouse wheel      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | `onwheel`, `onmousewheel` | | Progress         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` | | Touch            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="ba389-293"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint>představuje jeden kontaktní bod na zařízení citlivém na dotykové ovládání.</span><span class="sxs-lookup"><span data-stu-id="ba389-293"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="ba389-294">Další informace najdete v následujících materiálech:</span><span class="sxs-lookup"><span data-stu-id="ba389-294">For more information, see the following resources:</span></span>

* <span data-ttu-id="ba389-295">[Třídy EventArgs ve zdroji odkazů ASP.NET Core (větev dotnet/aspnetcore Release/3.1)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="ba389-295">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="ba389-296">[Webové dokumenty MDN: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; Obsahuje informace o tom, které prvky HTML podporují jednotlivé události modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="ba389-296">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="ba389-297">Výrazy lambda</span><span class="sxs-lookup"><span data-stu-id="ba389-297">Lambda expressions</span></span>

<span data-ttu-id="ba389-298">[Lambda výrazy](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) lze také použít:</span><span class="sxs-lookup"><span data-stu-id="ba389-298">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="ba389-299">Je často vhodné uzavřít další hodnoty, jako například při iteraci přes sadu prvků.</span><span class="sxs-lookup"><span data-stu-id="ba389-299">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="ba389-300">Následující příklad vytvoří tři tlačítka, z nichž každé volá `UpdateHeading` předání argumentu události ( <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> ) a jeho čísla tlačítka ( `buttonNumber` ), pokud je VYBRÁNO v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="ba389-300">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```razor
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> <span data-ttu-id="ba389-301">Nepoužívejte **proměnnou** smyčky ( `i` ) ve `for` smyčce přímo ve výrazu lambda.</span><span class="sxs-lookup"><span data-stu-id="ba389-301">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="ba389-302">V opačném případě se stejná proměnná používá ve všech výrazech lambda, což způsobuje `i` , že hodnota je stejná ve všech výrazech lambda.</span><span class="sxs-lookup"><span data-stu-id="ba389-302">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="ba389-303">Vždycky zachytit svou hodnotu v místní proměnné ( `buttonNumber` v předchozím příkladu) a pak ji použít.</span><span class="sxs-lookup"><span data-stu-id="ba389-303">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="ba389-304">Vnořenou eventCallback</span><span class="sxs-lookup"><span data-stu-id="ba389-304">EventCallback</span></span>

<span data-ttu-id="ba389-305">Běžný scénář s vnořenými komponentami je přáním spustit metodu nadřazené komponenty, když dojde k události podřízené součásti.</span><span class="sxs-lookup"><span data-stu-id="ba389-305">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="ba389-306">`onclick`Událost, ke které dochází v podřízené součásti, je běžným případem použití.</span><span class="sxs-lookup"><span data-stu-id="ba389-306">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="ba389-307">Chcete-li zobrazit události napříč komponentami, použijte <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="ba389-307">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="ba389-308">Nadřazená komponenta může přiřadit metodu zpětného volání podřízené součásti <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="ba389-308">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="ba389-309">`ChildComponent`V ukázkové aplikaci (*Components/ChildComponent. Razor*) ukazuje, jak `onclick` je nastavena obslužná rutina tlačítka pro příjem <xref:Microsoft.AspNetCore.Components.EventCallback> delegáta z ukázky `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="ba389-309">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="ba389-310"><xref:Microsoft.AspNetCore.Components.EventCallback>Je zadaný s `MouseEventArgs` , který je vhodný pro `onclick` událost z periferního zařízení:</span><span class="sxs-lookup"><span data-stu-id="ba389-310">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="ba389-311">`ParentComponent`Nastaví <xref:Microsoft.AspNetCore.Components.EventCallback%601> ( `OnClickCallback` ) dítěte na jeho `ShowMessage` metodu.</span><span class="sxs-lookup"><span data-stu-id="ba389-311">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="ba389-312">*Stránky/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="ba389-312">*Pages/ParentComponent.razor*:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@messageText</b></p>

@code {
    private string messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

<span data-ttu-id="ba389-313">Když je vybráno tlačítko v `ChildComponent` :</span><span class="sxs-lookup"><span data-stu-id="ba389-313">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="ba389-314">`ParentComponent` `ShowMessage` Je volána metoda.</span><span class="sxs-lookup"><span data-stu-id="ba389-314">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="ba389-315">`messageText`se aktualizuje a zobrazí v `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="ba389-315">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="ba389-316">V metodě zpětného volání () není vyžadováno volání [StateHasChanged](xref:blazor/lifecycle#state-changes) `ShowMessage` .</span><span class="sxs-lookup"><span data-stu-id="ba389-316">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="ba389-317"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>je volána automaticky pro revykreslování `ParentComponent` , stejně jako podřízené události, které aktivují revykreslování komponenty v obslužných rutinách události, které jsou spouštěny v rámci podřízeného objektu.</span><span class="sxs-lookup"><span data-stu-id="ba389-317"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="ba389-318"><xref:Microsoft.AspNetCore.Components.EventCallback>a <xref:Microsoft.AspNetCore.Components.EventCallback%601> povolují asynchronní delegáty.</span><span class="sxs-lookup"><span data-stu-id="ba389-318"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="ba389-319"><xref:Microsoft.AspNetCore.Components.EventCallback%601>je silného typu a vyžaduje konkrétní typ argumentu.</span><span class="sxs-lookup"><span data-stu-id="ba389-319"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="ba389-320"><xref:Microsoft.AspNetCore.Components.EventCallback>je slabě typované a umožňuje jakýkoli typ argumentu.</span><span class="sxs-lookup"><span data-stu-id="ba389-320"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="ba389-321">Vyvolat <xref:Microsoft.AspNetCore.Components.EventCallback> nebo <xref:Microsoft.AspNetCore.Components.EventCallback%601> s <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> a očekávat <xref:System.Threading.Tasks.Task> :</span><span class="sxs-lookup"><span data-stu-id="ba389-321">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="ba389-322">Použití <xref:Microsoft.AspNetCore.Components.EventCallback> a <xref:Microsoft.AspNetCore.Components.EventCallback%601> pro zpracování událostí a parametry komponenty vazby.</span><span class="sxs-lookup"><span data-stu-id="ba389-322">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="ba389-323">Preferovat silného typu <xref:Microsoft.AspNetCore.Components.EventCallback%601> přes <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="ba389-323">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="ba389-324"><xref:Microsoft.AspNetCore.Components.EventCallback%601>poskytuje lepší odezvu na chyby uživatelů součásti.</span><span class="sxs-lookup"><span data-stu-id="ba389-324"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="ba389-325">Podobně jako u jiných obslužných rutin událostí uživatelského rozhraní je zadání parametru události volitelné.</span><span class="sxs-lookup"><span data-stu-id="ba389-325">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="ba389-326">Použijte <xref:Microsoft.AspNetCore.Components.EventCallback> v případě, že zpětnému volání není předáno žádné číslo.</span><span class="sxs-lookup"><span data-stu-id="ba389-326">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="ba389-327">Zabránit výchozím akcím</span><span class="sxs-lookup"><span data-stu-id="ba389-327">Prevent default actions</span></span>

<span data-ttu-id="ba389-328">[`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault)Chcete-li zabránit výchozí akci pro událost, použijte atribut direktiva.</span><span class="sxs-lookup"><span data-stu-id="ba389-328">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="ba389-329">Když je vybraný klíč na vstupním zařízení a fokus prvku je v textovém poli, prohlížeč normálně zobrazuje znak klíče v textovém poli.</span><span class="sxs-lookup"><span data-stu-id="ba389-329">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="ba389-330">V následujícím příkladu je výchozím chováním znemožněno zadáním `@onkeypress:preventDefault` atributu direktiva.</span><span class="sxs-lookup"><span data-stu-id="ba389-330">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="ba389-331">Čítač zvýší a **+** klíč není zachycen do `<input>` hodnoty prvku:</span><span class="sxs-lookup"><span data-stu-id="ba389-331">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

```razor
<input value="@count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            count++;
        }
    }
}
```

<span data-ttu-id="ba389-332">Určení `@on{EVENT}:preventDefault` atributu bez hodnoty je ekvivalentní `@on{EVENT}:preventDefault="true"` .</span><span class="sxs-lookup"><span data-stu-id="ba389-332">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="ba389-333">Hodnotou atributu může být také výraz.</span><span class="sxs-lookup"><span data-stu-id="ba389-333">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="ba389-334">V následujícím příkladu `shouldPreventDefault` je `bool` pole nastaveno na buď `true` nebo `false` :</span><span class="sxs-lookup"><span data-stu-id="ba389-334">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

<span data-ttu-id="ba389-335">Obslužná rutina události není nutná, aby se zabránilo výchozí akci.</span><span class="sxs-lookup"><span data-stu-id="ba389-335">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="ba389-336">Obslužná rutina události a zabraňuje použití výchozích akcí, lze použít nezávisle.</span><span class="sxs-lookup"><span data-stu-id="ba389-336">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="ba389-337">Zastavit šíření událostí</span><span class="sxs-lookup"><span data-stu-id="ba389-337">Stop event propagation</span></span>

<span data-ttu-id="ba389-338">[`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation)Pro zastavení šíření události použijte atribut direktiva.</span><span class="sxs-lookup"><span data-stu-id="ba389-338">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="ba389-339">V následujícím příkladu zaškrtnutí políčka zabrání kliknutí na události z druhého podřízeného `<div>` objektu pro rozšíření na nadřazený `<div>` :</span><span class="sxs-lookup"><span data-stu-id="ba389-339">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

```razor
<label>
    <input @bind="stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```
