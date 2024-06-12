---
lab:
  title: 'Labo : Développer des agents IA à l’aide d’Azure OpenAI et du SDK de noyau sémantique'
  module: 'Module 01: Build your kernel'
---

# Labo : Créer un agent de recommandation de musique IA
# Manuel de labo de l’étudiant

Dans ce labo, vous créez le code d’un agent IA qui peut gérer la bibliothèque musicale de l’utilisateur et fournir des recommandations personnalisées en matière de chanson et de concert. Vous utilisez le Kit de développement logiciel (SDK) du noyau sémantique pour générer l’agent IA et le connecter au service LLM (Large Language Model). Le SDK de noyau sémantique vous permet de créer une application intelligente qui peut interagir avec le service LLM et fournir des recommandations personnalisées à l’utilisateur.

## Scénario de laboratoire

Vous êtes développeur pour un service de streaming audio international. Vous avez été chargé d’intégrer le service à l’IA pour fournir aux utilisateurs une expérience plus personnalisée. L’IA doit être en mesure de recommander des chansons et des tournées d’artistes à venir en fonction de l’historique d’écoute et des préférences de l’utilisateur. Vous décidez d’utiliser le Kit de développement logiciel (SDK) de noyau sémantique pour générer un agent IA capable d’interagir avec le service LLM (Large Language Model).

## Objectifs

En effectuant ce labo, vous allez effectuer les opérations suivantes :

* Créer un point de terminaison pour le service LLM (Large Language Model, grand modèle de langage)
* Générer un objet Semantic Kernel
* Exécuter des prompts à l’aide du kit SDK Semantic Kernel
* Créer des fonctions et des plug-ins de noyau sémantique
* Utiliser le planificateur Handlebars pour automatiser les tâches

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

1. Téléchargez le fichier zip situé sur `https://github.com/MicrosoftLearning/AZ-2005-Develop-AI-agents-OpenAI-Semantic-Kernel-SDK/blob/master/Allfiles/Labs/01/Lab-01-Starter.zip`.

1. Extrayez le contenu du fichier zip vers un emplacement facile à trouver et à mémoriser, par exemple un dossier sur votre Bureau.

1. Ouvrez Visual Studio Code, puis sélectionnez **Fichier** > **Ouvrir le dossier**.

1. Accédez au dossier **Starter** que vous avez extrait, puis sélectionnez **Sélectionner un dossier**.

1. Ouvrez le fichier **Program.cs** dans l’éditeur de code.

## Exercice 1 : Exécuter une invite avec le Kit de développement logiciel (SDK) du noyau sémantique

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

    Vous allez utiliser les données ici dans la tâche suivante pour générer votre noyau. N’oubliez pas de garder vos clés privées et sécurisées !

### Tâche 2 : Générer votre noyau

Dans cet exercice, vous apprenez à créer votre premier projet de Kit de développement logiciel (SDK) Semantic Kernel (Noyau sémantique). Vous apprenez à créer un projet, à ajouter le package NuGet du Kit de développement logiciel (SDK) Semantic Kernel et à ajouter une référence au Kit de développement logiciel (SDK) Semantic Kernel. C’est parti !

1. Retournez à votre projet Visual Studio Code.

1. Ouvrez le terminal en sélectionnant **Terminal** > **Nouveau terminal**.

1. Dans le terminal, exécutez la commande suivante pour installer le Kit de développement logiciel (SDK) Semantic Kernel :

    `dotnet add package Microsoft.SemanticKernel --version 1.2.0`

1. Pour créer le noyau, ajoutez le code suivant à votre fichier « Program.cs » :
    
    ```c#
    using Microsoft.SemanticKernel;

    var builder = Kernel.CreateBuilder();
    builder.AddAzureOpenAIChatCompletion(
        "your-deployment-name",
        "your-endpoint",
        "your-api-key",
        "deployment-model");
    var kernel = builder.Build();
    ```

    Veillez à remplacer les espaces réservés par les valeurs de votre ressource Azure.

1. Pour vérifier que votre noyau et votre point de terminaison fonctionnent, entrez le code suivant :

    ```c#
    var result = await kernel.InvokePromptAsync(
        "Who are the top 5 most famous musicians in the world?");
    Console.WriteLine(result);
    ```

