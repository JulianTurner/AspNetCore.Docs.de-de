## <a name="debug-diagnostics"></a><span data-ttu-id="b1692-101">Debugdiagnose</span><span class="sxs-lookup"><span data-stu-id="b1692-101">Debug diagnostics</span></span>

<span data-ttu-id="b1692-102">Legen Sie für eine ausführliche Routingdiagnoseausgabe `Logging:LogLevel:Microsoft` auf `Debug` fest.</span><span class="sxs-lookup"><span data-stu-id="b1692-102">For detailed routing diagnostic output, set `Logging:LogLevel:Microsoft` to `Debug`.</span></span> <span data-ttu-id="b1692-103">Legen Sie in der Entwicklungsumgebung die Protokollebene in *appsettings.Development.json* fest:</span><span class="sxs-lookup"><span data-stu-id="b1692-103">In the development environment, set the log level in *appsettings.Development.json*:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Debug",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```
