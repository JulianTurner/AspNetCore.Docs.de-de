<span data-ttu-id="f2bf1-101">Durch die Option „Zurücksetzen“ kann der Server eine HTTP/2-Anforderung mit einem angegebenen Fehlercode zurücksetzen.</span><span class="sxs-lookup"><span data-stu-id="f2bf1-101">Reset allows for the server to reset a HTTP/2 request with a specified error code.</span></span> <span data-ttu-id="f2bf1-102">Eine Anforderung zum Zurücksetzen wird als abgebrochen betrachtet.</span><span class="sxs-lookup"><span data-stu-id="f2bf1-102">A reset request is considered aborted.</span></span>

```csharp
var resetFeature = httpContext.Features.Get<IHttpResetFeature>();
resetFeature.Reset(errorCode: 2);
```

<span data-ttu-id="f2bf1-103">`Reset` gibt im vorangehenden Codebeispiel den Fehlercode `INTERNAL_ERROR` an.</span><span class="sxs-lookup"><span data-stu-id="f2bf1-103">`Reset` in the preceding code example specifies the `INTERNAL_ERROR` error code.</span></span> <span data-ttu-id="f2bf1-104">Weitere Informationen zu HTTP/2-Fehlercodes finden Sie im Abschnitt [HTTP/2-Spezifikationsfehlercode](https://tools.ietf.org/html/rfc7540#page-50).</span><span class="sxs-lookup"><span data-stu-id="f2bf1-104">For more information about HTTP/2 error codes, visit the [HTTP/2 specification error code section](https://tools.ietf.org/html/rfc7540#page-50).</span></span>