1. Exécutez le code et vérifiez que vous voyez une réponse du modèle Azure Open AI contenant les 5 plus célèbres musiciens du monde.

    La réponse provient du modèle Azure Open AI que vous avez transmis au noyau. Le SDK Semantic Kernel est en mesure de se connecter au grand modèle de langage (LLM) et d’exécuter l’invite. Notez la rapidité avec laquelle vous avez pu recevoir des réponses du LLM. Le Kit de développement logiciel (SDK) Semantic Kernel permet de générer facilement et efficacement des applications intelligentes.

## Exercice 2 : Créer des plug-ins de bibliothèque de musique personnalisées

Dans cet exercice, vous créez des plug-ins personnalisés pour votre bibliothèque de musique. Vous créez des fonctions qui peuvent ajouter des chansons à la liste récemment lue de l’utilisateur, obtenir la liste des chansons récemment lues et fournir des recommandations personnalisées sur les chansons. Vous créez également une fonction qui suggère un concert basé sur l’emplacement de l’utilisateur et leurs chansons récemment jouées.

**Durée d’exécution estimée de l’exercice** : 15 minutes

### Tâche 1 : Créer un plug-in de bibliothèque de musique

Dans cette tâche, vous créez un plug-in qui vous permet d’ajouter des chansons à la liste récemment lue de l’utilisateur et d’obtenir la liste des chansons récemment lues. Par souci de simplicité, les chansons récemment lues sont stockées dans un fichier texte.

1. Créez un dossier dans le répertoire « Lab01-Project » et nommez-le « Plugins ».

1. Dans le dossier « Plugins », créez un fichier « MusicLibrary.cs »

    Tout d’abord, créez des fonctions rapides pour obtenir et ajouter des chansons à la liste « Récemment écouté » de l’utilisateur.

1. Entrez le code suivant :

    ```c#
    using System.ComponentModel;
    using System.Text.Json;
    using System.Text.Json.Nodes;
    using Microsoft.SemanticKernel;

    public class MusicLibraryPlugin
    {
        [KernelFunction, 
        Description("Get a list of music recently played by the user")]
        public static string GetRecentPlays()
        {
            string content = File.ReadAllText($"Files/RecentlyPlayed.txt");
            return content;
        }
    }
    ```

    Dans ce code, vous utilisez le décorateur `KernelFunction` pour déclarer votre fonction native. Vous utilisez également le décorateur `Description` pour ajouter une description de ce que fait la fonction. La liste des lectures récentes de l’utilisateur est stockée dans un fichier texte appelé « RecentlyPlayed.txt ». Ensuite, vous pouvez ajouter du code pour ajouter une chanson à la liste.

1. Ajoutez le code suivant à la classe `MusicLibraryPlugin` :

    ```c#
    [KernelFunction, Description("Add a song to the recently played list")]
    public static string AddToRecentlyPlayed(
        [Description("The name of the artist")] string artist, 
        [Description("The title of the song")] string song, 
        [Description("The song genre")] string genre)
    {
        // Read the existing content from the file
        string filePath = "Files/RecentlyPlayed.txt";
        string jsonContent = File.ReadAllText(filePath);
        var recentlyPlayed = (JsonArray) JsonNode.Parse(jsonContent);

        var newSong = new JsonObject
        {
            ["title"] = song,
            ["artist"] = artist,
            ["genre"] = genre
        };

        recentlyPlayed.Insert(0, newSong);
        File.WriteAllText(filePath, JsonSerializer.Serialize(recentlyPlayed,
            new JsonSerializerOptions { WriteIndented = true }));

        return $"Added '{song}' to recently played";
    }
    ```

    Dans ce code, vous créez une fonction qui accepte l’artiste, la chanson et le genre sous forme de chaînes. En plus de la fonction `Description`, vous ajoutez également des descriptions des variables d’entrée. Le fichier « RecentlyPlayed.txt » contient la liste json mise en forme des chansons que l’utilisateur a récemment lues. Ce code lit le contenu existant du fichier, l’analyse et ajoute la nouvelle chanson à la liste. Ensuite, la liste mise à jour est réécrite dans le fichier.

1. Mettez à jour votre fichier « Program.cs » avec le code suivant :

    ```c#
    var kernel = builder.Build();
    kernel.ImportPluginFromType<MusicLibraryPlugin>();

    var result = await kernel.InvokeAsync(
        "MusicLibraryPlugin", 
        "AddToRecentlyPlayed", 
        new() {
            ["artist"] = "Tiara", 
            ["song"] = "Danse", 
            ["genre"] = "French pop, electropop, pop"
        }
    );
    
    Console.WriteLine(result);
    ```

    Dans ce code, vous importez le `MusicLibraryPlugin` dans le noyau en utilisant `ImportPluginFromType`. Ensuite, vous appelez `InvokeAsync` avec le nom du plug-in et le nom de la fonction que vous voulez appeler. Vous passez également l’artiste, la chanson et le genre comme arguments.

