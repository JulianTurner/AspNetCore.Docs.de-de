::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

> [!WARNING]
> <span data-ttu-id="1f937-101">Ein **catch-all**-Parameter kann aufgrund eines [Fehlers](https://github.com/dotnet/aspnetcore/issues/18677) beim Routing nicht ordnungsgemäß mit Routen übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="1f937-101">A **catch-all** parameter may match routes incorrectly due to a [bug](https://github.com/dotnet/aspnetcore/issues/18677) in routing.</span></span> <span data-ttu-id="1f937-102">Apps, die von diesem Fehler betroffen sind, weisen die folgenden Merkmale auf:</span><span class="sxs-lookup"><span data-stu-id="1f937-102">Apps impacted by this bug have the following characteristics:</span></span>
>
> * <span data-ttu-id="1f937-103">Eine catch-all-Route, zum Beispiel `{**slug}"`</span><span class="sxs-lookup"><span data-stu-id="1f937-103">A catch-all route, for example, `{**slug}"`</span></span>
> * <span data-ttu-id="1f937-104">Die catch-all-Route kann nicht mit Anforderungen abgeglichen werden, die abgeglichen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="1f937-104">The catch-all route fails to match requests it should match.</span></span>
> * <span data-ttu-id="1f937-105">Durch das Entfernen anderer Routen funktioniert die catch-all-Route.</span><span class="sxs-lookup"><span data-stu-id="1f937-105">Removing other routes makes catch-all route start working.</span></span>
>
> <span data-ttu-id="1f937-106">Weitere Beispiele zu diesem Fehler finden Sie in den GitHub-Issues [18677](https://github.com/dotnet/aspnetcore/issues/18677) und [16579](https://github.com/dotnet/aspnetcore/issues/16579).</span><span class="sxs-lookup"><span data-stu-id="1f937-106">See GitHub bugs [18677](https://github.com/dotnet/aspnetcore/issues/18677) and [16579](https://github.com/dotnet/aspnetcore/issues/16579) for example cases that hit this bug.</span></span>
>
> <span data-ttu-id="1f937-107">Eine Opt-in-Behebung für diesen Fehler ist im [.NET Core 3.1.301 SDK und höher](https://dotnet.microsoft.com/download/dotnet-core/3.1) enthalten.</span><span class="sxs-lookup"><span data-stu-id="1f937-107">An opt-in fix for this bug is contained in [.NET Core 3.1.301 SDK and later](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="1f937-108">Der folgende Code legt einen internen Switch fest, mit dem dieser Fehler behoben wird:</span><span class="sxs-lookup"><span data-stu-id="1f937-108">The following code sets an internal switch that fixes this bug:</span></span>
>
>```csharp
>public static void Main(string[] args)
>{
>    AppContext.SetSwitch("Microsoft.AspNetCore.Routing.UseCorrectCatchAllBehavior", 
>                          true);
>    CreateHostBuilder(args).Build().Run();
>}
>// Remaining code removed for brevity.
>```

::: moniker-end