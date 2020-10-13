HTTP-Nachspanne ähneln den HTTP-Headers, jedoch werden sie erst gesendet, nachdem der Antworttext gesendet wurde. Für IIS und HTTP.sys werden nur HTTP/2-Antwortnachspanne unterstützt.

```csharp
if (httpContext.Response.SupportsTrailers())
{
    httpContext.Response.DeclareTrailer("trailername"); 

    // Write body
    httpContext.Response.WriteAsync("Hello world");

    httpContext.Response.AppendTrailer("trailername", "TrailerValue");
}
```

Im vorherigen Beispielcode:

* stellt `SupportsTrailers` sicher, dass Nachspanne für die Antwort unterstützt werden.
* fügt `DeclareTrailer` dem Antwortheader `Trailer` den angegebenen Namen für den Nachspann hinzu. Das Deklarieren des Nachspanns einer Antwort ist optional, wird jedoch empfohlen. Wenn `DeclareTrailer` aufgerufen wird, muss der Nachspann deklariert werden, bevor Antwortheader gesendet werden.
* fügt `AppendTrailer` den Nachspann an.
