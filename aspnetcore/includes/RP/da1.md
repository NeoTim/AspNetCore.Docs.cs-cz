# <a name="update-the-generated-pages"></a><span data-ttu-id="0404c-101">Aktualizovat generované stránky</span><span class="sxs-lookup"><span data-stu-id="0404c-101">Update the generated pages</span></span>

<span data-ttu-id="0404c-102">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0404c-102">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="0404c-103">Máme o dobrý začátek aplikace movie, ale v prezentaci není ideální.</span><span class="sxs-lookup"><span data-stu-id="0404c-103">We have a good start to the movie app, but the presentation isn't ideal.</span></span> <span data-ttu-id="0404c-104">Nechceme zobrazíte čas (12:00:00 dop. na následujícím obrázku) a **ReleaseDate** by měl být **datum vydání** (dvě slova).</span><span class="sxs-lookup"><span data-stu-id="0404c-104">We don't want to see the time (12:00:00 AM in the image below) and **ReleaseDate** should be **Release Date** (two words).</span></span>

![Otevřít v prohlížeči Chrome ukazující data o filmech aplikace Movie](../../tutorials/razor-pages/sql/_static/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="0404c-106">Aktualizace generovaného kódu</span><span class="sxs-lookup"><span data-stu-id="0404c-106">Update the generated code</span></span>

<span data-ttu-id="0404c-107">Otevřít *Models/Movie.cs* a přidejte zvýrazněné řádky je znázorněno v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="0404c-107">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[](code/Models/Movie.cs?highlight=2,11-12)]
