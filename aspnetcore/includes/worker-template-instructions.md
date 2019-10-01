# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Vytvořte nový projekt.
1. Vyberte **službu pracovního procesu**. Vyberte **Další**.
1. Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu. Vyberte **Vytvořit**.
1. V dialogovém okně **vytvořit novou službu pracovního procesu** vyberte **vytvořit**.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. Vytvořte nový projekt.
1. Na bočním panelu vyberte **aplikaci** v části **.NET Core** .
1. V části **ASP.NET Core**vyberte **Worker** . Vyberte **Další**.
1. Pro **cílovou architekturu**vyberte **.NET Core 3,0** . Vyberte **Další**.
1. Do pole **název projektu** zadejte název. Vyberte **Vytvořit**.

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Použijte šablonu Worker Service`worker`() s příkazem [dotnet New](/dotnet/core/tools/dotnet-new) z příkazového prostředí. V následujícím příkladu se vytvoří aplikace pracovní služby s názvem `ContosoWorker`. Složka pro `ContosoWorker` aplikaci se vytvoří automaticky při spuštění příkazu.

```dotnetcli
dotnet new worker -o ContosoWorker
```