1. Exécutez le code en entrant `dotnet run` dans le terminal.

    Vous devez normalement voir la sortie suivante.

    ```output
    Added 'Danse' to recently played
    ```

    Si vous ouvrez « RecentlyPlayed.txt », vous devez voir la nouvelle chanson ajoutée à la liste.

### Tâche 2 : Fournir des recommandations personnalisées sur les chansons

Dans cette tâche, vous créez une invite qui fournit des recommandations de chanson personnalisées à l’utilisateur en fonction de ses chansons récemment lues. L’invite combine les fonctions natives pour générer une recommandation de chanson. Vous créez également une fonction à partir de l’invite pour la rendre réutilisable.

1. Dans votre fichier `MusicLibraryPlugin.cs`, ajoutez la fonction suivante :

    ```c#
    [KernelFunction, Description("Get a list of music available to the user")]
    public static string GetMusicLibrary()
    {
        string dir = Directory.GetCurrentDirectory();
        string content = File.ReadAllText($"Files/MusicLibrary.txt");
        return content;
    }
    ```

    Cette fonction lit la liste des musiques disponibles à partir d’un fichier nommé « MusicLibrary.txt ». Le fichier contient une liste json mise en forme de chansons disponibles pour l’utilisateur.

1. Mettez à jour votre fichier « Program.cs » avec le code suivant :

    ```c#
    var kernel = builder.Build();
    kernel.ImportPluginFromType<MusicLibraryPlugin>();
    
    string prompt = @"This is a list of music available to the user:
        {{MusicLibraryPlugin.GetMusicLibrary}} 

        This is a list of music the user has recently played:
        {{MusicLibraryPlugin.GetRecentPlays}}

        Based on their recently played music, suggest a song from
        the list to play next";

    var result = await kernel.InvokePromptAsync(prompt);
    Console.WriteLine(result);
    ```

Dans ce code, vous combinez vos fonctions natives avec un prompt sémantique. Les fonctions natives a la capacité de récupérer des données utilisateur auxquelles le grand modèle de langage (LLM) n’a pas pu accéder par lui-même, et le LLM a la capacité de générer une recommandation de chanson en fonction de l’entrée texte.

1. Pour tester votre code, entrez `dotnet run` dans le terminal.

    Vous devez voir une réponse similaire à la sortie suivante :

    ```output 
    Based on the user's recently played music, a suggested song to play next could be "Sabry Aalil" by Yasemin since the user seems to enjoy pop and Egyptian pop music.
    ```

    >[!NOTE] La chanson recommandée peut être différente de celle présentée ici.

1. Modifiez le code pour créer une fonction à partir de l’invite :

    ```c#
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

    var result = await kernel.InvokeAsync(songSuggesterFunction);
    Console.WriteLine(result);
    ```

    Dans ce code, vous créez une fonction à partir d’une invite qui suggère une chanson à l’utilisateur. Vous l’ajoutez ensuite au noyau Plug-ins. Enfin, vous indiquez au noyau d’exécuter la fonction.

### Tâche 3 : Fournir des recommandations de concert personnalisées

Dans cette tâche, vous créez un plug-in qui récupère les détails des concerts à venir. Vous créez également un plug-in qui demande au LLM de suggérer un concert basé sur les chansons et l’emplacement récemment joués par l’utilisateur.

1. Dans le dossier « Plugins », créez un fichier nommé « MusicConcertPlugin.cs »

1. Dans le fichier MusicConcertPlugin, ajoutez le code suivant :

    ```c#
    using System.ComponentModel;
    using Microsoft.SemanticKernel;

    public class MusicConcertPlugin
    {
        [KernelFunction, Description("Get a list of upcoming concerts")]
        public static string GetTours()
        {
            string content = File.ReadAllText($"Files/ConcertDates.txt");
            return content;
        }
    }
    ```

    Dans ce code, vous créez une fonction qui lit la liste des concerts à venir à partir d’un fichier nommé « ConcertDates.txt ». Le fichier contient une liste json mise en forme des concerts à venir. Ensuite, vous devez créer une invite pour demander au LLM de suggérer un concert.

