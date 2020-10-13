Durch die Option „Zurücksetzen“ kann der Server eine HTTP/2-Anforderung mit einem angegebenen Fehlercode zurücksetzen. Eine Anforderung zum Zurücksetzen wird als abgebrochen betrachtet.

```csharp
var resetFeature = httpContext.Features.Get<IHttpResetFeature>();
resetFeature.Reset(errorCode: 2);
```

`Reset` gibt im vorangehenden Codebeispiel den Fehlercode `INTERNAL_ERROR` an. Weitere Informationen zu HTTP/2-Fehlercodes finden Sie im Abschnitt [HTTP/2-Spezifikationsfehlercode](https://tools.ietf.org/html/rfc7540#page-50).