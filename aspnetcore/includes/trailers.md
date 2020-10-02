Přípojná vozidla HTTP jsou podobná hlavičkám HTTP, s výjimkou, že jsou odesílány po odeslání textu odpovědi. V případě služby IIS a HTTP.SYS jsou podporovány pouze přívěsy s odpovědí HTTP/2.

```csharp
if (httpContext.Response.SupportsTrailers())
{
    httpContext.Response.DeclareTrailer("trailername"); 

    // Write body
    httpContext.Response.WriteAsync("Hello world");

    httpContext.Response.AppendTrailer("trailername", "TrailerValue");
}
```

V předchozím příkladu kódu:

* `SupportsTrailers` zajišťuje, aby byla pro odpověď podporována přípojná vozidla.
* `DeclareTrailer` Přidá daný název přívěsu do `Trailer` hlavičky odpovědi. Deklarace přípojných vozidel odpovědi je volitelná, ale doporučuje se. Pokud `DeclareTrailer` je volána, musí být před odesláním hlaviček odpovědi.
* `AppendTrailer` připojí přípojné vozidlo.
