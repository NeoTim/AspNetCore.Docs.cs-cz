<span data-ttu-id="3c94d-101">Přípojná vozidla HTTP jsou podobná hlavičkám HTTP, s výjimkou, že jsou odesílány po odeslání textu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="3c94d-101">HTTP Trailers are similar to HTTP Headers, except they are sent after the response body is sent.</span></span> <span data-ttu-id="3c94d-102">V případě služby IIS a HTTP.SYS jsou podporovány pouze přívěsy s odpovědí HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="3c94d-102">For IIS and HTTP.SYS, only HTTP/2 response trailers are supported.</span></span>

```csharp
if (httpContext.Response.SupportsTrailers())
{
    httpContext.Response.DeclareTrailer("trailername"); 

    // Write body
    httpContext.Response.WriteAsync("Hello world");

    httpContext.Response.AppendTrailer("trailername", "TrailerValue");
}
```

<span data-ttu-id="3c94d-103">V předchozím příkladu kódu:</span><span class="sxs-lookup"><span data-stu-id="3c94d-103">In the preceding example code:</span></span>

* <span data-ttu-id="3c94d-104">`SupportsTrailers` zajišťuje, aby byla pro odpověď podporována přípojná vozidla.</span><span class="sxs-lookup"><span data-stu-id="3c94d-104">`SupportsTrailers` ensures that trailers are supported for the response.</span></span>
* <span data-ttu-id="3c94d-105">`DeclareTrailer` Přidá daný název přívěsu do `Trailer` hlavičky odpovědi.</span><span class="sxs-lookup"><span data-stu-id="3c94d-105">`DeclareTrailer` adds the given trailer name to the `Trailer` response header.</span></span> <span data-ttu-id="3c94d-106">Deklarace přípojných vozidel odpovědi je volitelná, ale doporučuje se.</span><span class="sxs-lookup"><span data-stu-id="3c94d-106">Declaring a response's trailers is optional, but recommended.</span></span> <span data-ttu-id="3c94d-107">Pokud `DeclareTrailer` je volána, musí být před odesláním hlaviček odpovědi.</span><span class="sxs-lookup"><span data-stu-id="3c94d-107">If `DeclareTrailer` is called, it must be before the response headers are sent.</span></span>
* <span data-ttu-id="3c94d-108">`AppendTrailer` připojí přípojné vozidlo.</span><span class="sxs-lookup"><span data-stu-id="3c94d-108">`AppendTrailer` appends the trailer.</span></span>
