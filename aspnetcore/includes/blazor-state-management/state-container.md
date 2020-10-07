<span data-ttu-id="69cdd-101">Geschachtelte Komponenten binden Daten in der Regel mithilfe *verketteter Bindungen*, wie in <xref:blazor/components/data-binding> beschrieben.</span><span class="sxs-lookup"><span data-stu-id="69cdd-101">Nested components typically bind data using *chained bind* as described in <xref:blazor/components/data-binding>.</span></span> <span data-ttu-id="69cdd-102">Geschachtelte und nicht geschachtelte Komponenten können den Zugriff auf Daten über einen registrierten arbeitsspeicherinternen Zustandscontainer teilen.</span><span class="sxs-lookup"><span data-stu-id="69cdd-102">Nested and un-nested components can share access to data using a registered in-memory state container.</span></span> <span data-ttu-id="69cdd-103">Eine benutzerdefinierte Zustandscontainerklasse kann eine <xref:System.Action> zuweisen, um Komponenten in verschiedenen Teilen der App über Zustandsänderungen zu informieren.</span><span class="sxs-lookup"><span data-stu-id="69cdd-103">A custom state container class can use an assignable <xref:System.Action> to notify components in different parts of the app of state changes.</span></span> <span data-ttu-id="69cdd-104">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="69cdd-104">In the following example:</span></span>

* <span data-ttu-id="69cdd-105">Ein Komponentenpaar verwendet einen Zustandscontainer, um eine Eigenschaft nachzuverfolgen.</span><span class="sxs-lookup"><span data-stu-id="69cdd-105">A pair of components uses a state container to track a property.</span></span>
* <span data-ttu-id="69cdd-106">Die Komponenten im Beispiel sind geschachtelt, die Schachtelung ist aber nicht erforderlich, damit dieser Ansatz funktioniert.</span><span class="sxs-lookup"><span data-stu-id="69cdd-106">The components of the example are nested, but nesting isn't required for this approach to work.</span></span>

<span data-ttu-id="69cdd-107">`StateContainer.cs`:</span><span class="sxs-lookup"><span data-stu-id="69cdd-107">`StateContainer.cs`:</span></span>

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

<span data-ttu-id="69cdd-108">In `Program.Main` (Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="69cdd-108">In `Program.Main` (Blazor WebAssembly):</span></span>

```csharp
builder.Services.AddSingleton<StateContainer>();
```

<span data-ttu-id="69cdd-109">In `Startup.ConfigureServices` (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="69cdd-109">In `Startup.ConfigureServices` (Blazor Server):</span></span>

```csharp
services.AddSingleton<StateContainer>();
```

<span data-ttu-id="69cdd-110">`Pages/Component1.razor`:</span><span class="sxs-lookup"><span data-stu-id="69cdd-110">`Pages/Component1.razor`:</span></span>

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

<span data-ttu-id="69cdd-111">`Shared/Component2.razor`:</span><span class="sxs-lookup"><span data-stu-id="69cdd-111">`Shared/Component2.razor`:</span></span>

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
