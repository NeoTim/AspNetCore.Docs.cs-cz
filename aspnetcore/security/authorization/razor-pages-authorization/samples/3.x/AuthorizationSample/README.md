# <a name="aspnet-core-authorization-sample"></a>Ukázka autorizace ASP.NET Core

Tato ukázka znázorňuje použití autorizace Razor Pages podle konvencí. Tato ukázka předvádí funkce popsané v tématu [Razor Pages autorizační konvence](https://docs.microsoft.com/aspnet/core/security/authorization/razor-pages-authorization) .

Autorizace uživatelů v této ukázce používá funkce ověřování souborů cookie popsané v tématu [použití ověřování souborem cookie bez ASP.NET Core identity](https://docs.microsoft.com/aspnet/core/security/authentication/cookie) . Koncepty a příklady uvedené v tomto tématu platí stejně jako aplikace, které používají ASP.NET Core identity. Informace o použití ASP.NET Core identity najdete v tématu [Úvod do identity v ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/authentication/identity).

Pomocí e-mailové adresy **maria.rodriguez@contoso.com** můžete uživatele ověřit pomocí hesla. Uživatel je ověřený v `AuthenticateUser` metodě v souboru Pages/ *account/login. cshtml. cs* . V reálném příkladu by byl uživatel ověřený proti databázi.

## <a name="examples-in-this-sample"></a>Příklady v této ukázce

| Funkce | Popis |
| --- | --- |
| [AuthorizePage](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) | Přidá na stránku [AuthorizeFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) se zadanou cestou. |
| [AuthorizeFolder](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizefolder) | Přidá [AuthorizeFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) na všechny stránky ve složce se zadanou cestou. |
| [AllowAnonymousToPage](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.allowanonymoustopage) | Přidá [AllowAnonymousFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.allowanonymousfilter) na stránku se zadanou cestou. |
| [AllowAnonymousToFolder](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.allowanonymoustofolder) | Přidá [AllowAnonymousFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.allowanonymousfilter) na všechny stránky ve složce se zadanou cestou. |
