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

1. Pour créer le noyau, ajoutez le code suivant à votre fichier **Program.cs** :
    
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

1. Entrez `dotnet run` pour exécuter le code et vérifiez que vous voyez une réponse du modèle Azure Open AI contenant les 5 plus célèbres musiciens du monde.

    La réponse provient du modèle Azure Open AI que vous avez transmis au noyau. Le SDK Semantic Kernel est en mesure de se connecter au grand modèle de langage (LLM) et d’exécuter l’invite. Notez la rapidité avec laquelle vous avez pu recevoir des réponses du LLM. Le Kit de développement logiciel (SDK) Semantic Kernel permet de générer facilement et efficacement des applications intelligentes.

## Exercice 2 : Créer des plug-ins de bibliothèque de musique personnalisées

Dans cet exercice, vous créez des plug-ins personnalisés pour votre bibliothèque de musique. Vous créez des fonctions qui peuvent ajouter des chansons à la liste récemment lue de l’utilisateur, obtenir la liste des chansons récemment lues et fournir des recommandations personnalisées sur les chansons. Vous créez également une fonction qui suggère un concert basé sur l’emplacement de l’utilisateur et leurs chansons récemment jouées.

**Durée d’exécution estimée de l’exercice** : 15 minutes

### Tâche 1 : Créer un plug-in de bibliothèque de musique

Dans cette tâche, vous créez un plug-in qui vous permet d’ajouter des chansons à la liste récemment lue de l’utilisateur et d’obtenir la liste des chansons récemment lues. Par souci de simplicité, les chansons récemment lues sont stockées dans un fichier texte.

1. Créez un dossier dans le répertoire « Lab01-Project » et nommez-le « Plugins ».

1. Dans le dossier « Plugins », créez un fichier « MusicLibraryPlugin.cs »

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

1. Mettez à jour votre fichier **Program.cs** avec le code suivant :

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

    Dans ce code, vous importez le plug-in MusicLibraryPlugin dans le noyau à l’aide de la fonction ImportPluginFromType. Ensuite, vous appelez InvokeAsync avec le nom du plug-in et le nom de la fonction que vous voulez appeler. Vous passez également l’artiste, la chanson et le genre comme arguments.

1. Exécutez le code en entrant `dotnet run` dans le terminal.

    Vous devez normalement voir la sortie suivante :

    ```output
    Added 'Danse' to recently played
    ```

    Si vous ouvrez « Files/RecentlyPlayed.txt », vous devez voir la nouvelle chanson ajoutée à la liste.

> [!NOTE]
> Si le terminal affiche des avertissements pour les valeurs Null, vous pouvez les ignorer, car ils n’affectent pas le résultat.

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

1. Mettez à jour votre fichier **Program.cs** avec le code suivant :

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

1. Dans le dossier « Plugins », créez un fichier nommé « MusicConcertsPlugin.cs ».