1. Dans le dossier « Invites », créez un dossier nommé « SuggestConcert »

1. Créez un fichier « config.json » dans le dossier « SuggestConcert » avec le contenu suivant :

    ```json
    {
        "schema": 1,
        "type": "completion",
        "description": "Suggest a concert to the user",
        "execution_settings": {
            "default": {
                "max_tokens": 4000,
                "temperature": 0
            }
        },
        "input_variables": [
            {
                "name": "location",
                "description": "The user's location",
                "required": true
            }
        ]
    }
    ```

1. Créez un fichier « skprompt.txt » dans le dossier « SuggestConcert » avec le contenu suivant :

    ```output
    This is a list of the user's recently played songs:
    {{MusicLibraryPlugin.GetRecentPlays}}

    This is a list of upcoming concert details:
    {{MusicConcertsPlugin.GetConcerts}}

    Suggest an upcoming concert based on the user's recently played songs. 
    The user lives in {{$location}}, 
    please recommend a relevant concert that is close to their location.
    ```

    Cette invite permet au LLM de filtrer l’entrée de l’utilisateur et de récupérer uniquement la destination à partir du texte. Vous appelez ensuite le planificateur pour créer un plan qui combine les plug-ins afin d’atteindre l’objectif.

1. Ouvrez votre fichier « Program.cs » et mettez-le à jour avec le code suivant :

    ```c#
    var kernel = builder.Build();    
    kernel.ImportPluginFromType<MusicLibraryPlugin>();
    kernel.ImportPluginFromType<MusicConcertsPlugin>();
    var prompts = kernel.ImportPluginFromPromptDirectory("Prompts");

    var songSuggesterFunction = kernel.CreateFunctionFromPrompt(
    // code omitted for brevity
    );

    kernel.Plugins.AddFromFunctions("SuggestSongPlugin", [songSuggesterFunction]);

    string location = "Redmond WA USA";
    var result = await kernel.InvokeAsync<string>(prompts["SuggestConcert"],
        new() {
            { "location", location }
        }
    );
    Console.WriteLine(result);
    ```

1. Dans le terminal, entrez `dotnet run`.

    Vous devriez obtenir un résultat similaire à la réponse suivante :

    ```output
    Based on the user's recently played songs and their location in Redmond WA USA, a relevant concert recommendation would be the upcoming concert of Lisa Taylor in Seattle WA, USA on February 22, 2024. Lisa Taylor is an indie-folk artist, and her music genre aligns with the user's recently played songs, such as "Loanh Quanh" by Ly Hoa. Additionally, Seattle is close to Redmond, making it a convenient location for the user to attend the concert.
    ```

    Essayez d’ajuster votre invite et votre emplacement pour voir quels autres résultats vous pouvez obtenir.

## Exercice 3 : Automatiser les suggestions avec le plan Handlebars

Le planificateur Handlebars est utile lorsque vous avez plusieurs étapes nécessaires pour accomplir une tâche. Les planificateurs utilisent l’IA pour sélectionner et combiner les plug-ins inscrits au noyau dans une série d’étapes pour atteindre un objectif. Dans cet exercice, vous générez un modèle de plan à l’aide du planificateur Handlebars et l’utilisez pour automatiser les suggestions.

**Durée d’exécution estimée de l’exercice** : 10 minutes

### Tâche 1 : Générer un modèle de plan

Dans cette tâche, vous générez un modèle de plan à l’aide du planificateur Handlebars. Le modèle de plan sera utilisé pour automatiser les suggestions en fonction de l’entrée de l’utilisateur.

1. Installez le planificateur Handlebars en entrant les éléments suivants dans le terminal :

    `dotnet add package Microsoft.SemanticKernel.Planners.Handlebars --version 1.2.0-preview`

    Ensuite, vous remplacez l’invite SuggestConcert et utilisez le planificateur Handlebars pour effectuer la tâche à la place.

1. Dans votre fichier « Program.cs », mettez à jour votre code comme suit :

    ```c#
    var kernel = builder.Build();
    kernel.ImportPluginFromType<MusicLibraryPlugin>();
    kernel.ImportPluginFromType<MusicConcertPlugin>();
    kernel.ImportPluginFromPromptDirectory("Prompts");

    var planner = new HandlebarsPlanner(new HandlebarsPlannerOptions() { AllowLoops = true });

    string location = "Redmond WA USA";
    string goal = @$"Based on the user's recently played music, suggest a 
        concert for the user living in ${location}";

    var plan = await planner.CreatePlanAsync(kernel, goal);
    var result = await plan.InvokeAsync(kernel);

    Console.WriteLine($"{result}");
    ```

