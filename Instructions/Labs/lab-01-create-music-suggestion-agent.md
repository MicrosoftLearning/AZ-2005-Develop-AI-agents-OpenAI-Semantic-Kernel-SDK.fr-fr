---
lab:
  title: 'Labo : Développer des agents IA à l’aide d’Azure OpenAI et du SDK Semantic Kernel'
  module: 'Module 01: Build your kernel'
---

# Labo : créer un assistant de recommandation de musique IA
# Manuel de labo de l’étudiant

Dans ce labo, vous créez le code d’un assistant IA qui peut gérer la bibliothèque musicale de l’utilisateur et fournir des recommandations personnalisées de chansons et de concerts. Vous utilisez le kit de développement logiciel (SDK) Semantic Kernel pour générer l’assistant IA et le connecter au service de grand modèle de langage (LLM). Le SDK de noyau sémantique vous permet de créer une application intelligente qui peut interagir avec le service LLM et fournir des recommandations personnalisées à l’utilisateur.

## Scénario de laboratoire

Vous êtes développeur pour un service de streaming audio international. Vous avez été chargé d’intégrer le service à l’IA pour fournir aux utilisateurs une expérience plus personnalisée. L’IA doit être en mesure de recommander des chansons et des tournées d’artistes à venir en fonction de l’historique d’écoute et des préférences de l’utilisateur. Vous décidez d’utiliser le SDK Semantic Kernel pour générer un assistant IA capable d’interagir avec le service LLM.

## Objectifs

En effectuant ce labo, vous allez effectuer les opérations suivantes :

* Créer un point de terminaison pour le service LLM (Large Language Model, grand modèle de langage)
* Générer un objet Semantic Kernel
* Exécuter des prompts à l’aide du kit SDK Semantic Kernel
* Créer des fonctions et des plug-ins Semantic Kernel
* Activer l’appel automatique de fonction pour automatiser les tâches

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
   
     `https://github.com/MicrosoftLearning/AZ-2005-Develop-AI-agents-OpenAI-Semantic-Kernel-SDK/blob/master/Allfiles/Labs/01/Lab-01-Starter.zip`

1. Téléchargez le fichier zip en cliquant sur le bouton `...` situé en haut à droite de la page, ou appuyez sur <kbd>Ctrl</kbd>+<kbd>Maj</kbd>+<kbd>S</kbd>.

1. Extrayez le contenu du fichier zip vers un emplacement facile à trouver et à mémoriser, par exemple un dossier sur votre Bureau.

1. Ouvrez Visual Studio Code, puis sélectionnez **Fichier** > **Ouvrir le dossier**.

1. Accédez au dossier **Starter** que vous avez extrait, puis sélectionnez **Sélectionner un dossier**.

1. Ouvrez le fichier **Program.cs** dans l’éditeur de code.

> [!NOTE]
> Si vous êtes invité à approuver les fichiers du dossier, sélectionnez **Oui, j’ai confiance dans les auteurs**. 

## Exercice 1 : Exécuter une invite avec le Kit de développement logiciel (SDK) du noyau sémantique

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

### Tâche 2 : Générer votre noyau

Dans cet exercice, vous apprenez à créer votre premier projet de Kit de développement logiciel (SDK) Semantic Kernel (Noyau sémantique). Vous apprenez à créer un projet, à ajouter le package NuGet du Kit de développement logiciel (SDK) Semantic Kernel et à ajouter une référence au Kit de développement logiciel (SDK) Semantic Kernel. C’est parti !

1. Retournez à votre projet Visual Studio Code.

1. Ouvrez le fichier **appsettings.json** et mettez à jour les valeurs avec votre ID de modèle, point de terminaison et clé API Azure OpenAI Services.

    ```json
    {
        "modelId": "gpt-35-turbo-16k",
        "endpoint": "",
        "apiKey": ""
    }
    ```