1. Dans le fichier « MusicConcertsPlugin », ajoutez le code suivant :

    ```c#
    using System.ComponentModel;
    using Microsoft.SemanticKernel;

    public class MusicConcertsPlugin
    {
        [KernelFunction, Description("Get a list of upcoming concerts")]
        public static string GetConcerts()
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

    Cette invite permet au LLM de filtrer l’entrée de l’utilisateur et de récupérer uniquement la destination à partir du texte. Ensuite, testez vos plug-ins pour vérifier la sortie.

1. Ouvrez votre fichier **Program.cs** et mettez-le à jour avec le code suivant :

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

## Exercice 3 : automatiser les suggestions en fonction de l’entrée utilisateur

Vous pouvez éviter d’appeler manuellement les fonctions de plug-in grâce à l’appel automatique de fonction. Le LLM sélectionne et combine automatiquement les plug-ins inscrits au noyau pour atteindre l’objectif. Dans cet exercice, vous allez activer l’appel automatique de fonction pour automatiser les suggestions.

**Durée d’exécution estimée de l’exercice** : 10 minutes

### Tâche 1 : automatiser les suggestions en fonction de l’entrée utilisateur

Dans cette tâche, vous allez activer l’appel automatique de fonction pour générer des suggestions en fonction de l’entrée utilisateur.

1. Dans votre fichier **Program.cs**, mettez à jour votre code comme suit :

    ```c#
    using Microsoft.SemanticKernel;
    using Microsoft.SemanticKernel.Connectors.OpenAI;
    
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

    OpenAIPromptExecutionSettings settings = new()
    {
        ToolCallBehavior = ToolCallBehavior.AutoInvokeKernelFunctions
    };
    
    string prompt = @$"Based on the user's recently played music, suggest a 
        concert for the user living in ${location}";

    var autoInvokeResult = await kernel.InvokePromptAsync(prompt, new(settings));
    Console.WriteLine(autoInvokeResult);
    ```

1. Dans le terminal, entrez `dotnet run`.

    Vous devez voir une réponse similaire à la sortie suivante :

    ```output
    Based on the user's recently played songs, the artist "Mademoiselle" has an upcoming concert in Seattle WA, USA on February 22, 2024, which is close to Redmond WA. Therefore, the recommended concert for the user would be Mademoiselle's concert in Seattle.
    ```

    Le noyau sémantique est en mesure d’appeler automatiquement la fonction `SuggestConcert` à l’aide des paramètres appropriés. Votre agent peut maintenant suggérer un concert à l’utilisateur en fonction de la liste des musiques récemment lues et de leur emplacement. Ensuite, vous pouvez ajouter la prise en charge des suggestions de musiques.

1. Mettez à jour votre fichier **Program.cs** avec le code suivant :

    ```c#
    OpenAIPromptExecutionSettings settings = new()
    {
        ToolCallBehavior = ToolCallBehavior.AutoInvokeKernelFunctions
    };
    
    var songSuggesterFunction = kernel.CreateFunctionFromPrompt(
        promptTemplate: @"Based on the user's recently played music:
        {{$recentlyPlayedSongs}}
        recommend a song to the user from the music library:
        {{$musicLibrary}}",
        functionName: "SuggestSong",
        description: "Recommend a song from the music library"
    );

    kernel.Plugins.AddFromFunctions("SuggestSongPlugin", [songSuggesterFunction]);

    string prompt = "Can you recommend a song from the music library?";

    var autoInvokeResult = await kernel.InvokePromptAsync(prompt, new(settings));
    Console.WriteLine(autoInvokeResult);
    ```

    Dans ce code, vous créez une fonction KernelFunction à partir d’un modèle d’invite qui indique au LLM comment suggérer une chanson. Ensuite, inscrivez-le au noyau et appelez une invite avec le paramètre d’appel automatique de fonction activé. Le noyau est en mesure d’exécuter la fonction et de fournir les paramètres appropriés pour terminer l’invite.

1. Dans le terminal, entrez `dotnet run` pour exécuter votre code.

    La sortie générée doit recommander une chanson aux utilisateurs en fonction de leur musique récemment écoutée. Votre réponse doit ressembler à la sortie suivante :
    
    ```
    Based on your recently played music, I recommend you listen to the song "Luv(sic)". It falls under the genres of hiphop and rap, which aligns with some of your recently played songs. Enjoy!  
    ```

    Ensuite, essayons une invite pour mettre à jour la liste des chansons récemment lues.

1. Mettez à jour votre fichier **Program.cs** avec le code suivant :

    ```c#
    string prompt = @"Add this song to the recently played songs list:  title: 'Touch', artist: 'Cat's Eye', genre: 'Pop'";

    var result = await kernel.InvokePromptAsync(prompt, new(settings));

    Console.WriteLine(result);
    ```

1. Entrez `dotnet run` dans le terminal.

    La sortie doit ressembler à ce qui suit :

    ```
    I have added the song 'Touch' by Cat's Eye to the recently played songs list.
    ```

    Lorsque vous ouvrez le fichier recentlyplayed.txt, vous devez voir la nouvelle chanson ajoutée en haut de la liste.
    

Utiliser le paramètre `AutoInvokeKernelFunctions` vous permet de vous concentrer sur la création de plug-ins pour répondre aux besoins de votre utilisateur. Votre agent est maintenant en mesure d’effectuer différentes actions automatiquement en fonction de l’entrée utilisateur. Beau travail !

### Révision

Dans ce laboratoire, vous avez créé un agent IA capable de gérer la bibliothèque musicale de l’utilisateur et de fournir des recommandations personnalisées en matière de chanson et de concert. Vous avez utilisé le Kit de développement logiciel (SDK) du noyau sémantique pour générer l’agent IA et le connecter au service LLM (Large Language Model). Vous avez créé des plug-ins personnalisés pour votre bibliothèque de musique, et activé l’appel automatique de fonction pour que votre agent réponde dynamiquement à l’entrée utilisateur. Félicitations, vous avez terminé ce labo !
