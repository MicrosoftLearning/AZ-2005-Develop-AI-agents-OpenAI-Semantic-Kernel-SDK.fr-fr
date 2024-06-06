---
lab:
  title: 'Labo : Développer des agents IA à l’aide d’Azure OpenAI et du SDK Semantic Kernel'
  module: 'Module 01: Build your kernel'
---

# Labo : Créer un agent de voyage IA
# Manuel de labo de l’étudiant

Dans ce labo, vous allez créer un agent de voyage IA à l’aide du kit de développement logiciel (SDK) Semantic Kernel. Vous allez créer un point de terminaison pour le service LLM (Large Language Model, grand modèle de langage), créer des fonctions Semantic Kernel et utiliser la fonctionnalité d’appel automatique de fonction du SDK Semantic Kernel pour acheminer l’intention de l’utilisateur vers les plug-ins appropriés, dont certains des plug-ins prédéfinis qui ont été fournis. Vous fournirez également du contexte au modèle LLM à partir de l’historique des conversations et permettre à l’utilisateur de poursuivre la conversation.

## Scénario de laboratoire

Vous êtes développeur dans une agence de voyage spécialisée dans la création d’expériences de voyage personnalisées pour ses clients. Vous avez été chargé de créer un agent de voyage IA capable d’aider les clients à les renseigner sur des destinations de voyage et à planifier des activités dans le cadre de leurs voyages. L’agent de voyage IA doit pouvoir convertir des montants en devises, suggérer des destinations et des activités, fournir des expressions utiles dans différentes langues et traduire des expressions. L’agent de voyage IA doit également pouvoir apporter des réponses contextuellement pertinentes aux demandes des utilisateurs en tenant compte de l’historique des conversations.

## Objectifs

En effectuant ce labo, vous allez effectuer les opérations suivantes :

* Créer un point de terminaison pour le service LLM (Large Language Model, grand modèle de langage)
* Générer un objet Semantic Kernel
* Exécuter des prompts à l’aide du kit SDK Semantic Kernel
* Créer des fonctions et des plug-ins Semantic Kernel
* Utiliser la fonctionnalité d’appel automatique de fonction du kit SDK Semantic Kernel

## Mise en place du labo

### Prérequis

Pour effectuer l’exercice, les éléments suivants doivent être installés dans votre système :