1. Ouvrez le terminal en sélectionnant **Terminal** > **Nouveau terminal**.

1. Dans le terminal, exécutez la commande suivante pour installer le Kit de développement logiciel (SDK) Semantic Kernel :

    `dotnet add package Microsoft.SemanticKernel --version 1.30.0`

1. 1. Ajoutez les directives `using` suivantes au fichier **Program.cs** :

    ```c#
    using Microsoft.SemanticKernel;
    using Microsoft.SemanticKernel.ChatCompletion;
    using Microsoft.SemanticKernel.Connectors.OpenAI;
    ```

1. Ajoutez le code suivant sous le commentaire **Créer un générateur de noyau avec la saisie semi-automatique de conversation Azure OpenAI** :

    ```c#
    // Create a kernel builder with Azure OpenAI chat completion
    var builder = Kernel.CreateBuilder();
    builder.AddAzureOpenAIChatCompletion(modelId, endpoint, apiKey);
    ```

1. Générez le noyau en ajoutant ce code sous le commentaire **Générer le noyau** :

    ```c#
    // Build the kernel
    var kernel = builder.Build();
    ```

1. Ajoutez le code suivant sous le commentaire **Vérifier le point de terminaison et exécuter une invite** :

    ```c#
    // Verify the endpoint and run a prompt
    var result = await kernel.InvokePromptAsync("Who are the top 5 most famous musicians in the world?");
    Console.WriteLine(result);
    ```

1. Cliquez avec le bouton droit sur le dossier **Starter**, puis sélectionnez **Ouvrir dans le terminal intégré**.

1. Entrez `dotnet run` dans le terminal pour exécuter le code.

    Vérifiez que vous voyez une réponse du modèle Azure Open AI contenant les 5 plus célèbres musiciens du monde.

    La réponse provient du modèle Azure Open AI que vous avez transmis au noyau. Le SDK Semantic Kernel est en mesure de se connecter au grand modèle de langage (LLM) et d’exécuter l’invite. Notez la rapidité avec laquelle vous avez pu recevoir des réponses du LLM. Le Kit de développement logiciel (SDK) Semantic Kernel permet de générer facilement et efficacement des applications intelligentes.

Vous pouvez supprimer le code de vérification une fois que vous confirmez votre réponse.

## Exercice 2 : Créer des plug-ins de bibliothèque de musique personnalisées

Dans cet exercice, vous créez des plug-ins personnalisés pour votre bibliothèque de musique. Vous créez des fonctions qui peuvent ajouter des chansons à la liste récemment lue de l’utilisateur, obtenir la liste des chansons récemment lues et fournir des recommandations personnalisées sur les chansons. Vous créez également une fonction qui suggère un concert basé sur l’emplacement de l’utilisateur et leurs chansons récemment jouées.

**Durée d’exécution estimée de l’exercice** : 15 minutes

### Tâche 1 : Créer un plug-in de bibliothèque de musique

Dans cette tâche, vous créez un plug-in qui vous permet d’ajouter des chansons à la liste récemment lue de l’utilisateur et d’obtenir la liste des chansons récemment lues. Par souci de simplicité, les chansons récemment lues sont stockées dans un fichier texte.

1. Dans le dossier **Plug-ins**, ouvrez le fichier **MusicLibraryPlugin.cs**.

1. Sous le commentaire **Créer une fonction noyau pour obtenir les chansons récemment écoutées**, ajoutez le décorateur de fonction noyau :


    ```c#
    // Create a kernel function to get recently played songs
    [KernelFunction("GetRecentPlays")]
    public static string GetRecentPlays()
    ```

    Le décorateur `KernelFunction` déclare votre fonction native. Vous utilisez un nom descriptif pour la fonction afin que l’IA puisse l’appeler correctement. La liste des lectures récentes de l’utilisateur est stockée dans un fichier texte appelé « RecentlyPlayed.txt ».

