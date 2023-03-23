# 前端领域驱动架构，第 2 部分

> 原文：<https://betterprogramming.pub/domain-driven-architecture-in-the-frontend-ii-cdc46cf8ac1c>

## 您的域代码如何与您的前端应用程序交互

![](img/9685b15fac8189be3171246ed29aaaa9.png)

由[安德斯·吉尔登](https://unsplash.com/@andersjilden?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

*(这是前面一篇关于领域驱动架构文章的延续。要了解这是什么，为什么它很重要，以及如何在您的前端代码中实现它，* [*请查看第一部分*](https://medium.com/@cesarmartinezd/domain-driven-architecture-in-the-frontend-i-d27fb71b5cb0) *。)*

到目前为止，您已经有了一个域类、它的类型和一个存储库。您可能想知道这将如何影响您的应用程序代码和框架(Vue、React、Angular、Svelte 等)，以及您将如何在您的组件中使用它。很抱歉让您失望了，我们还没有完全准备好接触框架代码，但是我们已经越来越接近了。

# 主要的

我们现在还不能谈论你的框架代码的原因是你的域对象不能直接进入你的组件。正如前一篇文章所说，您的域名必须受到保护。

允许域对象进入组件使它们在任何地方都可以被各种操作。这就是复杂性变得难以控制的原因。

由于我们不提供对域对象的免费访问，该架构建议提供对视图对象的访问。

# 查看对象

让我们深入代码([酱此处](https://github.com/Blindpupil/family-cookbook)):

```
import type { Recipe } from "@/domain/recipe/Recipe";
import type { RecipeId } from "@/domain/recipe/types";
import type { Ingredient } from "@/domain/ingredient/Ingredient";export class RecipeView {
  private constructor(
    public readonly id: RecipeId,
    public readonly name: string,
    public readonly ingredients: Ingredient[],
    public readonly instructions: string,
    public readonly portions: number,
    public readonly updatedAt: string
  ) {} static fromDomain(recipe: Recipe) {
    const { id, name, ingredients, instructions, portions, updatedAt } =
      recipe.properties;
    return new RecipeView(
      id,
      name,
      ingredients,
      instructions,
      portions,
      updatedAt.toLocaleDateString()
    );
  } get mealSizeIcon() {
    switch (this.portions) {
      case 1:
        return "single";
      case 2:
        return "couple";
      case 3:
        return "family-one-kid";
      default:
        return "family-two-kids";
    }
  }
}
```

如您所见，视图是域对象的公共可访问表示。视图没有域对象那么多规则(实际上没有)。您有更多的自由来实现对您和您的团队最方便的视图。让我们看看我们能从这个例子中学到什么:

1.  欢迎您导入域类型并使用它们来键入您喜欢的类型脚本代码。
2.  构造函数也是私有的:它需要通过`static fromDomain`方法实例化。我们传递域实例和解构属性而不是仅仅传递属性的原因是，域实例可以有视图所需的 getterss，而这些 getter 在属性中并不存在。
3.  视图的属性不需要与域对象的属性相匹配。在这个例子中，我们将`updatedAt`日期转换成要在 UI 中显示的字符串。这是不是一个好的选择完全取决于你正在开发的应用程序的需求。
4.  您可以根据需要用 getters，甚至额外的属性来增强视图类。在这个例子中，我添加了`mealSizeIcon` getter。

还记得我们在本文第一部分[中提到的关于简化组件的问题吗？您在视图中放置的东西是帮助您编写更笨、更简单的组件的东西之一。在许多情况下，这可能是比创建越来越小的组件更方便的解决方案(参见下面的提示和技巧了解更多细节)。](https://dev.to/blindpupil/domain-driven-architecture-in-the-frontend-i-1f41)

也许你现在想知道，我们应该如何将这些视图对象放入组件中？甚至，这些视图是在哪里实例化的？如果你是，那么你就在正确的轨道上。

# 用例

如果主要是连接域、次要和 UI 的东西，那么用例就是连接发生的道路。每当 UI 需要从域中访问任何东西时，都有一个用例来提供它。让我们看看这是什么样子:

```
import type { RecipeRepository } from "@/domain/recipe/repository/RecipeRepository";
import type { UserId } from "@/domain/user/types";
import { RecipeView } from "@/primary/recipe/RecipeView";export class GetRecipesUseCase {
  constructor(private readonly recipeRepository: RecipeRepository) {} async execute(userId: UserId): Promise<RecipeView[]> {
    const recipes = await this.recipeRepository.getRecipes(userId); return recipes.map(RecipeView.fromDomain);
  }
}
```

[来源。](https://github.com/Blindpupil/family-cookbook/blob/master/src/primary/recipe/use-cases/GetRecipesUseCase.ts)

在其最基本的形式中，一个用例将执行一个存储库方法，并将那些域的视图返回给 UI。这里有几件事值得注意:

1.  `UseCases`是由存储库构成的，而不是由实现它们的类构成的。(这是 SOLID 中的 D:依赖抽象，而不是具体)。
2.  `UseCases`有一个公共方法“execute ”,但是可以有任意多的私有方法。
3.  这是唯一允许执行域方法的地方。请求域对象的突变只发生在用例中。
4.  `UseCases`始终返回视图(或 void)。

即使这个例子没有显示出来，用例也会变得充满逻辑。当需要显示域之间的关系时，`UseCases`可以根据需要构建尽可能多的存储库来处理它。这是因为你会希望设计尽可能方便用户界面的用例。这是保持组件简洁的另一种方式。

例如，假设您想要显示可以用季节性食材烹饪的食谱列表。为此，我们可能需要 IngredientRepository 获取当前季节的配料列表，然后用包含这些配料的过滤器调用`RecipeRepository`。所有的逻辑都可以驻留在相应的`GetSeasonalRecipes`用例中。

另一个常见的例子:如果您的应用程序经常要求您为请求提供登录的用户 id，您可以决定让用例发出请求来获取该 id(例如使用 UserRepository)，而不是让组件来处理它。

> 当有几个用例需要几个存储库时，您可能需要小心避免循环依赖。

## 服务或用例索引

你将会在你的每一个领域中拥有大量的用例。因此，您的`use-cases`目录应该包含所有用例的索引，以便于访问一个领域的所有用例。这个索引采用了我们称之为“服务”的类的形式。该服务可能是这样的:

```
import type { RecipeRepository } from "@/domain/recipe/repository/RecipeRepository";
import type { RecipeToSave } from "@/domain/recipe/types";
import type { UserId } from "@/domain/user/types";
import type { UserRepository } from "@/domain/user/repository/UserRepository";
import { CreateRecipeUseCase } from "@/primary/recipe/use-cases/CreateRecipeUseCase";
import { GetRecipesUseCase } from "@/primary/recipe/use-cases/GetRecipesUseCase";export class RecipeService {
  private getRecipesUseCase: GetRecipesUseCase;
  private createRecipeUseCase: CreateRecipeUseCase; constructor(
    private readonly recipeRepository: RecipeRepository,
    private readonly userRepository: UserRepository
  ) {
    this.getRecipesUseCase = new GetRecipesUseCase(recipeRepository);
    this.createRecipeUseCase = new CreateRecipeUseCase(
      recipeRepository,
      userRepository
    );
  } async getRecipes(userId: UserId) {
    return await this.getRecipesUseCase.execute(userId);
  } async createRecipe(form: RecipeToSave) {
    return await this.createRecipeUseCase.execute(form);
  }
}
```

[来源。](https://github.com/Blindpupil/family-cookbook/blob/master/src/primary/recipe/use-cases/index.ts)

正如你所看到的，服务只是索引用例的“执行”功能。这样，当你在你的框架中使用它们时，你不需要一个接一个地注入用例。

您可以简单地注入服务。另外，能够在组件中调用`recipeService.getRecipes(userId)`比调用`getRecipesUseCase.execute(userId)`好得多。

# 副手

在许多地方，我们已经使用了`Repositories`接口来引用应该为我们提供域对象的函数。我们还没有实现这些存储库。我们接下来会这么做。

# 资源

应用程序与外部世界的所有通信都是由资源类实现的。我说的全部，就是全部。当一个组件需要显示一些数据时，它并不关心这些数据是来自 REST API、GraphQL API、本地存储、状态管理存储、WebSockets、edge 函数、firebase、supabase、cookies、IndexedDB 还是其他任何东西。资源是唯一需要处理这些问题的资源。

您的资源将实现在域中定义的存储库。它会尽一切努力来履行合同。这取决于你，前端开发，以确保它以最有效的方式做到这一点。然而，你会注意到，这个任务变得简单多了，因为你只有一个地方需要考虑它。

让我们从一个简单的例子开始:

```
import type { RecipeRepository } from "@/domain/recipe/repository/RecipeRepository";
import type { UserId } from "@/domain/user/types";
import type { Recipe } from "@/domain/recipe/Recipe";
import type { ApiRecipe } from "@/secondary/recipe/ApiRecipe";
import type { RestClient } from "@/secondary/RestClient";export class RecipeResource implements RecipeRepository {
  constructor(private readonly restClient: RestClient) {} async getRecipes(userId: UserId): Promise<Recipe[]> {
    const apiRecipes = await this.restClient.get<ApiRecipe[]>(
      `/users/${userId}/recipes`
    ); return apiRecipes.map((apiRecipe) => apiRecipe.toDomain());
  } async getFavoriteRecipes(userId: UserId): Promise<Recipe[]> {
    //
  } async createRecipe(userId: UserId, form: RecipeToSave): Promise<Recipe> {
    //
  } async updateRecipe(recipeId: RecipeId, form: RecipeToSave): Promise<Recipe> {
    //
  } async deleteRecipe(recipeId: RecipeId): Promise<void> {
    //
  }
}
```

[完整源代码](https://github.com/Blindpupil/family-cookbook/blob/master/src/secondary/recipe/RecipeResource.ts)。

这里重要的是`RecipeResource implements RecipeRepository`部分。在资源库中的所有方法都在资源中实现之前，您会在 IDE 中看到一个 TypeScript 错误。

在这里显示的简单例子中，`RecipeResource`只从 REST API 获取信息。所以它只使用了`RestClient`(它只是一个 fetch 的包装器)。

如果以后我们想用 Axios 或其他 HTTP 客户端替换 fetch，我们将能够在不干扰存储库的情况下这样做。此外，如果资源还需要连接 GraphQL API，例如，您可以简单地将自己的`graphQLClient`添加到构造函数中并使用它。

## API 适配器

API 提供的响应很难在前端与您的域完全匹配。适配器允许您将响应转换为域对象。

这里有一个例子:

```
export class ApiRecipe {
  constructor(
    public readonly id: RecipeId,
    public readonly name: string,
    public readonly ingredients: ApiIngredient[],
    public readonly instructions: string,
    public readonly portions: number,
    public readonly updatedAt: string
  ) {} toDomain(): Recipe {
    const ingredients = this.ingredients.map((ingredient) =>
      ingredient.toDomain()
    ); return Recipe.fromProperties({
      id: this.id,
      name: this.name,
      ingredients: ingredients.map((ingredient) => ingredient.properties),
      instructions: this.instructions,
      portions: this.portions,
      updatedAt: new Date(this.updatedAt),
    });
  }
}
```

[来源](https://github.com/Blindpupil/family-cookbook/blob/master/src/secondary/recipe/ApiRecipe.ts)。

在这个例子中，我们将 API 发送的`updatedAt`字符串转换成域期望的日期实例。

> *客户端会自动将 API 响应转换成* `*ApiRecipe*` *的实例。这就是为什么我们可以安全地用* `*restClient.get<ApiRecipe[]>*` *键入响应，并在* `*apiRecipes.map((apiRecipe) => apiRecipe.toDomain())*`中使用它

# 商店

您的组件不能直接访问状态管理库。组件需要的所有数据都从服务中调用，服务调用用例，用例与资源实现的存储库交互。然后是代表组件与存储交互的资源。

让我们扩展一下前面的例子，看看它是如何工作的。假设我们想在第一次点击 API 时将食谱存储在存储中。如果食谱已经存储，我们还想避免再次点击 API:

```
export class RecipeResource implements RecipeRepository {
  constructor(
    private readonly restClient: RestClient,
    private readonly store: RecipeStore
  ) {} async getRecipes(userId: UserId): Promise<Recipe[]> {
    const recipesInStore = this.store.recipes;
    if (recipesInStore.length !== 0) {
      return recipesInStore.map(Recipe.fromProperties);
    } const apiRecipes = await this.restClient.get<ApiRecipe[]>(
      `/users/${userId}/recipes`
    );
    const recipes = apiRecipes.map((apiRecipe) => apiRecipe.toDomain());
    this.store.saveRecipes(recipes.map((recipe) => recipe.properties)); return recipes;
  }
  // ...
}
```

为此，将`RecipeStore`传递给构造函数。`getRecipes`方法现在首先检查食谱是否已经在存储中。如果是，它将从存储的数据中返回实例化它们的域对象。如果没有存储食谱，那么我们获取它们，然后将它们保存在存储中，以便下次调用该函数时返回存储的食谱。差不多就是这样，真的。

如您所见，使用这种方法，您的商店的操作中永远不会有 API 调用。您对存储所做的一切就是保存和检索数据，正如预期的那样。

## **警告**

有一种特殊情况，在这种情况下，最方便的解决方案是让您的组件直接对存储中的变化做出反应。我还不确定以符合该架构原则的方式实现这一点的最佳方式是什么。但是，以我的经验来看，这真的是例外。您需要有两个或更多的兄弟组件，相互依赖的数据显示在同一页面上。

在 99%的情况下，你可以从存储中获取一次数据，让框架的内部反应系统通过 props、组件状态、事件等处理剩下的数据。

无论如何，可能有一种兼容的方式来做到这一点(可能[使用皮尼亚的或 Vuex](https://pinia.vuejs.org/core-concepts/state.html#subscribing-to-the-state) 的机制)，但我还没有想出来。如果你想通了，请告诉我！

# 用户界面

我们终于到了！现在我们来谈谈框架代码！我们已经拥有了调用我们领域的能力并在我们的组件中使用它所需要的一切。不幸的是，我不能告诉你在每一个前端框架中做这件事的最好方法，但是我会告诉你我是如何在 Vue 中学会做这件事的，我会告诉你在你选择的框架中你需要考虑什么来做出最好的决定。

在 Vue 中，我们使用[提供/注入机制](https://vuejs.org/guide/components/provide-inject.html#provide-inject)让组件访问它们需要的服务。您可以使用应用程序级提供，或者组件级提供(如果您知道在组件树中的什么位置提供)。

```
import { createApp } from "vue";
import { createPinia } from "pinia";import App from "./App.vue";import { RecipeResource } from "@/secondary/recipe/RecipeResource";
import { RestClient } from "@/secondary/RestClient";
import { useRecipeStore } from "@/secondary/recipe/RecipeStore";
import { RecipeService } from "@/primary/recipe/use-cases";
import { UserResource } from "@/secondary/user/UserResource";// Services
const pinia = createPinia();
const restClient = new RestClient();const userResource = new UserResource();const recipeStore = useRecipeStore();
const recipeResource = new RecipeResource(restClient, recipeStore);
const recipeService = new RecipeService(recipeResource, userResource);// Setup
const app = createApp(App);app.use(pinia);
app.provide<RecipeService>("recipeService", recipeService);app.mount("#app");
```

如您所见，您的所有组件真正需要访问的是服务。

之后，您可以在闲暇时在组件中使用它们:

```
export default defineComponent({
  setup() {
    const recipeService = inject<RecipeService>("recipeService")!; return {
      recipeService,
    };
  }, data() {
    return {
      recipes: [] as RecipeView[],
    };
  }, async created() {
    this.recipes = await this.recipeService.getRecipes("me");
  },
});
```

注入比简单的导入更可取，因为它将大大简化测试。像这样，您可以轻松地创建自己的模拟对象，并在测试时将它们注入到组件中。

通过这种方式，您可以轻松地测试引入到组件中的所有可能的数据状态。对于 React 来说，也许使用[上下文](https://reactjs.org/docs/context.html)来包含服务是正确的做法，但是我会让您来判断。

# 额外的

如果您已经做到了这一步，那么您已经拥有了开始自己的领域驱动前端架构之旅所需的所有元素。现在，我将分享一些我在个人旅程中学到的值得注意的经验。

## **数据流程图**

这里有一个简洁的图表来记住数据在架构中如何流动的主要思想:

![](img/1a121541a92dbb58b4e16866c924060a.png)

蓝色是 API 对象，红色是视图对象，紫色是域对象。数据可以双向流动(传入和发送)，但应该始终遵循相同的路径。来自外部源(是的，包括商店)的输入数据采用 API 对象的形式。资源接收这些，实例化域实例，并将它们传递给用例。用例然后将视图对象返回给组件。
注意，域占用了次要和主要的一些空间。这是为了表示领域在那些领域的影响:资源实现了领域存储库，用例是唯一调用领域方法的地方。

# 提示和技巧

有时候，实现这个架构最困难的部分是决定什么进入域，什么进入视图，以及什么可以合法地留在组件中。考虑这个问题的方式与你的前端代码和后端代码有很大的不同。

## 如何知道什么是领域的一部分，什么不是

[在《T3》一书中，Scott Millett 和 Nick Tune 让我们思考应用程序最重要的部分，也就是真正提供价值的部分。DDD 的全部意义在于创造条件，让开发人员能够将大部分注意力集中在这件事上。](https://www.momox-shop.fr/scott-millett-patterns-principles-and-practices-of-domain-driven-design-taschenbuch-M01118714709.html)

太棒了，你绝对应该这么做。然而，在日常工作中，当您接收到一个实现新特性的任务，并且您需要决定在您的域模型中包含哪些属性和方法时，您将需要一个更具体的答案。答案会因公司和项目而异。如果使您的解决方案与众不同的是 UX/用户界面，而不是驱动它的商业智能，那该怎么办？这是否意味着 UX/UI 相关的属性将进入该领域？一个人必须处在那个位置才能回答这个问题。

我学到的一件事(艰难的方式)是，我永远不应该假设后端提供的任何东西都需要在前端的领域模型中。这样做错过了这个练习的要点。作为一名前端开发人员，我鼓励你不要害怕重新考虑你的决定。查看您从后台收到的所有属性是否/在哪里/如何被使用。在你的领域模型中，只实现你完成目标所需要的东西，其他的都不要考虑。

如果以后有人需要向领域模型添加其他属性来增加您的功能，让他们去做，这是一件好事，因为您希望那个人自己考虑他将需要的属性。你现在不需要为他考虑。

就我个人而言，我发现这样的想法很有用，如果在应用程序中使用它的决定来自于产品所有者(产品人员，理想情况下是对问题和解决方案有全面了解的人)，那么它通常会出现在领域中。如果决定来自设计者(或者决定 UI 的人)，那么你可以把它放在视图中。

## 如何知道在视图中放置什么，为组件留下什么

为了回答这个问题，我们作为前端开发人员将不得不(再次)把应用程序作为一个整体来考虑，但是这次是从 UX/UI 的角度。当你考虑在域中放置什么的时候，你可能会有一些问题，这些问题可能会直接指向产品人员。当你考虑在视图中放置什么的时候，你可能会有一些问题，这些问题很可能会被提交给 UI/UX 设计师。

注意预期的设计，思考什么信息和/或关于信息的视觉队列总是一起出现。我之前用了一个关于我的烹饪书应用的例子:设计师可能决定每次显示一个食谱的难度，然后显示一个特定的图标来代表这个难度。如果情况总是如此，那么这是一个强有力的论据来说明在配方难度属性旁边或旁边放置一个图标。我经常看到的另一个好例子是显示过程的状态(例如，配方是草稿，银行转账正在进行，预约被取消)。通常当传达这样的信息时，设计者总是选择使用一些特定的视觉队列。这些可能会包含在你的观点中。特定于单个组件的元素可以保留在组件中。

当一个公司有一个一致的设计系统时，视图的力量才真正显现出来。如果是这样的话，你可以通过浏览和理解设计系统找到你需要的所有答案。例如，如果您在系统中看到日期总是以一种或两种格式显示，那么您可以在视图中包含这些格式。在这种情况下，组件不需要将数据转换成适当的格式。

对于实现这种架构的前端开发人员来说，维护良好的设计系统的价值再高也不过分。即使在非常复杂的解决方案中，您和您的团队所能实现的敏捷性也是无与伦比的。

## 到处都有这么多课。有没有更实用的方法来实现这一点？

我不是函数式编程的专家，但是是的，我相信有。事实是，我自己并不喜欢上课(或者至少我不是)。我唯一真正相信从类的形式中获益良多的是域模型。

我认为明确哪些属性是私有的和只读的，哪些不是非常重要。如果你愿意的话，你或许可以找出兼容的方法，用纯函数来实现架构的其余部分。如果你有，打电话给我，我很想看看！

# 结论

当我实现这个架构(或者至少是它最有价值的经验)时，我从来没有像现在这样快乐地编码。对我来说，这意味着在屏幕上随意写命令直到它起作用，和实际上感觉在控制正在发生的事情之间的区别。这就是我写这篇长篇文章的原因。如果我能帮助任何人感受到同样的快乐，我已经尽了我的一份力量。

也就是说，这种建筑，实际上是 DDD 的整体，并不适合每个人和每个项目。Millett 和 Tune 说，如果你正在构建另一个待办事项应用程序，那么你可能不需要 DDD。我认为这是因为在待办事项列表中没有需要管理的复杂性。任何开发人员都可以找到代码并理解一切，因为每个人都知道待办事项列表是如何工作的。然而，如果你正在建立一个独特的待办事项列表，它具有让用户感到特别的特性，那么也许 DDD 的教导会对你有用。

对我来说，主要的教导是:

*   当考虑如何管理日益复杂的代码库时，不要浪费时间。我和我的团队围绕这个主题进行的讨论(所有这些都为我在本文中表达的观点提供了信息)是我职业生涯中最丰富的内容，也是我迄今为止能够编写的最佳代码的基础。
*   没有时间浪费在思考领域上。在领域驱动架构下，对我来说，实现一个新的复杂特性的一天看起来和没有它的一天完全不同。在前一种情况下，跟随我一天，你会看到我盯着天花板，去喝咖啡，看设计，散步，和团队成员交谈，5 个小时不写一行代码，只是思考…然后他们回来，在 2 个小时内写出一个让我感到自豪的解决方案。在后一种情况下，你可能会看到我花了两天时间写代码，遇到莫名其妙的错误，不和任何人说话，并提供了一个解决方案，对此我感到很高兴，我再也不用处理这些问题了。
*   在你的代码库中，定义清晰的职责，当你发现新的职责出现时，也要把它们分开。
*   保护模型。就像在 flux 设计模式中，对存储的任何更改都被约束到特定的方式，同样，对域对象的更改也应该被约束。使用可用的语言特性来通知开发人员这些(有用的)约束。
*   编写测试。我没有机会在这里讨论测试，但是它是 DDD 一个很大的、不可分割的部分，(对于任何严肃的应用程序的编码都应该是一样的，不管是哪种设计)。[听鲍勃大叔在这里讨论自己](https://completedeveloperpodcast.com/episode-232/?utm_source=rss&utm_medium=rss&utm_campaign=episode-232)。
*   所有项目都有一个架构。不选择建筑是一个建筑决策。这样的决定总是应该有意识地做出，权衡利弊，并让每个人都知道最终决定背后的推理。说到利弊…

# 这种架构的利弊

最后，这里是我从自己的经历中收集到的利弊的简要概述。

## 赞成的意见

*   将开发人员置于一个他们必须考虑领域的位置，这只是“考虑手边业务问题的最佳解决方案”的另一种说法。
*   产生可维护的、有组织的、健壮的、可测试的(最好是经过测试的)代码。
*   更有益的编码体验，因此更有益的工作体验(至少对个人而言)。

## 骗局

*   入门成本增加。这对于大多数前端来说尤其新鲜。我花了相当长的时间才真正理解了我在编码时突然需要考虑的所有架构决策的方法和原因。
*   增加样板文件。这是我对前端领域驱动架构的最大问题。在我的个人项目中，我已经做出了某些有助于减少样板文件的决定，但是我已经认识到，我从编写更少的样板文件中节省下来的东西并不能弥补由于这些决定而引入代码库的不一致性。目前，我解决样板文件问题的方法是根据我的需要创建尽可能多的代码片段和文件模板，它们会为我编写样板文件，这样我就不必这么做了。