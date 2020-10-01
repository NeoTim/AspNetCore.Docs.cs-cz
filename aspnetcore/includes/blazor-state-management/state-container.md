<span data-ttu-id="ecf12-101">Vnořené komponenty obvykle vážou data pomocí *zřetězené vazby* , jak je popsáno v <xref:blazor/components/data-binding> .</span><span class="sxs-lookup"><span data-stu-id="ecf12-101">Nested components typically bind data using *chained bind* as described in <xref:blazor/components/data-binding>.</span></span> <span data-ttu-id="ecf12-102">Vnořené a nevnořené komponenty můžou sdílet přístup k datům pomocí registrovaného kontejneru stavu v paměti.</span><span class="sxs-lookup"><span data-stu-id="ecf12-102">Nested and un-nested components can share access to data using a registered in-memory state container.</span></span> <span data-ttu-id="ecf12-103">Třída kontejneru vlastního stavu může používat přiřazení <xref:System.Action> k oznamování komponent v různých částech aplikace změny stavu.</span><span class="sxs-lookup"><span data-stu-id="ecf12-103">A custom state container class can use an assignable <xref:System.Action> to notify components in different parts of the app of state changes.</span></span> <span data-ttu-id="ecf12-104">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="ecf12-104">In the following example:</span></span>

* <span data-ttu-id="ecf12-105">Dvojice komponent používá ke sledování vlastnosti kontejner stavu.</span><span class="sxs-lookup"><span data-stu-id="ecf12-105">A pair of components uses a state container to track a property.</span></span>
* <span data-ttu-id="ecf12-106">Komponenty příkladu jsou vnořené, ale pro přístup k této metodě není vnořování vyžadováno.</span><span class="sxs-lookup"><span data-stu-id="ecf12-106">The components of the example are nested, but nesting isn't required for this approach to work.</span></span>

<span data-ttu-id="ecf12-107">`StateContainer.cs`:</span><span class="sxs-lookup"><span data-stu-id="ecf12-107">`StateContainer.cs`:</span></span>

```csharp
public class StateContainer
{
    public string Property { get; set; } = "Initial value from StateContainer";

    public event Action OnChange;

    public void SetProperty(string value)
    {
        Property = value;
        NotifyStateChanged();
    }

    private void NotifyStateChanged() => OnChange?.Invoke();
}
```

<span data-ttu-id="ecf12-108">V `Program.Main` (Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="ecf12-108">In `Program.Main` (Blazor WebAssembly):</span></span>

```csharp
builder.Services.AddSingleton<StateContainer>();
```

<span data-ttu-id="ecf12-109">V `Startup.ConfigureServices` (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="ecf12-109">In `Startup.ConfigureServices` (Blazor Server):</span></span>

```csharp
services.AddSingleton<StateContainer>();
```

<span data-ttu-id="ecf12-110">`Pages/Component1.razor`:</span><span class="sxs-lookup"><span data-stu-id="ecf12-110">`Pages/Component1.razor`:</span></span>

```razor
@page "/Component1"
@inject StateContainer StateContainer
@implements IDisposable

<h1>Component 1</h1>

<p>Component 1 Property: <b>@StateContainer.Property</b></p>

<p>
    <button @onclick="ChangePropertyValue">Change Property from Component 1</button>
</p>

<Component2 />

@code {
    protected override void OnInitialized()
    {
        StateContainer.OnChange += StateHasChanged;
    }

    private void ChangePropertyValue()
    {
        StateContainer.SetProperty($"New value set in Component 1 {DateTime.Now}");
    }

    public void Dispose()
    {
        StateContainer.OnChange -= StateHasChanged;
    }
}
```

<span data-ttu-id="ecf12-111">`Shared/Component2.razor`:</span><span class="sxs-lookup"><span data-stu-id="ecf12-111">`Shared/Component2.razor`:</span></span>

```razor
@inject StateContainer StateContainer
@implements IDisposable

<h2>Component 2</h2>

<p>Component 2 Property: <b>@StateContainer.Property</b></p>

<p>
    <button @onclick="ChangePropertyValue">Change Property from Component 2</button>
</p>

@code {
    protected override void OnInitialized()
    {
        StateContainer.OnChange += StateHasChanged;
    }

    private void ChangePropertyValue()
    {
        StateContainer.SetProperty($"New value set in Component 2 {DateTime.Now}");
    }

    public void Dispose()
    {
        StateContainer.OnChange -= StateHasChanged;
    }
}
```