1. Sous le commentaire **Créer une fonction noyau pour ajouter une chanson à la liste récemment écoutée**, ajoutez le décorateur de fonction noyau :

    ```c#
    // Create a kernel function to add a song to the recently played list
    [KernelFunction("AddToRecentPlays")]
    public static string AddToRecentlyPlayed(string artist,  string song, string genre)
    ```

    À présent, lorsque cette classe de plug-in est ajoutée au noyau, elle sera en mesure d’identifier et d’appeler les fonctions.

1. Accédez au fichier **Program.cs**.

1. Ajoutez le code suivant sous **Importer des plug-ins vers le noyau** :

    ```c#
    // Import plugins to the kernel
    kernel.ImportPluginFromType<MusicLibraryPlugin>();
    ```

1. Sous le commentaire **Créer des paramètres d’exécution d’invite**, ajoutez le code suivant pour appeler automatiquement la fonction :

    ```c#
    // Create prompt execution settings
    OpenAIPromptExecutionSettings openAIPromptExecutionSettings = new() 
    {
        FunctionChoiceBehavior = FunctionChoiceBehavior.Auto()
    };
    ```

    L’utilisation de ce paramètre permet au noyau d’appeler automatiquement des fonctions sans avoir à les spécifier dans l’invite.

1. Ajoutez le code suivant sous le commentaire **Obtenir le service de saisie semi-automatique de conversation** :

    ```c#
    // Get chat completion service.
    var chatCompletionService = kernel.GetRequiredService<IChatCompletionService>();
    ChatHistory chatHistory = [];
    ```

1. Ajoutez le code suivant sous le commentaire **Créer une fonction d’assistance pour attendre et générer la réponse à partir du service de saisie semi-automatique de conversation** :

    ```c#
    // Create a helper function to await and output the reply from the chat completion service
    async Task GetAssistantReply() {
        ChatMessageContent reply = await chatCompletionService.GetChatMessageContentAsync(
            chatHistory,
            kernel: kernel,
            executionSettings: openAIPromptExecutionSettings
        );
        chatHistory.AddAssistantMessage(reply.ToString());
        Console.WriteLine(reply.ToString());
    }
    ```


1. Ajoutez le code suivant sous le commentaire **Ajouter des messages système à la conversation** :

    ```c#
    // Add system messages to the chat
    chatHistory.AddSystemMessage("When a user has played a song, add it to their list of recent plays.");
    chatHistory.AddSystemMessage("The listener has just played the song Danse by Tiara. It falls under these genres: French pop, electropop, pop.");
    await GetAssistantReply();
    ```

1. Exécutez le code en entrant `dotnet run` dans le terminal.

    Les invites de message système que vous avez ajoutées doivent appeler les fonctions de plug-in et vous devez voir la sortie suivante :

    ```output
    Added 'Danse' to recently played
    ```

    Si vous ouvrez **Files/RecentlyPlayed.txt**, vous devez voir la nouvelle chanson ajoutée à la liste.

### Tâche 2 : Fournir des recommandations personnalisées sur les chansons

Dans cette tâche, vous créez une invite qui fournit des recommandations de chanson personnalisées à l’utilisateur en fonction de ses chansons récemment lues. L’invite combine les fonctions natives pour générer une recommandation de chanson. Vous créez également une fonction à partir de l’invite pour la rendre réutilisable.

1. Dans votre fichier **Program.cs**, ajoutez le code suivant sous le commentaire **Créer une fonction de suggesteur de chansons à l’aide d’une invite** :

    ```c#
    // Create a song suggester function using a prompt
    var songSuggesterFunction = kernel.CreateFunctionFromPrompt(
        promptTemplate: @"This is a list of music available to the user:
        {{MusicLibraryPlugin.GetMusicLibrary}} 

        This is a list of music the user has recently played:
        {{MusicLibraryPlugin.GetRecentPlays}}

        Based on their recently played music, suggest a song from
        the list to play next",
        functionName: "SuggestSong",
        description: "Suggest a song to the user"
    );

    kernel.Plugins.AddFromFunctions("SuggestSongPlugin", [songSuggesterFunction]);
    ```

    Dans ce code, vous créez une fonction à partir de votre prompt et vous l’ajoutez aux plug-ins du noyau.

