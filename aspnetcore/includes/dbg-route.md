## <a name="debug-diagnostics"></a>Debugdiagnose

Legen Sie für eine ausführliche Routingdiagnoseausgabe `Logging:LogLevel:Microsoft` auf `Debug` fest. Legen Sie in der Entwicklungsumgebung die Protokollebene in *appsettings.Development.json* fest:

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
