# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Vytvořte nový projekt.
1. Vyberte **službu pracovního procesu**. Vyberte **Další**.
1. Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu. Vyberte **vytvořit**.
1. V dialogovém okně **vytvořit novou službu pracovního procesu** vyberte **vytvořit**.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

1. Vytvořte nový projekt.
1. Na bočním panelu vyberte **aplikaci** v části **.NET Core** .
1. V části **ASP.NET Core**vyberte **Worker** . Vyberte **Další**.
1. Pro **cílovou architekturu**vyberte **.NET Core 3,0** nebo novější. Vyberte **Další**.
1. Do pole **název projektu** zadejte název. Vyberte **vytvořit**.

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Pomocí příkazu [dotnet New](/dotnet/core/tools/dotnet-new) v příkazovém prostředí použijte šablonu Worker Service (`worker`). V následujícím příkladu se vytvoří aplikace pracovní služby s názvem `ContosoWorker`. Složka pro aplikaci `ContosoWorker` se vytvoří automaticky při spuštění příkazu.

```dotnetcli
dotnet new worker -o ContosoWorker
```

---