1. Sous le commentaire **Appeler la fonction de suggesteur de chansons avec une invite de l’utilisateur***, ajoutez le code suivant :

    ```c#
    // Invoke the song suggester function with a prompt from the user
    chatHistory.AddUserMessage("What song should I play next?");
    await GetAssistantReply();
    ```

    À présent, votre application peut appeler automatiquement vos fonctions de plug-in en fonction de la demande de l’utilisateur. Étendons ce code pour fournir des recommandations de concerts ainsi qu’en fonction des informations de l’utilisateur.

### Tâche 3 : Fournir des recommandations de concert personnalisées

Dans cette tâche, vous créez un plug-in qui demande au LLM de suggérer un concert basé sur les chansons récemment écoutées par l’utilisateur et son emplacement.

1. Dans votre fichier **Program.cs**, importez le plug-in de concerts sous le commentaire **Importer des plug-ins vers le noyau** :

    ```c#
    // Import plugins to the kernel 
    kernel.ImportPluginFromType<MusicLibraryPlugin>();
    kernel.ImportPluginFromType<MusicConcertsPlugin>();
    ```

1. Ajoutez le code suivant sous le commentaire **Créer une fonction de suggesteur de concert à l’aide d’une invite** :

    ```c#
    // Create a concert suggester function using a prompt
    var concertSuggesterFunction = kernel.CreateFunctionFromPrompt(
        promptTemplate: @"This is a list of the user's recently played songs:
        {{MusicLibraryPlugin.GetRecentPlays}}

        This is a list of upcoming concert details:
        {{MusicConcertsPlugin.GetConcerts}}

        Suggest an upcoming concert based on the user's recently played songs. 
        The user lives in {{$location}}, 
        please recommend a relevant concert that is close to their location.",
        functionName: "SuggestConcert",
        description: "Suggest a concert to the user"
    );

    kernel.Plugins.AddFromFunctions("SuggestConcertPlugin", [concertSuggesterFunction]);
    ```

    Ce prompt de fonction prend en compte la bibliothèque musicale et les informations sur les concerts à venir, ainsi que l’emplacement de l’utilisateur et fournit une recommandation.

1. Ajoutez le prompt suivant pour appeler la nouvelle fonction de plug-in :

    ```c#
    // Invoke the concert suggester function with a prompt from the user
    chatHistory.AddUserMessage("Can you recommend a concert for me? I live in Washington");
    await GetAssistantReply();
    ```

1. Dans le terminal, entrez `dotnet run`.

    Vous devriez obtenir un résultat similaire à la réponse suivante :

    ```output
    I recommend you attend the concert of Lisa Taylor. She will be performing in Seattle, Washington, USA on 2/22/2024. Enjoy the show!
    ```
    
    Votre réponse du LLM peut varier. Essayez d’ajuster votre invite et votre emplacement pour voir quels autres résultats vous pouvez obtenir.

Votre assistant est maintenant en mesure d’effectuer différentes actions automatiquement en fonction de l’entrée utilisateur. Beau travail !

### Révision

Dans ce labo, vous avez créé un assistant IA qui peut gérer la bibliothèque musicale de l’utilisateur et fournir des recommandations personnalisées de chansons et de concerts. Vous avez utilisé le kit de développement logiciel (SDK) Semantic Kernel pour générer l’assistant IA et le connecter au service de grand modèle de langage (LLM). Vous avez créé des plug-ins personnalisés pour votre bibliothèque de musique, et activé l’appel automatique de fonction pour que votre assistant réponde dynamiquement à l’entrée utilisateur. Félicitations, vous avez terminé ce labo !
