## <a name="grpc-not-supported-on-azure-app-service"></a>gRPC není u Azure App Service podporován.

> [!WARNING]
> [ASP.NET Core gRPC](xref:grpc/index) se v tuto chvíli v Azure App Service nebo IIS nepodporuje. Implementace HTTP. sys s protokolem HTTP/2 nepodporuje koncové hlavičky odpovědí HTTP, na kterých gRPC spoléhá. Další informace najdete v tématu [tento problém Githubu](https://github.com/aspnet/AspNetCore/issues/9020).