* [Visual Studio Code](https://code.visualstudio.com)
* [la dernière version du kit de développement logiciel (SDK) .NET 7.0](https://dotnet.microsoft.com/download/dotnet/7.0)
* L’[Extension C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) pour Visual Studio Code

### Préparer votre environnement de développement

Pour ces exercices, un projet de démarrage est mis à votre disposition. Effectuez les étapes suivantes pour configurer le projet de démarrage :

> [!IMPORTANT]
> Vous devez avoir installé .NET Framework 8.0 ainsi que les extensions de VS Code pour C# et le gestionnaire de packages NuGet.

1. Téléchargez le fichier zip situé sur `https://github.com/MicrosoftLearning/AZ-2005-Develop-AI-agents-OpenAI-Semantic-Kernel-SDK/blob/master/Allfiles/Labs/02/Lab-02-Starter.zip`.

1. Extrayez le contenu du fichier zip vers un emplacement facile à trouver et à mémoriser, par exemple un dossier sur votre Bureau.

1. Ouvrez Visual Studio Code, puis sélectionnez **Fichier** > **Ouvrir le dossier**.

1. Accédez au dossier **Starter** que vous avez extrait, puis sélectionnez **Sélectionner un dossier**.

1. Ouvrez le fichier **Program.cs** dans l’éditeur de code.

## Exercice 1 : Créer un plug-in avec le SDK Semantic Kernel

Pour cet exercice, vous créez un point de terminaison pour le service de grand modèle de langage (LLM). Le Kit de développement logiciel (SDK) Semantic Kernel (Noyau sémantique) utilise ce point de terminaison pour se connecter au LLM et exécuter des invites. Le SDK Semantic Kernel prend en charge HuggingFace, OpenAI et les grands modèles de langage Open AI. Pour cet exemple, vous utilisez Azure Open AI.

**Durée d’exécution estimée de l’exercice** : 10 minutes

### Tâche 1 : Créer une ressource Azure OpenAI

1. Accédez à [https://portal.azure.com](https://portal.azure.com).

1. Créez une ressource Azure OpenAI en utilisant les paramètres par défaut.

    > [!NOTE]
    > Si vous disposez déjà d’une ressource Azure OpenAI, vous pouvez ignorer cette étape.

1. Après la création de la ressource créée, sélectionnez **Accéder à la ressource**.

1. Dans la page **Vue d’ensemble**, sélectionnez **Accéder à Azure OpenAI Studio**.

:::image type="content" source="../media/model-deployments.png" alt-text="Capture d’écran de la page de déploiements Azure OpenAI.":::

1. Sélectionnez **Créer un déploiement**, puis **Déployer un modèle**.

1. Sous **Sélectionner un modèle**, sélectionnez **gpt-35-turbo-16k**.

    Utiliser la version de modèle par défaut

1. Donnez un nom à votre déploiement

1. Une fois le déploiement terminé, revenez à votre ressource Azure OpenAI.

1. Sous **Gestion des ressources**, accédez à **Clés et points de terminaison**.

    Vous vous servirez de ces valeurs pour générer votre noyau dans la tâche suivante. N’oubliez pas de mettre vos clés à l’abri, dans un emplacement privé et sécurisé.

1. Retournez au fichier **Program.cs** dans Visual Studio Code.

1. Mettez à jour les variables suivantes avec le nom de votre déploiement Azure Open AI Services, la clé API et le point de terminaison

    ```csharp
    string yourDeploymentName = "";
    string yourEndpoint = "";
    string yourKey = "";
    ```

    > [!NOTE]
    > Le modèle de déploiement doit être « gpt-35-turbo-16k » pour que certaines fonctionnalités du SDK Noyau sémantique fonctionnent.

### Tâche 2 : Créer une fonction native

Dans cette tâche, vous allez créer une fonction native capable de convertir un montant dans une devise de base vers une devise cible.

1. Créez un fichier nommé `CurrencyConverter.cs` dans le dossier **Plugins/ConvertCurrency**.

1. Dans le fichier `CurrencyConverter.cs` , ajoutez le code suivant pour créer une fonction de plug-in :

    ```c#
    using AITravelAgent;

    class CurrencyConverter
    {
        [KernelFunction, 
        Description("Convert an amount from one currency to another")]
        public static string ConvertAmount()
        {
            var currencyDictionary = Currency.Currencies;
        }
    }
    ```

    Dans ce code, vous utilisez le décorateur `KernelFunction` pour déclarer votre fonction native. Vous utilisez également le décorateur `Description` pour ajouter une description de ce que fait la fonction. Vous pouvez utiliser `Currency.Currencies` pour obtenir un dictionnaire de devises et leurs taux de change. Ensuite, ajoutez la logique nécessaire pour convertir un montant donné d’une devise en une autre devise.

1. Modifiez la fonction `ConvertAmount` avec le code suivant :

    ```c#
    [KernelFunction, Description("Convert an amount from one currency to another")]
    public static string ConvertAmount(
        [Description("The target currency code")] string targetCurrencyCode, 
        [Description("The amount to convert")] string amount, 
        [Description("The starting currency code")] string baseCurrencyCode)
    {
        var currencyDictionary = Currency.Currencies;
        
        Currency targetCurrency = currencyDictionary[targetCurrencyCode];
        Currency baseCurrency = currencyDictionary[baseCurrencyCode];

        if (targetCurrency == null)
        {
            return targetCurrencyCode + " was not found";
        }
        else if (baseCurrency == null)
        {
            return baseCurrencyCode + " was not found";
        }
        else
        {
            double amountInUSD = Double.Parse(amount) * baseCurrency.USDPerUnit;
            double result = amountInUSD * targetCurrency.UnitsPerUSD;
            return @$"${amount} {baseCurrencyCode} is approximately 
                {result.ToString("C")} in {targetCurrency.Name}s ({targetCurrencyCode})";
        }
    }
    ```

    Dans ce code, vous utilisez le dictionnaire `Currency.Currencies`pour obtenir l’objet `Currency` pour la devise cible et la devis de base. Vous utilisez ensuite l’objet `Currency` pour convertir le montant de la devise de base en devise cible. Enfin, vous retournez une chaîne avec le montant converti. Testons ensuite votre plug-in.

1. Dans le fichier `Program.cs`, importez et appelez votre nouvelle fonction de plug-in avec le code suivant :

    ```c#
    kernel.ImportPluginFromType<CurrencyConverter>();
    kernel.ImportPluginFromType<ConversationSummaryPlugin>();
    var prompts = kernel.ImportPluginFromPromptDirectory("Prompts");

    var result = await kernel.InvokeAsync("CurrencyConverter", 
        "ConvertAmount", 
        new() {
            {"targetCurrencyCode", "USD"}, 
            {"amount", "52000"}, 
            {"baseCurrencyCode", "VND"}
        }
    );

    Console.WriteLine(result);
    ```

    Dans ce code, vous utilisez la méthode `ImportPluginFromType` pour importer votre plug-in. Ensuite, vous utilisez la méthode `InvokeAsync` pour appeler votre fonction de plug-in. La méthode `InvokeAsync` prend le nom du plug-in, le nom de la fonction et un dictionnaire de paramètres. Enfin, vous affichez le résultat dans la console. Ensuite, exécutez le code pour vérifier qu’il fonctionne.

1. Dans le terminal, entrez `dotnet run`. Vous devez normalement voir la sortie suivante.

    ```output
    $52000 VND is approximately $2.13 in US Dollars (USD)
    ```

    Maintenant que votre plug-in fonctionne correctement, nous allons créer un prompt en langage naturel qui peut détecter les devises et le montant que l’utilisateur veut convertir.

### Tâche 3 : Analyser l’entrée utilisateur avec un prompt

Dans cette tâche, vous créez un prompt qui analyse l’entrée de l’utilisateur pour identifier la devise cible, la devise de base et le montant à convertir.

1. Créez un dossier nommé `GetTargetCurrencies` dans le dossier **Prompts**.

1. Dans le dossier `GetTargetCurrencies`, créez un fichier nommé `config.json`.

1. Entrez le texte suivant dans le fichier `config.json` :

    ```output
    {
        "schema": 1,
        "type": "completion",
        "description": "Identify the target currency, base currency, and amount to convert",
        "execution_settings": {
            "default": {
                "max_tokens": 800,
                "temperature": 0
            }
        },
        "input_variables": [
            {
                "name": "input",
                "description": "Text describing some currency amount to convert",
                "required": true
            }
        ]
    }
    ```

1. Dans le dossier `GetTargetCurrencies`, créez un fichier nommé `skprompt.txt`.

1. Entrez le texte suivant dans le fichier `skprompt.txt` :

    ```html
    <message role="system">Identify the target currency, base currency, and 
    amount from the user's input in the format target|base|amount</message>

    For example: 

    <message role="user">How much in GBP is 750.000 VND?</message>
    <message role="assistant">GBP|VND|750000</message>

    <message role="user">How much is 60 USD in New Zealand Dollars?</message>
    <message role="assistant">NZD|USD|60</message>

    <message role="user">How many Korean Won is 33,000 yen?</message>
    <message role="assistant">KRW|JPY|33000</message>

    <message role="user">{{$input}}</message>
    <message role="assistant">target|base|amount</message>
    ```

### Tâche 4 : Vérifier votre travail

Dans cette tâche, vous exécutez votre application et vous vérifiez que votre code fonctionne correctement. 

1. Testez votre nouveau prompt en mettant à jour votre fichier `Program.cs` avec le code suivant :

    ```c#
    kernel.ImportPluginFromType<CurrencyConverter>();
    var prompts = kernel.ImportPluginFromPromptDirectory("Prompts");

    var result = await kernel.InvokeAsync(prompts["GetTargetCurrencies"],
        new() {
            {"input", "How many Australian Dollars is 140,000 Korean Won worth?"}
        }
    );

    Console.WriteLine(result);
    ```

1. Entrez `dotnet run` dans le terminal. Vous devez normalement voir la sortie suivante :

    ```output
    AUD|KRW|140000
    ```

    > [!NOTE]
    > Si votre code ne produit pas le résultat attendu, vous pouvez le passer en revue dans le dossier **Solution**. Vous pouvez être amené à ajuster le prompt dans le fichier `skprompt.txt` pour produire des résultats plus exacts.

Vous avez maintenant un plug-in qui peut convertir un montant d’une devise en une autre, et un prompt qui peut être utilisé pour analyser l’entrée de l’utilisateur dans un format que la fonction `ConvertAmount` peut utiliser. Cela permettra aux utilisateurs de convertir facilement des montants en devise à l’aide de votre agent de voyage IA.

## Exercice 2 : Automatiser la sélection de plug-in en fonction de l’intention de l’utilisateur

Dans cet exercice, vous détectez l’intention de l’utilisateur et vous routez la conversation vers les plug-ins souhaités. Vous pouvez utiliser un plug-in fourni pour récupérer l’intention de l’utilisateur. C’est parti !

**Durée d’exécution estimée de l’exercice** : 10 minutes

### Tâche 1 : Utiliser le plug-in GetIntent

1. Mettez à jour votre fichier `Program.cs` avec le code suivant :

    ```c#
    kernel.ImportPluginFromType<CurrencyConverter>();
    var prompts = kernel.ImportPluginFromPromptDirectory("Prompts");

    Console.WriteLine("What would you like to do?");
    var input = Console.ReadLine();

    var intent = await kernel.InvokeAsync<string>(
        prompts["GetIntent"], 
        new() {{ "input",  input }}
    );

    ```

    Dans ce code, vous utilisez le prompt `GetIntent` pour détecter l’intention de l’utilisateur. Vous stockez ensuite l’intention dans une variable appelée `intent`. Ensuite, vous routez l’intention vers votre plug-in `CurrencyConverter`.

1. Ajoutez le code suivant à votre fichier `Program.cs` :

    ```c#
    switch (intent) {
        case "ConvertCurrency": 
            var currencyText = await kernel.InvokeAsync<string>(
                prompts["GetTargetCurrencies"], 
                new() {{ "input",  input }}
            );
            var currencyInfo = currencyText!.Split("|");
            var result = await kernel.InvokeAsync("CurrencyConverter", 
                "ConvertAmount", 
                new() {
                    {"targetCurrencyCode", currencyInfo[0]}, 
                    {"baseCurrencyCode", currencyInfo[1]},
                    {"amount", currencyInfo[2]}, 
                }
            );
            Console.WriteLine(result);
            break;
        default:
            Console.WriteLine("Other intent detected");
            break;
    }
    ```

    Le plug-in `GetIntent` retourne les valeurs suivantes : ConvertCurrency, SuggestDestinations, SuggestActivities, Translate, HelpfulPhrases, Unknown. Vous utilisez une instruction `switch` pour router l’intention de l’utilisateur vers le plug-in approprié. 
    
    Si l’intention de l’utilisateur est de convertir une devise, vous utilisez le prompt `GetTargetCurrencies` pour récupérer les informations sur la devise. Ensuite, vous utilisez le plug-in `CurrencyConverter` pour convertir le montant.

    Ensuite, vous ajoutez certains cas pour gérer les autres intentions. Pour l’instant, utilisons la capacité d’appel automatique de fonctions du Kit de développement logiciel (SDK) Noyau sémantique pour router l’intention vers les plug-ins disponibles.

1. Créez le paramètre d’appel de fonction automatique en ajoutant le code suivant à votre fichier `Program.cs` :

    ```c#
    kernel.ImportPluginFromType<CurrencyConverter>();
    var prompts = kernel.ImportPluginFromPromptDirectory("Prompts");

    OpenAIPromptExecutionSettings settings = new()
    {
        ToolCallBehavior = ToolCallBehavior.AutoInvokeKernelFunctions
    };

    Console.WriteLine("What would you like to do?");
    var input = Console.ReadLine();
    var intent = await kernel.InvokeAsync<string>(
        prompts["GetIntent"], 
        new() {{ "input",  input }}
    );
    ```

    Ensuite, vous ajoutez des cas à l’instruction switch pour les autres intentions.

1. Mettez à jour votre fichier `Program.cs` avec le code suivant :

    ```c#
    switch (intent) {
        case "ConvertCurrency": 
            // ...Code you entered previously...
            break;
        case "SuggestDestinations":
        case "SuggestActivities":
        case "HelpfulPhrases":
        case "Translate":
            var autoInvokeResult = await kernel.InvokePromptAsync(input!, new(settings));
            Console.WriteLine(autoInvokeResult);
            break;
        default:
            Console.WriteLine("Other intent detected");
            break;
    }
    ```

    Dans ce code, vous utilisez le paramètre `AutoInvokeKernelFunctions` pour appeler automatiquement des fonctions et des prompts référencés dans votre noyau. Si l’intention de l’utilisateur est de convertir une devise, le plug-in `CurrencyConverter` effectue sa tâche. 
    
    Si l’intention de l’utilisateur est d’obtenir des suggestions de destination ou d’activité, de traduire une expression ou d’obtenir des expressions utiles dans une langue, le paramètre `AutoInvokeKernelFunctions` appelle automatiquement les plug-ins existants qui ont inclus dans le code du projet.

    Vous pouvez également ajouter du code pour exécuter l’entrée de l’utilisateur en tant que prompt adressé au grand modèle de langage (LLM) si elle ne fait pas partie de ces cas de l’intention.

1. Remplacez le cas par défaut par le code suivant :

    ```c#
    default:
        Console.WriteLine("Sure, I can help with that.");
        var otherIntentResult = await kernel.InvokePromptAsync(input!, new(settings));
        Console.WriteLine(otherIntentResult);
        break;
    ```

    Maintenant, si l’utilisateur a une intention différente, le LLM peut gérer la demande de l’utilisateur. Essayons-le !

### Tâche 2 : Vérifier votre travail

Dans cette tâche, vous exécutez votre application et vous vérifiez que votre code fonctionne correctement. 

1. Entrez `dotnet run` dans le terminal. Quand vous y êtes invité, entrez un texte similaire au prompt suivant :

    ```output
    What would you like to do?
    How many TTD is 50 Qatari Riyals?    
    ```

1. Vous devez voir une sortie similaire à la réponse suivante :

    ```output
    $50 QAR is approximately $93.10 in Trinidadian Dollars (TTD)
    ```

1. Entrez `dotnet run` dans le terminal. Quand vous y êtes invité, entrez un texte similaire au prompt suivant :

    ```output
    What would you like to do?
    I want to go somewhere that has lots of warm sunny beaches and delicious, spicy food!
    ```

1. Vous devez voir une sortie similaire à la réponse suivante :

    ```output
    Based on your preferences for warm sunny beaches and delicious, spicy food, I have a few destination recommendations for you:

    1. Thailand: Known for its stunning beaches, Thailand offers a perfect combination of relaxation and adventure. You can visit popular beach destinations like Phuket, Krabi, or Koh Samui, where you'll find crystal-clear waters and white sandy shores. Thai cuisine is famous for its spiciness, so you'll have plenty of mouthwatering options to try, such as Tom Yum soup, Pad Thai, and Green Curry.

    2. Mexico: Mexico is renowned for its beautiful coastal regions and vibrant culture. You can explore destinations like Cancun, Playa del Carmen, or Tulum, which boast stunning beaches along the Caribbean Sea. Mexican cuisine is rich in flavors and spices, offering a wide variety of dishes like tacos, enchiladas, and mole sauces that will satisfy your craving for spicy food.

    ...

    These destinations offer a perfect blend of warm sunny beaches and delicious, spicy food, ensuring a memorable trip for you. Let me know if you need any further assistance or if you have any specific preferences for your trip!
    ```

1. Entrez `dotnet run` dans le terminal. Quand vous y êtes invité, entrez un texte similaire au prompt suivant :

    ```output
    What would you like to do?
    Can you give me a recipe for chicken satay?

1. You should see a response similar to the following response:

    ```output
    Sure, I can help with that.
    Certainly! Here's a recipe for chicken satay:

    ...
    ```

    L’intention doit être acheminée vers le cas par défaut et le LLM doit gérer la demande d’une recette de poulet satay.

    > [!NOTE]
    > Si votre code ne produit pas le résultat attendu, vous pouvez passer en revue le code dans le dossier **Solution**.

Ensuite, nous allons modifier la logique du routage pour fournir un historique des conversations à certains plug-ins. Fournir l’historique permet aux plug-ins de récupérer des réponses aux demandes de l’utilisateur avec un contexte plus pertinent.

### Tâche 3 : Effectuer le routage du plug-in

Dans cet exercice, vous utilisez l’historique des conversations pour fournir un contexte au modèle de langage volumineux (LLM). Vous ajustez également le code afin qu’il permette à l’utilisateur de poursuivre la conversation, tout comme un vrai chatbot. C’est parti !

1. Modifiez le code pour utiliser une boucle `do`-`while` pour accepter l’entrée de l’utilisateur :

    ```c#
    string input;

    do 
    {
        Console.WriteLine("What would you like to do?");
        input = Console.ReadLine();

        // ...
    }
    while (!string.IsNullOrWhiteSpace(input));
    ```

    Vous pouvez maintenant poursuivre la conversation jusqu’à ce que l’utilisateur entre une ligne vide.

1. Capturez des détails sur le voyage de l’utilisateur en modifiant le cas `SuggestDestinations` :

    ```c#
    case "SuggestDestinations":
        chatHistory.AppendLine("User:" + input);
        var recommendations = await kernel.InvokePromptAsync(input!);
        Console.WriteLine(recommendations);
        break;
    ```

1. Utilisez les détails du voyage dans le cas `SuggestActivities` avec le code suivant :

    ```c#
     case "SuggestActivities":
        var chatSummary = await kernel.InvokeAsync(
            "ConversationSummaryPlugin", 
            "SummarizeConversation", 
            new() {{ "input", chatHistory.ToString() }});
        break;
    ```

    Dans ce code, vous utilisez la fonction intégrée `SummarizeConversation` pour résumer la conversation avec l’utilisateur. Ensuite, utilisons le résumé pour suggérer des activités à la destination.

1. Étendez le cas `SuggestActivities` avec le code suivant :

    ```c#
    var activities = await kernel.InvokePromptAsync(
        input,
        new () {
            {"input", input},
            {"history", chatSummary},
            {"ToolCallBehavior", ToolCallBehavior.AutoInvokeKernelFunctions}
    });

    chatHistory.AppendLine("User:" + input);
    chatHistory.AppendLine("Assistant:" + activities.ToString());
    
    Console.WriteLine(activities);
    break;
    ```

    Dans ce code, vous ajoutez `input` et `chatSummary` en tant qu’arguments de noyau. Ensuite, le noyau appelle l’invite et l’achemine vers le plug-in `SuggestActivities`. Vous ajoutez également l’entrée utilisateur et la réponse de l’Assistant à l’historique des conversations et affichez les résultats. Ensuite, vous devez ajouter la variable `chatSummary` au plug-in `SuggestActivities`.

1. Accédez à **Prompts/SuggestActivities/config.json** et ouvrez le fichier dans Visual Studio Code

1. Sous `input_variables`, ajoutez une variable pour l’historique des conversations :

    ```json
    "input_variables": [
      {
          "name": "history",
          "description": "Some background information about the user",
          "required": false
      },
      {
          "name": "destination",
          "description": "The destination a user wants to visit",
          "required": true
      }
  ]
  ```

1. Accédez à **Prompts/SuggestActivities/skprompt.txt** et ouvrez le fichier

1. Ajoutez une invite pour utiliser l’historique des conversations :

    ```html 
    You are an experienced travel agent. 
    You are helpful, creative, and very friendly. 
    Consider the traveler's background: {{$history}}
    ```

    Laissez le reste de l’invite en l’état. Maintenant, le plug-in utilise l’historique des conversations pour fournir un contexte au LLM.

### Tâche 4 : Vérifier votre travail

Dans cette tâche, vous exécutez votre application et vous vérifiez que le code fonctionne correctement.

1. Comparez vos cas de commutateur mis à jour au code suivant :

    ```c#
    case "SuggestDestinations":
            chatHistory.AppendLine("User:" + input);
            var recommendations = await kernel.InvokePromptAsync(input!);
            Console.WriteLine(recommendations);
            break;
    case "SuggestActivities":

        var chatSummary = await kernel.InvokeAsync(
            "ConversationSummaryPlugin", 
            "SummarizeConversation", 
            new() {{ "input", chatHistory.ToString() }});

        var activities = await kernel.InvokePromptAsync(
            input!,
            new () {
                {"input", input},
                {"history", chatSummary},
                {"ToolCallBehavior", ToolCallBehavior.AutoInvokeKernelFunctions}
        });

        chatHistory.AppendLine("User:" + input);
        chatHistory.AppendLine("Assistant:" + activities.ToString());
        
        Console.WriteLine(activities);
        break;
    ```

1. Entrez `dotnet run` dans le terminal. Lorsque vous y êtes invité, entrez du texte similaire à ce qui suit :

    ```output
    What would you like to do?
    How much is 60 USD in new zealand dollars?
    ```

1. La sortie devrait ressembler à ce qui suit :

    ```output
    $60 USD is approximately $97.88 in New Zealand Dollars (NZD)
    What would you like to do?
    ```

1. Entrez une invite pour les suggestions de destination avec des indicateurs de contexte, par exemple :

    ```output
    What would you like to do?
    I'm planning an anniversary trip with my spouse, but they are currently using a wheelchair and accessibility is a must. What are some destinations that would be romantic for us?
    ```

1. Vous devez recevoir une sortie avec des recommandations de destinations accessibles.

1. Entrez une invite pour les suggestions d’activité, par exemple :

    ```output
    What would you like to do?
    What are some things to do in Barcelona?
    ```

1. Vous devez recevoir des recommandations qui s’inscrivent dans le contexte précédent, par exemple, des activités accessibles à Barcelone comme suit :

    ```output
    1. Visit the iconic Sagrada Família: This breathtaking basilica is an iconic symbol of Barcelona's architecture and is known for its unique design by Antoni Gaudí.

    2. Explore Park Güell: Another masterpiece by Gaudí, this park offers stunning panoramic views of the city, intricate mosaic work, and whimsical architectural elements.

    3. Visit the Picasso Museum: Explore the extensive collection of artworks by the iconic painter Pablo Picasso, showcasing his different periods and styles.
    ```

    > [!NOTE]
    > Si votre code ne produit pas le résultat attendu, vous pouvez le passer en revue dans le dossier **Solution**.

Vous pouvez continuer à tester l’application avec différentes invites et différents indicateurs de contexte. Bon travail ! Vous avez fourni des indicateurs de contexte au LLM et ajusté le code pour permettre à l’utilisateur de poursuivre la conversation.

### Révision

Dans ce labo, vous avez créé un point de terminaison pour le service LLM (Large Language Model, grand modèle de langage), généré un objet Semantic Kernel, exécuté des prompts à l’aide du kit SDK Semantic Kernel, crée des fonctions et des plug-ins Semantic Kernel et utilisé la fonctionnalité d’appel automatique de fonction du kit SDK Semantic Kernel pour acheminer l’intention de l’utilisateur vers les plug-ins appropriés. Vous avez également fourni du contexte au modèle LLM à partir de l’historique des conversations et permis à l’utilisateur de poursuivre la conversation. Félicitations, vous avez terminé ce labo !