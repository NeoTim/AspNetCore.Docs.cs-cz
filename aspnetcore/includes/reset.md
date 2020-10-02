<span data-ttu-id="899ed-101">Reset umožňuje serveru resetovat požadavek HTTP/2 se zadaným kódem chyby.</span><span class="sxs-lookup"><span data-stu-id="899ed-101">Reset allows for the server to reset a HTTP/2 request with a specified error code.</span></span> <span data-ttu-id="899ed-102">Žádost o obnovení je považována za přerušenou.</span><span class="sxs-lookup"><span data-stu-id="899ed-102">A reset request is considered aborted.</span></span>

```csharp
var resetFeature = httpContext.Features.Get<IHttpResetFeature>();
resetFeature.Reset(errorCode: 2);
```

<span data-ttu-id="899ed-103">`Reset` v předchozím příkladu kódu určuje `INTERNAL_ERROR` kód chyby.</span><span class="sxs-lookup"><span data-stu-id="899ed-103">`Reset` in the preceding code example specifies the `INTERNAL_ERROR` error code.</span></span> <span data-ttu-id="899ed-104">Další informace o kódech chyb HTTP/2 najdete v [části kód chyby specifikace HTTP/2](https://tools.ietf.org/html/rfc7540#page-50).</span><span class="sxs-lookup"><span data-stu-id="899ed-104">For more information about HTTP/2 error codes, visit the [HTTP/2 specification error code section](https://tools.ietf.org/html/rfc7540#page-50).</span></span>