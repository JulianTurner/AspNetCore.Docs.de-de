Geschachtelte Komponenten binden Daten in der Regel mithilfe *verketteter Bindungen*, wie in <xref:blazor/components/data-binding> beschrieben. Geschachtelte und nicht geschachtelte Komponenten können den Zugriff auf Daten über einen registrierten arbeitsspeicherinternen Zustandscontainer teilen. Eine benutzerdefinierte Zustandscontainerklasse kann eine <xref:System.Action> zuweisen, um Komponenten in verschiedenen Teilen der App über Zustandsänderungen zu informieren. Im folgenden Beispiel:

* Ein Komponentenpaar verwendet einen Zustandscontainer, um eine Eigenschaft nachzuverfolgen.
* Die Komponenten im Beispiel sind geschachtelt, die Schachtelung ist aber nicht erforderlich, damit dieser Ansatz funktioniert.

`StateContainer.cs`:

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

In `Program.Main` (Blazor WebAssembly):

```csharp
builder.Services.AddSingleton<StateContainer>();
```

In `Startup.ConfigureServices` (Blazor Server):

```csharp
services.AddSingleton<StateContainer>();
```

`Pages/Component1.razor`:

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

`Shared/Component2.razor`:

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
