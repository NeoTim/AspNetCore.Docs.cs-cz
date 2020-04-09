## <a name="grpc-not-supported-on-azure-app-service"></a>gRPC není ve službě Azure App Service podporováno

> [!WARNING]
> [ASP.NET Core gRPC](xref:grpc/index) není momentálně podporované ve službě Azure App Service nebo IIS. Http/2 implementace Http.Sys nepodporuje http odpověď koncové hlavičky, které gRPC spoléhá. Další informace naleznete v [tomto problému GitHub](https://github.com/dotnet/AspNetCore/issues/9020).
