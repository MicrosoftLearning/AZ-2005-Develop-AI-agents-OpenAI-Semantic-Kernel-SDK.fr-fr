---
lab:
  title: 'Labo : Développer des agents IA à l’aide d’Azure OpenAI et du SDK Semantic Kernel'
  module: 'Module 01: Build your kernel'
---

# Labo : créer un assistant de voyage IA
# Manuel de labo de l’étudiant

Dans ce labo, vous allez créer un assistant de voyage IA à l’aide du kit de développement logiciel (SDK) Semantic Kernel. Vous allez créer un point de terminaison pour le service LLM (Large Language Model, grand modèle de langage), créer des fonctions Semantic Kernel et utiliser la fonctionnalité d’appel automatique de fonction du SDK Semantic Kernel pour acheminer l’intention de l’utilisateur vers les plug-ins appropriés, dont certains des plug-ins prédéfinis qui ont été fournis. Vous fournirez également du contexte au modèle LLM à partir de l’historique des conversations et permettre à l’utilisateur de poursuivre la conversation.

## Scénario de laboratoire

Vous êtes développeur dans une agence de voyage spécialisée dans la création d’expériences de voyage personnalisées pour ses clients. Vous avez été chargé de créer un assistant de voyage IA capable d’aider les clients à en savoir plus sur des destinations de voyage et à planifier des activités dans le cadre de leurs voyages. L’assistant de voyage IA doit pouvoir convertir des montants en devises, suggérer des destinations et des activités, fournir des expressions utiles dans différentes langues et traduire des expressions. L’assistant de voyage IA doit également pouvoir apporter des réponses contextuellement pertinentes aux demandes des utilisateurs en tenant compte de l’historique des conversations.

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
* [La dernière version du kit de développement logiciel (SDK) .NET 8.0](https://dotnet.microsoft.com/en-us/download/dotnet/8.0)
* L’[Extension C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) pour Visual Studio Code

### Préparer votre environnement de développement

Pour ces exercices, un projet de démarrage est mis à votre disposition. Effectuez les étapes suivantes pour configurer le projet de démarrage :

> [!IMPORTANT]
> Vous devez avoir installé .NET Framework 8.0 ainsi que les extensions de VS Code pour C# et le gestionnaire de packages NuGet.

1. Collez l'URL suivante dans une nouvelle fenêtre de navigateur.
   
     `https://github.com/MicrosoftLearning/AZ-2005-Develop-AI-agents-OpenAI-Semantic-Kernel-SDK/blob/master/Allfiles/Labs/02/Lab-02-Starter.zip`

1. Téléchargez le fichier zip en cliquant sur le bouton <kbd>...</kbd> situé en haut à droite de la page, ou appuyez sur <kbd>Ctrl</kbd>+<kbd>Maj</kbd>+<kbd>S</kbd>.

1. Extrayez le contenu du fichier zip vers un emplacement facile à trouver et à mémoriser, par exemple un dossier sur votre Bureau.

1. Ouvrez Visual Studio Code, puis sélectionnez **Fichier** > **Ouvrir le dossier**.

1. Accédez au dossier **Starter** que vous avez extrait, puis sélectionnez **Sélectionner un dossier**.

1. Ouvrez le fichier **Program.cs** dans l’éditeur de code.

> [!NOTE]
> Si vous êtes invité à approuver les fichiers du dossier, sélectionnez **Oui, j’ai confiance dans les auteurs**.

## Exercice 1 : Créer un plug-in avec le SDK Semantic Kernel

Pour cet exercice, vous créez un point de terminaison pour le service de grand modèle de langage (LLM). Le Kit de développement logiciel (SDK) Semantic Kernel (Noyau sémantique) utilise ce point de terminaison pour se connecter au LLM et exécuter des invites. Le SDK Semantic Kernel prend en charge HuggingFace, OpenAI et les grands modèles de langage Open AI. Pour cet exemple, vous utilisez Azure Open AI.

**Durée d’exécution estimée de l’exercice** : 10 minutes

### Tâche 1 : Créer une ressource Azure OpenAI

1. Accédez à [https://portal.azure.com](https://portal.azure.com).

1. Créez une ressource Azure OpenAI en utilisant les paramètres par défaut.

    > [!NOTE]
    > Si vous disposez déjà d’une ressource Azure OpenAI, vous pouvez ignorer cette étape.

1. Après la création de la ressource créée, sélectionnez **Accéder à la ressource**.

1. Sur la page **Vue d’ensemble**, sélectionnez **Accéder au portail Azure AI Foundry**.

1. Sélectionnez **Créer un déploiement**, puis **À partir de modèles de base**.

1. Dans la liste des modèles, sélectionnez **gpt-35-turbo-16k**.

1. Cliquez sur **Confirmer**

1. Entrez un nom pour votre déploiement et conservez les options par défaut.

1. Une fois le déploiement terminé, revenez à votre ressource Azure OpenAI dans le portail Azure.

1. Sous **Gestion des ressources**, accédez à **Clés et points de terminaison**.

    Vous allez utiliser les données ici dans la tâche suivante pour générer votre noyau. N’oubliez pas de garder vos clés privées et sécurisées !

### Tâche 2 : créer un plug-in natif

Dans cette tâche, vous allez créer un plug-in de fonction natif capable de convertir un montant dans une devise de base vers une devise cible.

1. Retournez à votre projet Visual Studio Code.

1. Ouvrez le fichier **appsettings.json** et mettez à jour les valeurs avec votre ID de modèle, point de terminaison et clé API Azure OpenAI Services.

    ```json
    {
        "modelId": "gpt-35-turbo-16k",
        "endpoint": "",
        "apiKey": ""
    }
    ```

1. Accédez au fichier nommé **CurrencyConverter.cs** dans le dossier **Plugins**.

1. Dans le fichier **CurrencyConverterPlugin.cs**, ajoutez le code suivant sous le commentaire **Créer une fonction noyau qui obtient le taux de change** :

    ```c#
    // Create a kernel function that gets the exchange rate
    [KernelFunction("convert_currency")]
    [Description("Converts an amount from one currency to another, for example USD to EUR")]
    public static decimal ConvertCurrency(decimal amount, string fromCurrency, string toCurrency)
    {
        decimal exchangeRate = GetExchangeRate(fromCurrency, toCurrency);
        return amount * exchangeRate;
    }
    ```

    Dans ce code, vous utilisez le décorateur **KernelFunction** pour déclarer votre fonction native. Vous utilisez également le décorateur **Description** pour ajouter une description de ce que fait la fonction. Ensuite, ajoutez la logique nécessaire pour convertir un montant donné d’une devise en une autre devise.

1. Ouvrez le fichier **Program.cs**

1. Importez le plug-in de convertisseur de devises sous le commentaire **Ajouter des plug-ins au noyau** :

    ```c#
    // Add plugins to the kernel
    kernel.ImportPluginFromType<CurrencyConverterPlugin>();
    ```

    Testons ensuite votre plug-in.

1. Cliquez avec le bouton droit sur votre fichier **Program.cs**, puis cliquez sur « Ouvrir dans le terminal intégré ».

1. Dans le terminal, entrez `dotnet run`. 

    Entrez une demande d’invite pour convertir la devise, par exemple « Combien vaut 10 USD à Hong Kong ? ».

    Vous devez obtenir une sortie similaire à la suivante :

    ```output
    Assistant: 10 USD is equivalent to 77.70 Hong Kong dollars (HKD).
    ```

## Exercice 2 : créer une invite Handlebars

Dans cet exercice, vous allez créer une fonction à partir d’une invite Handlebars. La fonction invite le LLM à créer un iténeraire de voyage pour l’utilisateur. C’est parti !

**Durée d’exécution estimée de l’exercice** : 10 minutes

### Tâche 1 : créer une fonction à partir d’une invite Handlebars

1. Ajoutez la directive `using` suivante au fichier **Program.cs** :

    `using Microsoft.SemanticKernel.PromptTemplates.Handlebars;`

1. Ajoutez le code suivant sous le commentaire **Créer une invite Handlebars** :

    ```c#
    // Create a handlebars prompt
    string hbprompt = """
        <message role="system">Instructions: Before providing the user with a travel itinerary, ask how many days their trip is</message>
        <message role="user">I'm going to {{city}}. Can you create an itinerary for me?</message>
        <message role="assistant">Sure, how many days is your trip?</message>
        <message role="user">{{input}}</message>
        <message role="assistant">
        """;
    ```

    Dans ce code, vous créez une invite avec peu d’exemples à l’aide du format de modèle Handlebars. L’invite guide le modèle pour récupérer plus d’informations de l’utilisateur avant de créer un iténeraire de voyage.

1. Ajoutez le code suivant sous le commentaire **Créer la configuration du modèle d’invite à l’aide du format Handlebars** :

    ```c#
    // Create the prompt template config using handlebars format
    var templateFactory = new HandlebarsPromptTemplateFactory();
    var promptTemplateConfig = new PromptTemplateConfig()
    {
        Template = hbprompt,
        TemplateFormat = "handlebars",
        Name = "GetItinerary",
    };
    ```

    Ce code crée une configuration de modèle Handlebars à partir de l’invite. Vous pouvez l’utiliser pour créer une fonction de plug-in.

1. Ajoutez le code suivant sous le commentaire **Créer une fonction de plug-in à partir de l’invite** : 

    ```c#
    // Create a plugin function from the prompt
    var promptFunction = kernel.CreateFunctionFromPrompt(promptTemplateConfig, templateFactory);
    var itineraryPlugin = kernel.CreatePluginFromFunctions("TravelItinerary", [promptFunction]);
    kernel.Plugins.Add(itineraryPlugin);
    ```

    Ce code crée une fonction de plug-in pour l’invite et l’ajoute au noyau. Vous êtes maintenant prêt à appeler votre fonction.

1. Entrez `dotnet run` dans le terminal pour exécuter le code.

    Essayez l’entrée suivante pour demander au LLM un iténeraire.

    ```output
    Assistant: How may I help you?
    User: I'm going to Hong Kong, can you create an itinerary for me?
    Assistant: Sure! How many days will you be staying in Hong Kong?
    User: 10
    Assistant: Great! Here's a 10-day itinerary for your trip to Hong Kong:
    ...
    ```

    Et voilà. Vous avez créé les prémices d’un assistant de voyage IA. Utilisons des invites et des plug-ins pour ajouter d’autres fonctionnalités.

1.  Ajoutez le plug-in de réservation de vol sous le commentaire **Ajouter des plug-ins au noyau** :

    ```c#
    // Add plugins to the kernel
    kernel.ImportPluginFromType<CurrencyConverterPlugin>();
    kernel.ImportPluginFromType<FlightBookingPlugin>();
    ```

    Ce plug-in simule les réservations de vol à l’aide du fichier **flights.json** avec des détails fictifs. Ensuite, ajoutez des invites système supplémentaires à l’assistant.

1.  Ajoutez le code suivant sous le commentaire **Ajouter des messages système à la conversation** :

    ```c#
    // Add system messages to the chat
    history.AddSystemMessage("The current date is 01/10/2025");
    history.AddSystemMessage("You are a helpful travel assistant.");
    history.AddSystemMessage("Before providing destination recommendations, ask the user about their budget.");
    ```

    Ces invites vous aideront à créer une expérience utilisateur fluide et à simuler le plug-in de réservation de vol. Vous êtes maintenant prêt à tester votre code.

1. Entrez `dotnet run` dans le terminal.

    Essayez d’entrer certaines des invites suivantes :

    ```output
    1. Can you give me some destination recommendations for Europe?
    2. I want to go to Barcelona, can you create an itinerary for me?
    3. How many Euros is 100 USD?
    4. Can you book me a flight to Barcelona?
    ```

    Essayez d’autres entrées et découvrez comment votre assistant voyage répond.

## Exercice 3 : exiger le consentement de l’utilisateur pour les actions

Dans cet exercice, vous ajoutez une fonction d’appel de filtre qui demande l’approbation de l’utilisateur avant d’autoriser l’assistant à réserver un vol en son nom. C’est parti !

### Tâche 1 : créer un filtre d’appel de fonction

1. Créez un fichier nommé **PermissionFilter.cs**.

1. Entrez le code suivant dans le fichier :

    ```c#
    #pragma warning disable SKEXP0001 
    using Microsoft.SemanticKernel;
    
    public class PermissionFilter : IFunctionInvocationFilter
    {
        public async Task OnFunctionInvocationAsync(FunctionInvocationContext context, Func<FunctionInvocationContext, Task> next)
        {
            // Check the plugin and function names
            
            await next(context);
        }
    }
    ```

    >[!NOTE] 
    > Dans la version 1.30.0 du Kit de développement logiciel (SDK) de Semantic Kernel, les filtres de fonction sont susceptibles de changer et nécessitent une suppression d’avertissement. 

    Dans ce code, vous implémentez l’interface `IFunctionInvocationFilter`. La méthode `OnFunctionInvocationAsync` est toujours appelée chaque fois qu’une fonction est appelée à partir d’un assistant IA.

1. Ajoutez le code suivant pour détecter quand la fonction `book_flight` est appelée :

    ```c#
    // Check the plugin and function names
    if ((context.Function.PluginName == "FlightBookingPlugin" && context.Function.Name == "book_flight"))
    {
        // Request user approval

        // Proceed if approved
    }
    ```

    Ce code utilise `FunctionInvocationContext` pour déterminer le plug-in et la fonction qui ont été appelés.

1. Ajoutez la logique suivante pour demander l’autorisation de l’utilisateur pour réserver le vol :

    ```c#
    // Request user approval
    Console.WriteLine("System Message: The assistant requires an approval to complete this operation. Do you approve (Y/N)");
    Console.Write("User: ");
    string shouldProceed = Console.ReadLine()!;

    // Proceed if approved
    if (shouldProceed != "Y")
    {
        context.Result = new FunctionResult(context.Result, "The operation was not approved by the user");
        return;
    }
    ```

1. Accédez au fichier **Program.cs**.

1. Ajoutez le code suivant sous le commentaire **Ajouter des filtres au noyau** :

    ```c#
    // Add filters to the kernel
    kernel.FunctionInvocationFilters.Add(new PermissionFilter());
    ```

1. Entrez `dotnet run` dans le terminal.

    Entrez une invite pour réserver un vol. Vous devriez voir une réponse similaire à ce qui suit :

    ```output
    User: Find me a flight to Tokyo on the 19
    Assistant: I found a flight to Tokyo on the 19th of January. The flight is with Air Japan and the price is $1200.
    User: Y
    System Message: The assistant requires an approval to complete this operation. Do you approve (Y/N)
    User: N
    Assistant: I'm sorry, but I am unable to book the flight for you.
    ```

    L’assistant doit exiger l’approbation de l’utilisateur avant de poursuivre les réservations.

### Révision

Dans ce labo, vous avez créé un point de terminaison pour le service de grand modèle de langage (LLM), créé un objet Semantic Kernel et exécuté des invites à l’aide du SDK de Semantic Kernel. Vous avez également créé des plug-ins et utilisé des messages système pour guider le modèle. Félicitations, vous avez terminé ce labo !
