# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Vytvoření nového projektu
1. Vyberte **položku Pracovní služba**. Vyberte **další**.
1. Zadejte název projektu do pole **Název projektu** nebo přijměte výchozí název projektu. Vyberte **Vytvořit**.
1. V **dialogovém** okně Vytvořit novou pracovní službu vyberte **Vytvořit**.

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

1. Vytvoření nového projektu
1. V postranním panelu vyberte **Aplikaci** pod **.NET Core.**
1. V části **ASP.NET jádrem**vyberte **Možnost Pracovník** . Vyberte **další**.
1. Vyberte **rozhraní .NET Core 3.0** nebo novější pro **cílovou architekturu**. Vyberte **další**.
1. Zadejte název do pole **Název projektu.** Vyberte **Vytvořit**.

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Použijte šablonu`worker`Pracovní služba ( ) s novým příkazem [dotnet](/dotnet/core/tools/dotnet-new) z příkazového prostředí. V následujícím příkladu je vytvořena aplikace `ContosoWorker`Pracovní služba s názvem . Složka aplikace `ContosoWorker` se vytvoří automaticky při spuštění příkazu.

```dotnetcli
dotnet new worker -o ContosoWorker
```

---
