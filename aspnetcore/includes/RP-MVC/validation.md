---
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
ms.openlocfilehash: c96c5e66d2e15db03c321d239a64b98119a7543a
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552566"
---
<!-- USED in RP and MVC tutorial -->

## <a name="add-validation-rules-to-the-movie-model"></a><span data-ttu-id="fc144-101">Добавление правил проверки к модели фильма</span><span class="sxs-lookup"><span data-stu-id="fc144-101">Add validation rules to the movie model</span></span>

<span data-ttu-id="fc144-102">Пространство имен DataAnnotations предоставляет набор встроенных атрибутов проверки, которые декларативно применяются к классу или свойству.</span><span class="sxs-lookup"><span data-stu-id="fc144-102">The DataAnnotations namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="fc144-103">Кроме того, DataAnnotations содержит атрибуты форматирования (такие как `DataType`), которые обеспечивают форматирование и не предназначены для проверки.</span><span class="sxs-lookup"><span data-stu-id="fc144-103">DataAnnotations also contains formatting attributes like `DataType` that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="fc144-104">Обновите класс `Movie`, чтобы использовать преимущества встроенных атрибутов проверки `Required`, `StringLength`, `RegularExpression` и `Range`.</span><span class="sxs-lookup"><span data-stu-id="fc144-104">Update the `Movie` class to take advantage of the built-in `Required`, `StringLength`, `RegularExpression`, and `Range` validation attributes.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="fc144-105">Атрибуты проверки определяют поведение для свойств модели, к которым они применяются:</span><span class="sxs-lookup"><span data-stu-id="fc144-105">The validation attributes specify behavior that you want to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="fc144-106">Атрибуты `Required` и `MinimumLength` указывают, что свойство должно иметь значение. Тем не менее, чтобы удовлетворить требованиям проверки, пользователю достаточно ввести пробел.</span><span class="sxs-lookup"><span data-stu-id="fc144-106">The `Required` and `MinimumLength` attributes indicate that a property must have a value; but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="fc144-107">Атрибут `RegularExpression` ограничивает набор допустимых для ввода символов.</span><span class="sxs-lookup"><span data-stu-id="fc144-107">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="fc144-108">В приведенном выше коде в Genre:</span><span class="sxs-lookup"><span data-stu-id="fc144-108">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="fc144-109">должны использоваться только буквы;</span><span class="sxs-lookup"><span data-stu-id="fc144-109">Must only use letters.</span></span>
  * <span data-ttu-id="fc144-110">первая буква должна быть прописной;</span><span class="sxs-lookup"><span data-stu-id="fc144-110">The first letter is required to be uppercase.</span></span> <span data-ttu-id="fc144-111">пробелы, цифры и специальные символы не допускаются.</span><span class="sxs-lookup"><span data-stu-id="fc144-111">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="fc144-112">В `RegularExpression` Rating:</span><span class="sxs-lookup"><span data-stu-id="fc144-112">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="fc144-113">первый символ должен быть прописной буквой;</span><span class="sxs-lookup"><span data-stu-id="fc144-113">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="fc144-114">допускаются специальные символы и цифры, а также последующие пробелы.</span><span class="sxs-lookup"><span data-stu-id="fc144-114">Allows special characters and numbers in  subsequent spaces.</span></span> <span data-ttu-id="fc144-115">Значение "PG-13" допустимо для рейтинга, но недопустимо для жанра.</span><span class="sxs-lookup"><span data-stu-id="fc144-115">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="fc144-116">Атрибут `Range` ограничивает диапазон значений.</span><span class="sxs-lookup"><span data-stu-id="fc144-116">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="fc144-117">Атрибут `StringLength` позволяет задать максимальную и при необходимости минимальную длину строкового свойства.</span><span class="sxs-lookup"><span data-stu-id="fc144-117">The `StringLength` attribute lets you set the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="fc144-118">Типы значений (например, `decimal`, `int`, `float`, `DateTime`) по своей природе являются обязательными и не требуют атрибута `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="fc144-118">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="fc144-119">Наличие правил проверки, которые автоматически применяются ASP.NET Core, помогает повысить степень надежности приложения.</span><span class="sxs-lookup"><span data-stu-id="fc144-119">Having validation rules automatically enforced by ASP.NET Core helps make your app more robust.</span></span> <span data-ttu-id="fc144-120">Это также гарантирует, что в любом случае будут выполнены все проверки и в базе данных не будут случайно оставлены поврежденные данные.</span><span class="sxs-lookup"><span data-stu-id="fc144-120">It also ensures that you can't forget to validate something and inadvertently let bad data into the database.</span></span>