1. Dans le terminal, entrez `dotnet run`.

    Vous devez voir une réponse similaire à la sortie suivante :

    ```output
    Based on the user's recently played songs, the artist "Mademoiselle" has an upcoming concert in Seattle WA, USA on February 22, 2024, which is close to Redmond WA. Therefore, the recommended concert for the user would be Mademoiselle's concert in Seattle.
    ```

    Ensuite, vous modifiez le code pour générer le modèle de plan Handlebars.

1. Dans votre fichier « Program.cs », mettez à jour votre code comme suit :

    ```c#
    var plan = await planner.CreatePlanAsync(kernel, goal);
    Console.WriteLine("Plan:");
    Console.WriteLine(plan);
    ```

    Vous pouvez désormais voir le plan généré. Ensuite, modifiez le plan pour inclure des suggestions de chansons ou ajouter une chanson à la liste récemment lue de l’utilisateur.

1. Étendez votre code avec l’extrait de code suivant :

    ```c#
    var plan = await planner.CreatePlanAsync(kernel, 
        @$"If add song:
        Add a song to the user's recently played list.
        
        If concert recommendation:
        Based on the user's recently played music, suggest a concert for 
        the user living in a given location.

        If song recommendation:
        Suggest a song from the music library to the user based on their 
        recently played songs.");

    Console.WriteLine("Plan:");
    Console.WriteLine(plan);
    ```

1. Entrez `dotnet run` dans le terminal pour afficher la sortie des plans créés.

    Vous devez obtenir un modèle similaire à la sortie suivante :

    ```output
    Plan:
    {{!-- Step 1: Identify Key Values --}}
    {{set "location" location}}
    {{set "addSong" addSong}}
    {{set "concertRecommendation" concertRecommendation}}
    {{set "songRecommendation" concertRecommendation}}

    {{!-- Step 2: Use the Right Helpers --}}
    {{#if addSong}}
        {{set "song" song}}
        {{set "artist" artist}}
        {{set "genre" genre}}
        {{set "songAdded" (MusicLibraryPlugin-AddToRecentlyPlayed artist=artist song=song genre=genre)}}  
        {{json songAdded}}
    {{/if}}

    {{#if concertRecommendation}}
        {{set "concertSuggested" (Prompts-SuggestConcert location=location recentlyPlayedSongs=recentlyPlayedSongs musicLibrary=musicLibrary)}}
        {{json concertSuggested}}
    {{/if}}

    {{#if songRecommendation}}
        {{set "songSuggested" (SuggestSongPlugin-SuggestSong recentlyPlayedSongs=recentlyPlayedSongs musicLibrary=musicLibrary)}}
        {{json songSuggested}}
    {{/if}}

    {{!-- Step 3: Output the Result --}}
    {{json "Goal achieved"}}
    ```

     Notez que la syntaxe est `{{#if ...}}`. Cette syntaxe agit comme une instruction conditionnelle que le planificateur Handlebars peut utiliser, similaire à un bloc traditionnel `if`-`else` en C#. L’instruction `if` doit être fermée avec `{{/if}}`.

    Ensuite, vous utilisez ce modèle généré pour créer votre propre plan Handlebars. 

1. Créez un fichier nommé « handlebarsTemplate.txt » ayant le texte suivant :

    ```output
    {{set "addSong" addSong}}
    {{set "concertRecommendation" concertRecommendation}}
    {{set "songRecommendation" songRecommendation}}

    {{#if addSong}}
        {{set "song" song}}
        {{set "artist" artist}}
        {{set "genre" genre}}
        {{set addedSong (MusicLibraryPlugin-AddToRecentlyPlayed artist song genre)}}  
        Output The following content, do not make any modifications:
        {{json addedSong}}     
    {{/if}}

    {{#if concertRecommendation}}
        {{set "location" location}}
        {{set "concert" (Prompts-SuggestConcert location)}}
        Output The following content, do not make any modifications:
        {{json concert}}
    {{/if}}

    {{#if songRecommendation}}
        {{set "recentlyPlayedSongs" (MusicLibraryPlugin-GetRecentPlays)}}
        {{set "musicLibrary" (MusicLibraryPlugin-GetMusicLibrary)}}
        {{set "song" (SuggestSongPlugin-SuggestSong recentlyPlayedSongs musicLibrary)}}
        Output The following content, do not make any modifications:
        {{json song}}
    {{/if}}
    ```

    Dans ce modèle, vous ajoutez une instruction au LLM pour ne pas effectuer de génération de texte pour vous assurer que la sortie est strictement gérée par les plug-ins. Nous allons maintenant essayer le modèle !

