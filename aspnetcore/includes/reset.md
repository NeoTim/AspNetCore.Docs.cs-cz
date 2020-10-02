Reset umožňuje serveru resetovat požadavek HTTP/2 se zadaným kódem chyby. Žádost o obnovení je považována za přerušenou.

```csharp
var resetFeature = httpContext.Features.Get<IHttpResetFeature>();
resetFeature.Reset(errorCode: 2);
```

`Reset` v předchozím příkladu kódu určuje `INTERNAL_ERROR` kód chyby. Další informace o kódech chyb HTTP/2 najdete v [části kód chyby specifikace HTTP/2](https://tools.ietf.org/html/rfc7540#page-50).