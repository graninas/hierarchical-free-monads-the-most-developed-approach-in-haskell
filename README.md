# Hierarchical Free Monads: The Most Developed Approach In Haskell
# _**(And The Death Of Final Tagless)**_

### Abstract

This article is for those who are searching for approaches in Haskell to create real software. The article describes the approach known as Hierarchical Free Monads (HFM), shows why it's better than other approaches, and how the approach was used to drive several businesses. The article also provides a technical perspective on HFM in comparison to Final Tagless / mtl and demonstrates how to solve typical tasks with it. Additionally, the article debunks several widespread myths about Free Monads.

There are also philosophical digressions in which I'm trying to explain why Haskell is not yet as popular as it can be, and why we need all that knowledge known as "Software Engineering" to make it more spread.

This article **is not for seasoned haskellers** with a strong opinion and not for those people who consider Software Engineering to be something essentially bad. **They may find I'm exaggerating and overbolizing too much** but this is just the best illustration of how I feel.

### Table of Contents

* [Essay: My Long Path To Real World Haskell](#Essay-My-Long-Path-To-Real-World-Haskell)
* [Philosophy: The State Of Software Design In Haskell](#Philosophy-The-State-Of-Software-Design-In-Haskell)
* [Exposition: Hierarchical Free Monads For Real World](#Exposition-Hierarchical-Free-Monads-For-Real-World)
* [Dive-in: Hierarchical Free Monads](#Dive-in-Hierarchical-Free-Monads)
  - [Nesting Free Monadic Languages](#Nesting-Free-Monadic-Languages)
  - [Boilerplate](#Boilerplate)
  - [A Unified Design](#A-Unified-Design)
  - [Composition And Explicit Effects](#Composition-And-Explicit-Effects)
  - [Extensibility](#Extensibility)
  - [Expression Problem](#Expression-Problem)
  - [Declarative Semantics](#Declarative-Semantics)
  - [Exception Handling](#Exception-Handling)
  - [Resource Management](#Resource-Management)
  - [Testability](#Testability)
  - [Performance](#Performance)
* [Conclusion](#Conclusion)
* [Farewell word](#Farewell-word)

# Essay: My Long Path To Real World Haskell

My professional path started with C++. It was a beautiful time. I was young, C++ was simple, the future seemed to be bright. I felt like I was on the crest of the wave with the most useful and powerful language out there. I was a wizard of C++, I could create any possible application with it, and I thought there was nothing I could learn because I knew everything.

And then Haskell has come.

I met Haskell when reading some article, and I immediately remembered I've seen this name earlier in the discussions. People talked about Haskell like something unusual, beautiful, even transcendental. I haven't paid that much attention to it until this article. And when I read it, - I didn't understand it, not a single word. But I was trapped already.

Short story long; the first week with Haskell turned me into a Haskell addict. I fell in love, and the bright future with C++ became now broken. Unfortunately, it took a long eight years before I started writing in Haskell for money.

But before this finally happened, during all those dark stone ages with C++, I was playing with Haskell and discovered a great drama in it.

I was learning Haskell, I was advancing towards the steep curve of new concepts, and I was very curious how people write real programs in Haskell. I started asking questions, but no one seemed to be understanding me and what I'm asking about.

To that moment, I've built a strong experience in Software Engineering with OOP languages, such as C++ and C#, I've learned the need of the software design principles, I had a nice understanding of how to create testable software, and even all those patterns and approaches could lead me to the final goal of real world development: a software that solves the business' problems.

But it seemed haskellers weren't interested in things like this. Haskellers looked more intelligent and more smart than me because they were able to juggle crazy types, categories, lemmas and coyonedas, and they did everything to get me thinking I'm too amateurish here. Like while having those crazy types and Math, I don't need to care about that ugly mainstream nonsense anymore. What I actually need is to learn this new world of superior concepts just to have a chance to get into Heaven. I believed them, - if I'd only had a choice!

It was my biggest mistake.

Unfortunately, this delayed my Haskell career for several years. When I realized my mistake I began working secretly on my hobby projects in Haskell. I was unsure but it started looking like I do need the approaches to build real software in Haskell. I do need to make it testable, and the property based testing does not satisfy all my needs. I do want to make my code highly decoupled, and I do want to make it layered, with all the details hidden behind some interfaces. I really need a way to do Inversion of Control and Dependency Injection in Haskell. Am I asking too much?

It became clear to me that there was the Boötes Void, The Great Nothing, the knowingly unlearnt lesson in the Haskell world. A terra incognita the community was ignoring completely. I'm not sure if it was a willful blindness or just a high focus on the more interesting academic stuff, but there was a stone, there was a mountain, and there was no person who could call himself Sisyphus.

And I rolled up my sleeves. It was 2015 when I started my research of Software Engineering in Haskell, and in 2016 I wrote the first line for my future book [Functional Design and Architecture](https://graninas.com/functional-design-and-architecture-book/). A long path of reinventing engineery things in the FP setting was waiting for me ahead... To be honest, this wasn't an easy path because I placed myself into the opposition to the main dogmas of the community. It's not only the idea of having practices, patterns, methodologies and approaches was ["too simple"](https://www.simplehaskell.org/), ["juniorish"](https://www.parsonsmatt.org/2019/12/26/write_junior_code.html) and ["boring"](https://www.snoyman.com/blog/2019/11/boring-haskell-manifesto), but the very tool I found to achieve this - Free monads - has been already diminished and dogmated. I had to fight with the myths spread by influential articles from respected folks. But it was a pure luck for me because I managed to see what all others couldn't.

This is how I developed a methodology I call FDD, "Functional Declarative Design". I took the raw idea of Free Monads and showed how it's suitable for Software Design in Haskell. I found a way to work with Free monads easily without going too deep into theoretical concepts. I invented an approach I call Hierarchical Free Monads which is much better than other approaches in Haskell. I wrote a whole [book](https://graninas.com/functional-design-and-architecture-book/) about it, gave many [talks](https://graninas.com/talks-eng/) and proved Free Monads are fast, reliable, simple and expressive. I created several useful frameworks and technologies with Hierarchical Free Monads. I successfully tested this approach in production, many times.

And today I can say: Hierarchical Free Monads is the most developed, documented and rewarding approach in Haskell nowadays.

# Philosophy: The State Of Software Design In Haskell

You might have heard that the biggest thing haskellers value is Math: Category Theory, Abstract Algebra, Lambda Calculus, advanced type systems. You might have even heard the opinion that mainstream developers just aren't proficient in Math and this is why they have "Software Engineering" which is no more than just a rough substitution of it. So the discipline called "Software Engineering" is not applicable, is dirty and unwanted, they say. Why should we put our effort into methodologies and other mind flaws the outside industry has?

And I'm not exaggerating. Haskellers consider their language as something special, unique, blessed and therefore standing out of those strange agreements the outer world managed to build. This is clearly not true. Although Haskell is kinda a unique language (what language is not?), it's no way different in the design space. Software Design is about how to create a working software with low risks. How to make it reliable, maintainable, satisfying requirements. And certainly, how to achieve industry's main goals: a ready product that solves real problems and can produce money. Software Engineering helps the industry to not invent things again and again, to not start from scratch, to not spend more time for curiosity than it's actually needed. There should be several well established ways to do all the usual things: web apps, backends, frontends, command line apps, machine learning apps. This makes the development much cheaper and allows for less proficient developers who can be unfamiliar with all the high-level concepts but still will be able to solve business problems.

In this regard, Haskell was lacking high-level approaches and ideas composed into a complete methodology of Software Design. At least until recent years. Yes, sure, we have the mtl approach for a decade or so. We also call it Final Tagless, and it's the most popular approach to write code... But from the design point of view, it doesn't really satisfy the requirements the industry has. For example, its testability is very low, and its complexity is very high, especially if we're trying to incorporate some advanced library with massive type level tricks involved. The problem with mtl/FT is not in its idea but in how it's done. It doesn't allow you to separate layers completely, it forces you to use advanced type level features (like type classes, type equality, type families), and doesn't provide any good way to express the semantics other than just "immediate evaluation". It has many other flaws, and it's a leaky abstraction. I wouldn't recommend using FT for big codebases.

Maybe, dissatisfaction by Final Tagless was the main reason why different effect systems were born. Haskellers were searching for a way to describe effects in a more explicit and more algebraic way, so they started putting effects into lists of types and then interpret this list somehow. Roughly, this is how all the extensible effects work. However, it turns out that working with such a code is no pleasure, and the bigger your codebase, the more boilerplate you have to deal with. Explicit lists of effects are maybe very granular, but this is no way an advantage because it doesn't bring any extra safety but represents a major obstacle to refactoring. This approach is unmerciful. If you made a mistake in your list of effects, or you forgot some effect, you'll end up with a huge unreadable compile error about something completely unrelated. Something about type level magic rather than about your business logic. Effect systems bring too much accidental complexity in the code; perhaps this was a reason why PureScript developers removed a similar system from the language. It turned out that [effect tracking is commercially worthless](https://degoes.net/articles/no-effect-tracking).

Effect systems may look cool and interesting on the first sight. Complete correctness! Explicit declarations of effects! Mathematical foundations! Smart type level magic to play with!.. But Software Engineering is not about cool things, and we should not follow the Cool Thing Driven Development methodology if we want to keep the risks low. To our benefit, different other approaches have been discovered and investigated, and the whole idea of Software Engineering in Haskell became more respected.

You might ask, what else you can use for software design in Haskell if not Final Tagless or effect systems? Well, there are different approaches out there:

* Final Tagless (FT) / mtl
* Effect Systems and Freer Monads
* ReaderT pattern
* Service Handle pattern
* Free Monads
* Hierarchical Free Monads (HFM)

To help with that, I collected a list of materials about Software Design in Haskell and composed a comparison table on these approaches. You can also check out my talks and presentations to get a better understanding on what I'll be talking about in the next sections. And for sure, you can read my book to ensure that the complete methodology of building software in Haskell now exists.

* [Software Design in Haskell (list of materials)](https://github.com/graninas/software-design-in-haskell)
* [Opinionated comparison table of different approaches](https://gist.github.com/graninas/1b7961ccaedf7b5cb92417a1599fdc99)
* [Functional Design and Architecture (book)](https://graninas.com/functional-design-and-architecture-book/)
* [Hierarchical Free Monads and Software Design in Functional Programming (Talk)](https://youtu.be/3GKQ4ni2pS0) | [Slides](https://docs.google.com/presentation/d/1SYMIZ-LOI8Ylykz0PTxwiPuHN_02gIWh9AjJDO6xbvM/edit?usp=sharing) 
* [Final Tagless vs Free Monad (Talk, Rus)](https://youtu.be/u1GGqDQyGfc) | [Slides (Eng)](https://drive.google.com/open?id=1VhS8ySgk2w5RoN_l_Ar_axcE4Dzf97zLw1uuzUJQbCo)

And now let me prove to you that Hierarchical Free Monads is the best approach to build real software in Haskell.

# Exposition: Hierarchical Free Monads For Real World

So what is the approach I call "Hierarchical Free Monads" and how it's different from regular Free Monads? Well, it's just an idea that Free Monadic languages can be nested. It's a simple idea, but it allows you to go very far in the designing of your applications.

I proposed a design for several frameworks using HFM as a core technology. Usign this design, we implemented these projects:

- [Juspay's PureScript Presto](https://github.com/juspay/purescript-presto) - a framework for building mobile apps using a handy eDSL.
- [Juspay's PureScript Presto.Backend](https://github.com/juspay/purescript-presto-backend) - a framework for web RESTful backends. This framework was initially created by my colleagues using the design of Presto. It has the same design as Presto but also is empowered by some additional features like logging, HTTP APIs integration, KV DB, SQL DB subsystems, state handling. I participated in its further development and added a technology called [automatic whitebox testing](https://github.com/graninas/automatic-whitebox-testing-showcase).
- [Juspay's EulerHS](https://github.com/juspay/euler-hs). A Haskell framework for building web services and RESTful backends. A Haskell counterpart to PureScript Presto.Backend. It has own interesting properties though, for example, support of SQLite, MySQL and Postgres out of the box.

All these frameworks drive Indian financial company [Juspay](juspay.in). Its financial services are written on top of Presto, Presto.Backend and EulerHS. There are new projects completely based on EulerHS. This technology helped the company to grow up and enabled a wide adoption of PureScript and Haskell in the future. Essentially, Juspay was the first company I used my ideas in. We found that HFMs hide all the complexity of implementation details behind convenient interfaces so that the code is understandable by not only developers but by managers as well. This helped us to easily reason about the domain, the logic, the business goals and requirements. My approach also brought confidence that Haskell and PureScript are not only academic languages.

- [Enecuum's Node](https://github.com/graninas/Node) - a full-fledged framework for building distributed, concurrent, multithreading apps, blockchains initially, but not only. I designed this framework for [Enecuum](https://enecuum.com/) and then we successfully created our own blockchain using it. What's important, we managed to achieve our goals less than in 4 months with a team of 4 haskellers. The framework allowed us to write fast, complicated blockchain logic easily, quickly, without bugs and with maximum confidence due to a great testability of the HFM approach. You can read more about the framework in this my article: [Building network actors with Node Framework](https://gist.github.com/graninas/9beb8df5d88dda5fa21c47ce9bcb0e16).

- [Hydra](https://github.com/graninas/Hydra). This is a special story here. Hydra is not only a full-fledged Free Monadic framework to build web and CLI apps, it's also a showcase project for my book. This project is aimed to show the differences between approaches, and it currently provides 3 engines: Free Monads, Church Encoded Free Monads and Final Tagless. The framework allows you to easily write RESTful apps (with [servant](https://hackage.haskell.org/package/servant)) having SQL DB (with [beam](https://hackage.haskell.org/package/beam)), KV DB support, multithreading, with logging, concurrency etc. It also provides a testing framework for your apps. Hydra is the further development of the HFMs approach and highly resembles the design of the Node framework.

When I started my book, I couldn't even imagine the ideas I developed would give a light to such an interesting project. Unfortunately, Free Monads suffer from a bad reputation nowadays. In the next section I'll explain many technical aspects and destroy several myths lurking around the approach.

# Dive In: Hierarchical Free Monads

### Nesting Free Monadic Languages

Consider the following two Free monadic languages: a language for logging `Logger`:

```haskell
-- Algebra (interface) for the LoggerL Free monadic language with only 1 method
data LoggerF next where
  LogMessage :: LogLevel -> Message -> (() -> next) -> LoggerF next

-- Functor instance needed for the Free machinery
instance Functor LoggerF where
  fmap f (LogMessage lvl msg next) = LogMessage lvl msg (f . next)

-- Free monadic language
type Logger a = Free LoggerF a
```

And a top language `App` which contains `Logger` as a sublanguage:

```haskell
data AppF next where
  EvalLogger :: Logger () -> (() -> next) -> AppF next

instance Functor AppF where
  fmap f (EvalLogger logAct next) = EvalLogger logAct (f . next)

type App a = Free AppF a
```

This is the whole idea. You put one language into a method of another language making a hierarchy of languages. The interpreters will also follow this hierarchy structure. Let's do a short inspection. One for the top language:

```haskell
-- Interpreting function
interpretAppF :: AppF a -> IO a
interpretAppF (EvalLogger loggerAct next) = do
  runLogger loggerAct      -- nested interpreter call
  pure $ next ()

-- Interpreter entry point
runApp :: App a -> IO a
runApp = foldFree interpretAppF
```

And one for the nested language:

```haskell
-- Simple console logger
interpretLoggerF :: LoggerF a -> IO a
interpretLoggerF (LogMessage lvl msg next) = do
  putStrLn msg
  pure $ next ()

runLogger :: Logger a -> IO a
runLogger = foldFree interpretLoggerF
```

For an easier usage, it's better to define a convenient interface for the `App` language. You can do this with several smart constructors:

```haskell
-- Log message with Info level.
logInfo :: Message -> App ()
logInfo msg = evalLogger (logMessage Info msg) id

-- Helper function to wrap LoggerF method
logMessage :: Level -> Message -> Logger ()
logMessage lvl msg = liftF $ LogMessage lvl msg id

-- Helper function to wrap AppF method
evalLogger :: Logger () -> App ()
evalLogger logger = liftF $ EvalLogger logger id
```

Suppose we want to add a new feature - getting a random value - into this small HFM framework. It's easy. The updated language will look like this:

```haskell
data AppF next where
  GetRandomInt :: (Int, Int) -> (Int -> next) -> AppF next
  EvalLogger :: Logger () -> (() -> next) -> AppF next

instance Functor AppF where
  fmap f (GetRandomInt range next) = GetRandomInt range (f . next)
  fmap f (EvalLogger logAct next)  = EvalLogger logAct (f . next)

getRandomInt :: (Int, Int) -> App Int
getRandomInt range = liftF $ GetRandomInt range id

-- Updated interpreter:
interpretAppF :: AppF a -> IO a
interpretAppF (EvalLogger loggerAct next) = next <$> runLogger loggerAct
interpretAppF (GetRandomInt range next)   = next <$> randomRIO range
```

Just a line here, a line there...

Stop. Wrapper functions, Functor instance, smart constructors, interpreters? So much boilerplate, huh? What's the point then? Whether this is just a waste of time and effort? Well, yes but actually no. It's not boilerplate.

### Boilerplate

Boilerplate is not a code that you have a possibility to automate somehow. A code once written and no longer touched, - is not boilerplate. Boilerplate is a code you have to write many times in your day-to-day practice, or a repeating code which pursuits imaginary goals. Boilerplate makes it hard to achieve more real and more important goals and becomes a big obstacle for refactoring. This is why Functor instances, interpreters and smart constructors are not boilerplate. You don't really need to automate it (although it's possible to generate Functor instances with Template Haskell). If you are about to introduce a new method then adding a new line into the Functor instance is a matter of seconds. It's about 0.01% of your time. Instead of automating this it's better to focus on something more valuable. Tests or business logic for example.

Although Free Monads require some work on the language definition and implementation levels, this approach doesn't bring extra boilerplate into the business logic layer. This is good because boilerplate in the business logic costs much more. Let's compare this for FT and HFM.

Simple scenario in this small HFM framework:

```haskell
printRandomFactorial :: App ()
printRandomFactorial = do
    n <- getRandomInt (1, 100)
    logInfo $ show $ fact n
```

The same scenario with FT:

```haskell
printRandomFactorial :: (Random m, Logger m) => m ()
printRandomFactorial = do
    n <- getRandomInt (1, 100)
    logInfo $ show $ fact n
```

While the bodies of the two functions are pretty much the same there is a significant difference in the function definitions. FT requires you to specify a list of constraints. The more effects you have the more constraints will be there. Normally, business logic of a regular web service consists of dozens if not hundreds functions, and typing this kind of boilerplate makes coding extremely annoying. It doesn't buy anything useful. Absolute correctness is not required, and moreover, cannot be achieved. Documenting the effects doesn't make the code clearer. Fine structuring of the project has more impact on the code clearness. Good project organization will allow you to know what effects are used there by just looking into the namespace (like, `app/Product/Storage/Queries` or `app/Server/API`). List of effects/constraints is not a tool for layering, effects can contradict to the namespaces, and in general it is a redundant boilerplate for no real purpose, "just in case".

It's worth to note that there is an approach for FT to overcome this (described [here](https://serokell.io/blog/tagless-final)). To avoid explicit effects list you can wrap it into a single type, adopt the `AppM` pattern and live happily... Or not. There are other problems with FT which make it very hard to live happily. Let's discuss how we can design our programs with FT and HFM.

### A Unified Design

*"With FT you have freedom. You can incorporate an effect whenever you want to do it. You're not limited by someone's opinionated design. It's so easy, and so cool to compose effects. The HFM approach prohibits that. It's too rigid. All the effects should be specified in the algebra. Developer of this framework makes decisions instead of you. He limits your creativity. Are you really suggesting this? Are you really suggesting to restrict the freedom?"*

Yes, I'm really suggesting this. There are several reasons.

* Freedom is not free. Allowing business logic developers to incorporate arbitrary effects into arbitrary places will lead to a mess. Trusting to your developers is a good idea, but providing a unified design for the whole code is the practice one cannot refuse. Freedom of FT is too risky. Being opinionated is not bad, it's a way to decrease risks and to make the development cheaper. This is what HFM does and this is why it's very practical.
* HFM provides a unified design for all the applications by default. This design is also known as [3-layered cake](https://www.parsonsmatt.org/2018/03/22/three_layer_haskell_cake.html). HFM helps to separate concerns much better than FT. Language definition, interpreters and business logic represent own layers with clear responsibilities and precise boundaries. This is naturally a way to follow many Software Design principles: Interface Segregation, Single Responsibility, low coupling / high cohesion etc.

Violation of these principles seems to be embedded into FT. For example, in FT effects are usually peppered by implementation details too much. These details are coming from the native libraries and leak into the business logic. No good abstraction, no separation of concerns. Guts are exhibited and accessible for all curious people.

Let's investigate a simple case. The following code contains too much info about the logger implementation:

```haskell
printRandomFactorial :: (Random m, WithLog SomeLogEnvironment String m) => m ()
```

This makes you think about the details a lot, but why should you? With HFM, you only know you have a logging interface (i.e, this method: `logInfo :: Message -> App ()` ), and you don't need to care about how it works under the hood. FT doesn't allow easy abstractions. HFM does it graciously. You can provide a good interface over some effect, and this interface won't expose the implementation details. Business logic will be decoupled from the raw libraries. All the native libraries will go to the interpreters. You can even substitute your implementations on the fly, - which is not easily possible with FT.

There are even more reasons why FT violates core design principles. It also does a bad job in keeping complexity low. In other words, it fails the main task of Software Design.

### Composition And Explicit Effects

_"FT enables simple composition of effects. Such a cool idea - composing the effects! And what about HFM? It forces you to update the framework once some new effect becomes needed. Is this even viable? Whether the composition is the essence of Functional Programming and you just threw it to the trash?"_

Another point of FT proponents is about composability. However there is no value in composition itself. As well as there is no value in lambdas, high order functions, types, type classes and other features of the language. We're here not to admire the language. We're here to solve real problems and achieve business goals, and we should be very careful in choosing the tools.

There is no value composing effects. There is value in controlling effects. Specifications in the FT are very like when you place a mark `BUG: fix me!` near a bug. Do you really control it? Nope. The bug is still there. Explicit lists of effects is just a needless dancing around a landing strip in order to summon the complete correctness.

### Extensibility

*"But FT is extensible. Your HFM is not. Huh?.."*

True, FT is easy to extend, but by what cost? Let's see. This is how we can add an effect in FT:

```haskell
-- Then:
-- printRandomFactorial :: (Random m, WithLog SomeLogEnvironment String m) => m ()

-- Now:
printRandomFactorial :: (Random m, Database m, WithLog SomeLogEnvironment String m) => m ()
```

However once it's done, a lot of code should be updated. If the outer function had a call to `printRandomFactorial`, it's affected now. And all other functions up to the top of the call stack.

```haskell
-- Then:
-- printFactAndFib :: (Random m, WithLog SomeLogEnvironment String m) => m ()

-- Now:
printFactAndFib :: (Random m, Database m, WithLog SomeLogEnvironment String m) => m ()
printFactAndFib = do
  printRandomFactorial
  printRandomFibonacci
```

Let's try to see how this will look with the `AppM` approach (pseudocode; see full description [here](https://serokell.io/blog/tagless-final)):

```haskell
newtype AppM a = AppM { runAppM :: ReaderT Env IO a }
  deriving (Functor, Applicative, Monad, Random, WithLog SomeLogEnvironment String)

class (WithLog SomeLogEnvironment String m, Random m) => Lang m
instance Lang AppM

printFactAndFib :: (Lang m) => m ()
```

Solution works but costs too much. It brings a lot of accidental complexity and involves some extra features from the language. Besides that, throwing in new effects is still possible:

```haskell
printFactAndFib :: (Lang m, MonadIO m) => m ()
```

Compare these movements with a simple `App` from the Free monadic language:

```haskell
printRandomFactorial :: App ()
printRandomFibonacci :: App ()
printFactAndFib :: App ()
```

This code is very simple. Adding new ~~effects~~ subsystems into the language itself won't break any code. There is no overengineering in the form of smart tricks on the type level. This is why the HFM approach is better for designing software: it keeps accidental complexity low which is orders of magnitude more important then extensibility.

And in case you truly need some extensibility without changing the Free monadic framework, you can add a method that makes introducing new effects outside the framework possible:

```haskell
data AppF next where
  RunIO :: IO a -> (a -> next) -> LangF next

runIO :: IO a -> Lang a
runIO ioAct = liftF $ RunIO ioAct id
```

Now you can implement some additional subsystem using this method:

```haskell
import qualified SQLite as SQLite

runSQLiteQuery :: String -> Lang (Either Error SQLite.Rows)
runSQLiteQuery query = runIO $ SQLite.runQuery query
```

True that this method is somewhat dangerous, but it's still under control. You can disable it by a config for your framework, you can add a trace message into the interpreter, you can even handle exceptions from the `ioAct`.

```haskell
interpretAppF :: AppF a -> IO a
interpretAppF (RunIO ioAct next) = do
  eResult <- try ioAct
  case eResult of
    Left (err :: SomeException) -> ... -- do something with error
    Right res -> pure res
```

And you know what? It's much more safe than the `MonadIO` effect (or `MonadUnliftIO`) that is barely avoided in the FT codebases.

### Expression Problem

*"But you didn't solve the Expression Problem!"*

Well, you're right. With FT the Expression Problem is kinda solved. But we're not paid for solving expression problems, we're paid for solving business problems. Sometimes extensibility is a requirement, and sometimes it's not. My experience shows that the number of core subsystems rarely exceeds 10. Maybe 15. Some of these subsystems can be implemented on the start, some of them are fine to add lately. You always know what you do and why you do this. You design an interface for a subsystem, you think about its usage, you plan the abstraction over impure calls, you investigate the behaviour of the native library, you test your integration, you deliver a new feature that can be used in the business logic now. This is how Software Development works.

...And BTW, the outer world is not aware about the term "Expression Problem", at all. Although haskellers love it and love to solve the Expression Problem, it's not a primary goal for industrial development.

### Declarative Semantics

Interestingly, Hierarchical Free Monads allow to express a way bigger range of behavioral patterns. Besides a "traditional" imperative style (like methods `logInfo` and `getRandomInt`) it's possible to have a declarative style with syntax best suitable for a specific case. Declarativity and introspection of Free Monads opens doors into a whole new world of various semantics.

To clarify what I mean, let's imagine there is a requirement to create a CLI interactive application. In Haskell, you can take an interesting library [haskeline](https://hackage.haskell.org/package/haskeline) for this, but how to incorporate it into your code without breaking the whole design? With HFM, there is a nice solution. Consider the following business logic code (this code is a part of the [Labyrinth game](https://github.com/graninas/Hydra/tree/master/app/labyrinth) from the Hydra framework):

```haskell
app :: GameState -> AppL ()
app st = do
  scenario $ putStrLn "Labyrinth (aka Terra Incognita) game"
  
  cliToken <- cli (onStep st) onUnknownCommand $ do    -- declaring a CLI interactive interface
    cmd "go up"    $ makeMove st DirUp                 -- supported commands with handlers
    cmd "go down"  $ makeMove st DirDown
    cmd "go left"  $ makeMove st DirLeft
    cmd "go right" $ makeMove st DirRight
    
    cmd "quit"     $ quit st

  awaitCliFinished cliToken
```

What do you see here? There is a declarative `cli` method from the framework. It states there should be an interactive subsystem with such commands. Each command is tied to a handler to be called. When the user starts this program, he will see the prompt:

```
$ stack exec labyrinth

Labyrinth (aka Terra Incognita) game
> go right
step executed.
> quit
Bye-bye
```

The Hydra framework has a bit more complex hierarchical structure than the HFM language in this article. There are several layers of Free monadic languages:

`AppL` <- `LangL` <- `various core effects`

In the sample above, the `app` function is an entry point into the game, and the `cli` method works within the `AppL` monad. In turn, the `cli` method takes handlers operating one level down, within the `LangL` monad. Take a look at the definition:

```haskell
makeMove :: GameState -> Direction -> LangL ()

quit :: GameState -> LangL ()
```

These handlers can do all the core effects: logging, state handling, working with SQL and KV DB and so on. But it's impossible to run another `cli` subsystem because it's a responsibility of the `AppL` monad only.

Another declarative subsystem in Hydra allows it to work with processes (forked flows). Again, the only `AppL` layer has a right to spawn processes (see [Meteor Counter](https://github.com/graninas/Hydra/blob/25b820af45e289a1fe5bb66e9137f0e88215b00d/app/MeteorCounter/Free.hs#L79) app):

```haskell
-- Process ofr counting meteors 
meteorCounter :: AppState -> LangL ()
meteorCounter = ...

-- Application definition
meteorsMonitoring :: AppConfig -> AppL ()
meteorsMonitoring cfg = do
  st <- atomically $ initState cfg
  
  process $ forever $ meteorCounter st
  process $ forever $ withRandomDelay st $ meteorShower st NorthEast
  process $ forever $ withRandomDelay st $ meteorShower st NorthWest
  process $ forever $ withRandomDelay st $ meteorShower st SouthEast
  process $ forever $ withRandomDelay st $ meteorShower st SouthWest
  
  ...
```

This specific separation might be debatable, because you can't spawn threads in the `LangL` methods even if you want to. Still it's kinda possible by organizing your app so that the `AppL` layer will be waiting for signals from the `LangL` layer to spawn more processes. This makes the program even more reactive and declarative.

Let me present to you one more sample of a semantics different than "just imperative evaluation". In my another hobby project, [hinteractive](https://github.com/graninas/hinteractive), an engine for interactive fiction games, Free Monads are used to achieve a kind of reactive syntax for game transitions. Check out this sample of a Zork-like game:

```haskell
-- | West of House location.
westOfHouse :: AGGraph ()
westOfHouse = graph $
  with (westOfHouse' >> getInput)
    ~> on "open mailbox" openMailbox
    ~> on "read leaflet" reading
    /> leaf nop
```

It's hard to believe but the transition operators `~>`, `/>` (and more others: `<~>`, `</>`...) are just symbolic aliases for methods of a specific Free language designed for this task. The game engine is based on several Free Monadic languages interacting to each other so you could express your domain logic in a more illustrative and self-explaining way.

I'm really not sure how to do similar things with FT. Proponents of FT often note that Final Tagless and Free Monads are equivalent in the Math sense. Like, you can easily convert between them and what's the deal then. But while being absolutely correct this argument misses the point completely. The difference is how we use either of the approaches in real tasks. And this difference is dramatic.

### Exception Handling

*"In real tasks you said? But it's impossible to have exceptions with Free Monads! How would you even use Free Monads in production while it lacks such an important feature?!"*

Well, let me share a secret with you. It's a huge and deeply rooted myth. There is no problem to work with exceptions in Free Monads. Moreover, HFM offers a much better approach that eliminates all that complexity around error handling, sync and async exceptions in Haskell.

Firstly, let me show you how to incorporate exception throwing and catching into a Free Monad framework. It's very simple actually. There will be two methods, let's call them `throwException` and `runSafely`:

```haskell
data LangF next where
  ThrowException :: forall a e next. Exception e => e -> (a -> next) -> LangF next
  RunSafely :: Lang a -> (Either Text a -> next) -> LangF next

type Lang a = Free LangF a

instance Functor AppF where
  fmap f (ThrowException exc next) = ThrowException exc (f . next)
  fmap f (RunSafely act next)      = RunSafely act (f . next)

throwException :: forall a e. Exception e => e -> Lang a
throwException ex = liftF $ ThrowException ex id

runSafely :: Lang a -> Lang (Either Text a)
runSafely act = liftF $ RunSafely act id
```

Notice how we're nesting the `Lang` scenario recursively into the `RunSafely` method. And now you can throw exceptions in your scenarios as well as catching them:

```haskell
data AppException = InvalidOperation Text
  deriving (Eq, Ord, Show, Generic, Exception)

unsafeScenario :: Lang Int
unsafeScenario = do
  val <- getRandomInt (1, 90)
  case () of
    _ | val <= 30 -> pure 0
      | val <= 60 -> pure val
      | otherwise -> throwException $ InvalidOperation "Failed with 1/3 chance"

safeScenario :: Lang ()
safeScenario = do
  eVal <- runSafely unsafeScenario
  case eVal of
    Left err  -> logError $ "Exception got: " <> err
    Right val -> logInfo  $ "Value got: " <> show val
```

The main work is done by the interpreter. As there is a nested `Lang` scenario, we can run the `Lang` interpreter recursively and use `try` around it:

```haskell
interpretLangF :: LangF a -> IO a
interpretLangF (ThrowException exc next) = throwIO exc
interpretLangF (RunSafely act next) = do
  eResult <- try $ runLang coreRt act
  pure $ next $ case eResult of
    Left (err :: SomeException) -> Left $ show err
    Right r  -> Right r

runLang :: Lang a -> IO a
runLang = foldFree interpretLangF
```

If the nested scenario throws an exception, the latter will be caught here. It can be an exception produced with the `throwException` method, or an exception made by the standard `error` function, or even an exception coming from the interpreters of other subsystems. For example, the following code is trying to connect to a DB. It will be safe irrespective whether the implementation of the `runIO` method catches the exceptions or not:

```haskell
unsafeIOScenario :: Lang ()
unsafeIOScenario = runIO $ do
   conn <- SQLite.connect sqliteCfg                          -- can throw
   SQLite.query "INSERT INTO students VALUES ('John Doe')"   -- can throw
   error "Oops"                                              -- throws

safeScenario :: Lang ()
safeScenario = void $ runSafely unsafeIOScenario
```

The `runSafely` can catch all normal sync exceptions. Thanks to the great abstracting power of the Free Monad, we can wrap all the dependencies like raw DB libraries, logging libraries, networking libraries into our own languages. Making an abstracted interface to a subsystem simplifies the interaction with the subsystem but also it allows to safely handle its exceptions and turning them into values. See this sample of an HTTP API interaction language from the Hydra framework:

```haskell
interpretLangF coreRt (L.CallServantAPI bUrl clientAct next)
  = next <$> catchAny
      (S.runClientM clientAct (S.mkClientEnv (coreRt ^. RLens.httpClientManager) bUrl))
      (pure . Left . S.ConnectionError)
```

In your business logic scenarios, you don't see these implementation details, you just use a handy safe method that returns `Left err` on case of something went wrong:

```haskell
callServantAPI :: BaseUrl -> ClientM a -> LangL (Either ClientError a)
callServantAPI url cl = liftF $ CallServantAPI url cl id
```

With HFM, you get a nice separation of concerns. The code is now divided into error domains, and each part of it deals with its own type of problems. Interpreters handle native exceptions. Languages convert unsafe code into safe one. Scenarios can work with business-specific errors and exceptions. Exception and error handling is a very hard theme, and there is no reason to increase accidental complexity of the code beyond this. I am horrified by the thought that I'm one to one with this problem if I decide to use FT. The practices we elaborated  (see [1](https://markkarpov.com/tutorial/exceptions.html), [2](https://www.fpcomplete.com/blog/2016/11/exceptions-best-practices-haskell)) don't seem to be simple, convenient and handy. You know, all those `MonadThrow`, `MonadMask`, `MonadCatch`, `Exception` and other clunky monsters. There is literally no good way to deal with errors in FT. All the approaches are broken by design and cannot be fixed.

### Resource Management

Have you heard about a practice from mainstream development which is called **RAII**? RAII stands for _Resource Acquisition Is Initialization_. This practice is very important as it simplifies resource management significantly. In such languages as C++ or C# (or even in Python), RAII can be used to control lifetimes of file handles, memory, threads and other resources. Instead of manually managing a resource, you bind its lifetime to, for example, a lifetime of a class, and the resource will be released when the object of this class is destroyed. Another popular idea for RAII is to have a scope that is the only place where a resource can be accessed. Once the control flow leaves this scope, the resource is destroyed.

The Haskell's `bracket` pattern is a form of RAII as well. I've heard that with Free Monads, it's impossible to implement such semantics. Either at all, or in a safe manner. But actually this function can be directly encoded as a method of a framework:

```haskell
data LangF next where
  IOBracket :: IO a -> (a -> IO b) -> (a -> IO c) -> (c -> next) -> LangF next

ioBracket
  :: IO a        -- computation to run first ("acquire resource")
  -> (a -> IO b) -- computation to run last ("release resource")
  -> (a -> IO c) -- computation to run in-between
  -> LangL c
ioBracket acq rel act = liftF $ IOBracket acq rel act id

-- Interpreter
interpretLangF (IOBracket acq rel act next) = next <$> bracket acq rel act
```

As it's implemented via `bracket`, it behaves similarly. Notice, the IO actions are used there. However it might be needed to run your `Lang` scenarios to work with the resource. This is finely doable:

```haskell
data LangF next where
    WithResource :: IO a -> (a -> IO b) -> (a -> Lang c) -> (c -> next) -> LangF next

interpretLangF (WithResource acq rel act next) = next <$> bracket acq rel (\r -> runLang $ act r)
```

No changes are needed if you need a kind of `bracket` within the `Lang` environment. Just encode it like this:

```haskell
langBracket :: LangL a -> (a -> LangL b) -> (a -> LangL c) -> LangL c
langBracket acq rel act = do
  r <- acq
  a <- act r
  rel r
  pure a
```

The code of `langBracket` isn't exactly like the `bracket` function and it's not completely safe in the event of a presence of `throwException` and `runSafely` methods. But with using these functions it's for sure possible to express all the resource handling combinators from the [Control.Exception](https://hackage.haskell.org/package/base-4.12.0.0/docs/Control-Exception.html) module.

One more interesting aspect of resource handling with Free Monads is closely corresponding to how we do it in the mainstream languages. The architecture based on Free Monads is divided into three layers: business logic, interface eDSLs and implementation. The latter layer includes not only interpreters but also runtime structures, resources and data hidden from the two other layers. Free Monadic scenarios should be run on top of this runtime, and it's possible to track different resources there. For example, the Hydra framework has the following runtime structure for keeping DB connections, threads, raw variables and other resources:

```haskell
-- | Runtime data for core subsystems.
data CoreRuntime = CoreRuntime
    { _rocksDBs          :: R.RocksDBHandles
    , _loggerRuntime     :: LoggerRuntime
    , _stateRuntime      :: StateRuntime
    , _processRuntime    :: ProcessRuntime
    , _sqlConns          :: MVar (Map D.ConnTag D.NativeSqlConn)
    }

-- Interpreters can use CoreRuntime to store different things.
interpretLangF :: R.CoreRuntime -> L.LangF a -> IO a
interpretLangF coreRt (L.EvalLogger loggerAct next) = ...

runLangL :: R.CoreRuntime -> L.LangL a -> IO a
runLangL coreRt = foldFree (interpretLangF coreRt)
``` 

Some of the runtime values drive the framework and do not appear in the business logic somehow (like the logger handler of a specific logging library), some other values have an 'avatar'-like representation in the business logic. For example, the STM subsystem in the Hydra framework is wrapped into own abstraction eDSL, the `StateL` Free Monadic language (see [here](https://github.com/graninas/Hydra/blob/master/src/Hydra/Core/State/Language.hs)). The business logic code isn't allowed to interact with the native `STM` subsystem directly, but it can use `StateVar` and the `StateL` abstraction to do this safely and under close control. With this approach, you can easily introspect your STM variables, you can print your variables at any moment of application run. You can even establish some limits for your system by tracking how many STM variables have been created.

All these and several other techniques the HFM approach provides help to manage resources easily, without adding any extra accidental complexity into the business logic code. This is drastically important for big code bases within the industrial setting.

Now let me ask you a philosophical question. Why do all the articles about Free Monads say that handling exceptions and managing resources is not possible or quite limited compared to FT? The cause maybe is that a theoretical reasoning about Free Monads as a Math object has nothing to do with an engineery way of solving problems. Sometimes hacky, sometimes dirty, - engineery solutions help us to achieve business' goals, and we don't really need to chase a complete Math-like correctness all the time.

### Testability

Historically, Haskell developers tended to idolize property-based testing. Although this approach is good it follows the idea that there are some immanent properties you could test. This might be true for pure algorithms and small programs but once you step to the ground of usual, IO-bound applications, the property-based testing becomes less useful. It's rarely a set of algorithms. More often applications like web-services are a bunch of interactions with external services: databases, HTTP services, filesystems. Extracting some internal properties (better to say invariants) from these scenarios is not an easy task. This is why other testing approaches have been invented. Integration testing is such.

It's indeed important to test applications with integration tests. These tests can spot many problems with the code and its behavior. We can even say integration tests are the most useful from others. This is true however setuping integration tests is not easy, and they can be very slow and fragile. And here is the question: how to still test the behavior and not suffer from problems with integration tests? Software Engineering has an answer: mocking. Yes, you've heard it right - mocking, - in our lovely Haskell. Even in Haskell we need the practices the mainstream development has.

Here, a problem ocurred. The Final Tagless architecture seems to be very inconvenient for this. When a subsystem (or effect if you wish) is represented as a type class, mocking then effectively means there should be a special mocking type instead of a real one. Let's say there is a type class for logging and an interpreter for an actual working monad like this:

```haskell
class Logger m where
  logInfo :: Level -> Message -> m ()
  
newtype AppM a = AppM { runAppM :: ReaderT Env IO a }
  deriving (Functor, Applicative, Monad, MonadIO)

instance Logger AppM where
  logInfo _ msg = liftIO $ putStrLn msg
```

This means you should create another "base monad" class for your testing environment, which is no longer the same as the live one:

```haskell
newtype TestM a = TestM (IO a)
  deriving (Functor, Applicative, Monad, MonadIO)

instance Logger TestM where
  logInfo _ _ = pure ()
```

It's certainly possible to build additional mechanisms for mocking and more or less fine functional testing, but the classy essence of FT doesn't allow to do this with the same level of convenience as Free Monads do. I would even say Free Monads here outperform all other approaches.

Let's investigate a short sample on how to organize a small mocking framework for the `App` language. To mock `GetRandomInt` calls, we will need a container (let it be list), and a special mocking interpreter will use these values during the interpretation instead of evaluating the actual effect:

```haskell
data RandomValueMocks = RandomValueMocks
  { curVal :: IORef Int
  , vals :: [Int]
  }

interpretAppF' :: RandomValueMocks -> AppF a -> IO a
interpretAppF' mocks (EvalLogger loggerAct next) = pure $ next ()   -- No mocking
interpretAppF' mocks (GetRandomInt range next)   = do
  idx <- readIORef (curVal mocks)          -- getting the next mock
  writeIORef (curVal mocks) (idx + 1)
  pure $ next $ (vals mocks) !! idx

runApp' :: RandomValueMocks -> App a -> IO a
runApp' mocks = foldFree (interpretAppF' mocks)
```

Now it's possible to setup mocks in the tests:

```haskell
-- Program to test
getRandomFib :: App Int
getRandomFib = getRandomInt (1, 100) >>= pure . fib
    
spec :: Spec
spec = describe "Functional tests" $ do
  it "getRandomFib should return 5 for 6th member" $ do
     
     curVal <- newIORef 0                     -- creating mocks
     let mocks = RandomValueMocks curVal [6]
     
     result <- runApp' mocks getRandomFib     -- running the test interpreter
     result `shouldBe` 5
```

Now you see how it's easy to substitute systems by mocking interpreters; even those methods which return something generic can be mocked. Let's say we need to mock the `runSafely` method:

`runSafely :: Lang a -> Lang (Either Text a)`

How would we do this when every occurrence of this method can have its own type and we literally can't place all the mocks into a single structure? Well, there is nothing bad in passing those mocks as GHC.Any's:

```haskell
data RunSafelyMocks = RunSafelyMocks
  { curVal :: IORef Int
  , vals :: [GHC.Any]
  }

interpretAppF' :: RunSafelyMocks -> AppF a -> IO a
interpretAppF' mocks (RunSafely _ next)   = do
  idx <- readIORef (curVal mocks)
  writeIORef (curVal mocks) (idx + 1)
  let any = (vals mocks) !! idx
  
  let val = unsafeCoerce any     -- turning an untyped mock into an appropriate type from GHC.Any
  pure $ next val
```

Here, we used a kind of "dirty hack" to store mocks of different types in the same structure. While `unsafeCoerce` looks too dangerous, this code will be fine if the mocks are formed correctly. This trick is truly an engineering solution: strange, hacky but working.

But you might say this is not that impressive because you can do the same with FT. True. However the fact that Free Monadic languages and interpreters are just values helps to keep the code simple, finely separated and decoupled. You can easily build a design in which you will be able to substitute any subsystem by providing another implementation (interpreter). You can even do Dependency Injection on the fly, - in contrast to Final Tagless. Free Monads do not add any extra complexity into this.

Still not convinced? Then I have a killer feature, ace up my sleeve. I call it ["Automatic White-Box Testing Approach"](https://github.com/graninas/automatic-whitebox-testing-showcase).

What is this? It's a way to record your Free Monadic scenario, its steps and effects, into a single list of entries. Why? Because you can replay this recording against your scenario, and the player will immediately spot what parts of the scenario have changed. Does it sound like magic? Consider the following recording you could obtain from a simple scenario:

```json
// recording.json:
{
    "entries": [
      [
        0, "RunDBQueryEntry",
        {"jsonResult":[], "query":"SELECT * FROM students"}
      ],
      [
        1, "LogInfoEntry",
        {"message":"No records found."}
      ]
    ]
}
```

This is just a json file you can read to see what happens in your business logic. It's also replayable. True that this approach is a kind of unit testing because it knows everything about the details, but still this approach might be helpful if you want to have a golden set of tests. Automatic tests. You don't have to write a line of code for your tests; once you have a business logic written on top of your HFM framework, - you can obtain recordings immediately.

I recommend you to read my article for more info about this approach. It's closely tied with Free Monads. To be honest I don't see any good way to port this to Final Tagless. Maybe there is; but still I'm very sure Free Monads are much more testable than other design approaches.

### Performance

_**"Stop it. Stop spreading Free Monads. Don't bash our anti-Free-Monad-FUD. Nothing of these benefits outweighs the fact that Free Monads are inherently slow. Very slow, extremely slow. Quadratic complexity of binding? How could this have been even considered?"**_

Performance. The last resort argument of all developers who has a focus on the details rather than on the big picture. This argument usually implies that all the cases require the best possible performance right here, right now. Developers like to argue about the absolute need of performance, but once they finish arguing, they return to work and continue writing the slow code.

Fortunately, the myth about slow Free Monads is just a myth. Let me state several things:
* Normal Free Monads (the `Free` type from [here](http://hackage.haskell.org/package/free-5.1.3/docs/Control-Monad-Free.html)) are indeed slow. They have O(n^2) binding of monadic chains.
* Church Encoded Free Monads (the `F` type from [here](http://hackage.haskell.org/package/free-5.1.3/docs/Control-Monad-Free-Church.html)) are as fast as Final Tagless.
* There are even more different Free Monads out there. For example, in PureScript, a Free Monad from the ["Reflection with No Remorse"](http://okmij.org/ftp/Haskell/zseq.pdf) paper has been implemented. This Free Monad is also fast enough to be used in production.
* Normal Free Monads and Church Encoded Free Monads have a different implementation, but the interface is exactly the same. You can even change the monad without affecting your business logic. This means, even if you started from the normal Free Monad, you can move to the Church Encoded one whenever you want.
* In HFM, scenarios are never a single flow of actions. Due to the hierarchical structure of a HFM framework, scenarios are more like trees of separate Free Monadic chains. This means these scenarios have own counter on how many operations in them. This smoothes the binding problem, if any.
* Even normal Free Monads can be used for short scenarios.

When we deal with software solving real problems, performance shouldn't be an object for theoretical reasoning. Theorethizing can't provide you the actual picture on how your code behaves. Abstract performance that is unrelated to a specific task, to a specific code, doesn't make any sense. The only measurement of truth here is the experiment. I knew this and I did some experiments for you in my Hydra framework.

The following table shows comparison of the four approaches for a simple scenario. It was obtained by running `time` on a program compiled without any specific tweaks and with default GHC options. Don't mind the absolute numbers but compare the difference.

|Ops cnt  | FT    | FreeM | ChurchM | IO
----------| ------|-------|---------|--------
|10       | 0.265 | 0.222 | 0.223   | 0.227
|100      | 0.221 | 0.226 | 0.228   | 0.222
|1000     | 0.227 | 0.245 | 0.223   | 0.226
|10000    | 0.229 | 4.106 | 0.227   | 0.224
|100000   | 0.289 | inf   | 0.31    | 0.309
|1000000  | 0.859 | inf   | 1.134   | 0.857
|10000000 | 6.384 | inf   | 9.507   | 6.413
|20000000 | 13.734| inf   | 18.997  | 12.588
|30000000 | 18.16 | inf   | 28.568  | 17.76

Scenario itself:

```haskell
flow :: IORef Int -> L.AppL ()
flow ref = L.scenario $ do
  val' <- L.evalIO $ readIORef ref
  val  <- L.getRandomInt (1, 100)
  L.evalIO $ writeIORef ref $ val' + val


scenario :: Int -> R.AppRuntime -> IO ()
scenario ops appRt = do
  ref <- newIORef 0
  void $ R.startApp appRt (replicateM_ ops $ flow ref)
  val <- readIORef ref
  print val
```

Here, the Church Encoded Free Monad engine is a bit slower than Final Tagless, but don't mind the difference, - it starts to be significant from 1 million of operations. Are you sure your scenarios will be that long? This table says you can even use a normal Free Monad with monadic chains containing up to 10K actions. (If you're still unsure, try to measure performance yourself using the possibilities the Hydra framework provides).

# Conclusion

Now this is it. Hierarchical Free Monads is the most developed approach in Haskell because:

* There is a whole book about this approach: ["Functional Design and Architecture"](https://graninas.com/functional-design-and-architecture-book/).
* There are articles describing it in detail:
  - This one;
  - [Automatic White-Box Testing With Free Monads](https://github.com/graninas/automatic-whitebox-testing-showcase)
  - [Building network actors with Node Framework](https://gist.github.com/graninas/9beb8df5d88dda5fa21c47ce9bcb0e16)
* There are talks on it:
  - [Hierarchical Free Monads and Software Design in Functional Programming (Eng)](https://www.youtube.com/watch?v=3GKQ4ni2pS0)
  - [Automatic Whitebox Testing with Free Monads (Eng)](https://www.youtube.com/watch?v=ciZL-adDpVQ)
* There are showcase projects demonstrating all the aspects of HFM:
  - [Hydra framework](https://github.com/graninas/Hydra)
  - [hinteractive](https://github.com/graninas/hinteractive)
* There are successful commercial technologies based on it:
  - [Node framework](https://github.com/graninas/Node)
  - [PureScript Presto](https://github.com/juspay/purescript-presto)
  - [PureScript Presto.Backend](https://github.com/juspay/purescript-presto-backend)
* Finally, the most important: there are happy businesses driven by this technology to their great benefit and pleasure.

This is why you should use Hierarchical Free Monads, too.

### Farewell Word

The long history of programming languages shows that a language can't really survive without the industrial adoption. All the languages which were only academic toys became completely abandoned after a decade or maybe two. The languages which are refusing to interact with the mainstream are doomed to die slowly and inevitably. There is no reason for Haskell to be different here. The tasks from academia will be finished sooner or later. If we want the language to be successful and alive, adoption by the industry is the only possible way long term.

But the industry is not interested in advanced Math concepts, it's not interested in cool smart things, it doesn't value curiosity as haskellers do. The only thing the industry considers important is can it have its goals achieved or not and by what cost. Unfortunately, achieving the industry's goals is where the Haskell community has a big problem. We all need to learn from the mainstream, we need to adopt methodologies, ideas and practices to show the industry that Haskell is not a toy but rather a tool able to solve real tasks and lead businesses to success. We all should be more open-minded and should not stay in our Ivory Tower.

Hire me to know more, support me, subscribe to me on Twitter, ask questions.

* Buy the book: [Functional Design And Architecture (Second Edition)](https://www.manning.com/books/functional-design-and-architecture)
* [My consultancy work](https://graninas.com/cv-contacts/)
* GitHub: [graninas](https://github.com/graninas)
* Twitter: [@graninas](https://twitter.com/graninas)
* [LinkedIn](https://www.linkedin.com/in/alexander-granin-46889236/)
* Telegram: [@graninas](https://web.telegram.org/#/im?p=@graninas)
* E-mail: [graninas@gmail.com](mailto:graninas@gmail.com)