### Tâche 2 : Utiliser le planificateur Handlebars pour automatiser les suggestions

Dans cette tâche, vous créez une fonction à partir du modèle de plan Handlebars et utilisez-la pour automatiser les suggestions en fonction de l’entrée de l’utilisateur.

1. Supprimez les plans Handlebars en modifiant votre code existant :

    ```c#
    var builder = Kernel.CreateBuilder();
    builder.AddAzureOpenAIChatCompletion(
        "your-deployment-name",
        "your-endpoint",
        "your-api-key",
        "deployment-model");
    var kernel = builder.Build();
    kernel.ImportPluginFromType<MusicLibraryPlugin>();
    kernel.ImportPluginFromType<MusicConcertsPlugin>();
    kernel.ImportPluginFromPromptDirectory("Prompts");
    
    var songSuggesterFunction = kernel.CreateFunctionFromPrompt(
        promptTemplate: @"Based on the user's recently played music:
        {{$recentlyPlayedSongs}}
        recommend a song to the user from the music library:
        {{$musicLibrary}}",
        functionName: "SuggestSong",
        description: "Suggest a song to the user"
    );

    kernel.Plugins.AddFromFunctions("SuggestSongPlugin", [songSuggesterFunction]);
    ```

1. Ajoutez du code qui lit le fichier de modèle crée une fonction :

    ```c#
    string template = File.ReadAllText($"handlebarsTemplate.txt");

    var handlebarsPromptFunction = kernel.CreateFunctionFromPrompt(
        new() {
            Template = template,
            TemplateFormat = "handlebars"
        }, new HandlebarsPromptTemplateFactory()
    );
    ```

    Dans ce code, vous passez un objet `Template` à la méthode `CreateFunctionFromPrompt` du noyau, ainsi que le `TemplateFormat`. `CreateFunctionFromPrompt` accepte également un type `IPromptTemplateFactory` qui indique au noyau comment analyser un modèle donné. Étant donné que vous utilisez un modèle Handlebars, vous utilisez le type `HandlebarsPromptTemplateFactory`.

    Nous allons ensuite exécuter la fonction avec quelques arguments et consulter les résultats.

1. Ajoutez le code suivant à votre fichier `Program.cs` :

    ```c#
    string location = "Redmond WA USA";
    var templateResult = await kernel.InvokeAsync(handlebarsPromptFunction,
        new() {
            { "location", location },
            { "concertRecommendation", true },
            { "songRecommendation", false },
            { "addSong", false },
            { "artist", "" },
            { "song", "" },
            { "genre", "" }
        });

    Console.WriteLine(templateResult);
    ```

1. Entrez `dotnet run` dans le terminal pour voir la sortie de votre modèle de planificateur.

    Une réponse similaire à la sortie suivante doit s’afficher :

    ```output
    Based on the user's recently played songs, Ly Hoa seems to be a relevant artist. The closest concert to Redmond WA, USA would be in Portland OR, USA on April 16th, 2024.  
    ```

    L’invite a pu suggérer un concert à l’utilisateur en fonction de la liste des musiques récemment lues et de leur emplacement. Vous pouvez également essayer de définir d’autres variables sur true et voir ce qui se passe !

Votre code est maintenant en mesure d’effectuer différentes actions en fonction de l’entrée de l’utilisateur. Beau travail !

### Révision

Dans ce laboratoire, vous avez créé un agent IA capable de gérer la bibliothèque musicale de l’utilisateur et de fournir des recommandations personnalisées en matière de chanson et de concert. Vous avez utilisé le Kit de développement logiciel (SDK) du noyau sémantique pour générer l’agent IA et le connecter au service LLM (Large Language Model). Vous avez créé des plug-ins personnalisés pour votre bibliothèque de musique, utilisé le planificateur Handlebars pour automatiser les suggestions et créé une fonction à partir du modèle de plan Handlebars pour automatiser les suggestions en fonction de l’entrée de l’utilisateur. Félicitations pour avoir terminé ce labo !