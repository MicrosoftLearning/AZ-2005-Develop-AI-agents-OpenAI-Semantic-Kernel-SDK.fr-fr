---
lab:
  title: Créer un Assistant IA avec Semantic Kernel
  description: Découvrez comment utiliser Semantic Kernel pour créer un assistant d’IA générative capable d’effectuer des tâches DevOps.
---

# Créer un Assistant IA avec Semantic Kernel

Dans ce labo, vous développez le code d’un assistant alimenté par l’IA conçu pour automatiser les opérations de développement et simplifier les tâches. Vous utilisez le kit de développement logiciel (SDK) Semantic Kernel pour générer l’assistant IA et le connecter au service de grand modèle de langage (LLM). Le SDK Semantic Kernel vous permet de créer une application intelligente qui peut interagir avec le service LLM, répondre aux requêtes en langage naturel et fournir des recommandations personnalisées à l’utilisateur. Pour cet exercice, les fonctions fictives sont fournies pour représenter des tâches DevOps classiques. C’est parti !

Cet exercice prend environ **30** minutes.

## Déployer un modèle de saisie semi-automatique de conversation

1. Accédez à [https://portal.azure.com](https://portal.azure.com).

1. Créez une nouvelle ressource **Azure OpenAI** à l’aide des paramètres par défaut.

1. Après la création de la ressource créée, sélectionnez **Accéder à la ressource**.

1. Sur la page **Vue d’ensemble**, sélectionnez **Accéder au portail Azure AI Foundry**.

    Le portail Azure AI Foundry devrait s’ouvrir dans un nouvel onglet.

1. Dans le volet de navigation gauche, sélectionnez **Déploiements**.

1. Sélectionnez **Déployer le modèle**, puis **Déployer le modèle de base**.

1. Recherchez **gpt-4o** dans la liste des modèles, sélectionnez-le, puis choisissez **Confirmer**.

    Une boîte de dialogue devrait s’afficher pour configurer le déploiement vers votre ressource Azure OpenAI.

1. Passez en revue les paramètres et sélectionnez **Déployer**.

    Une fois le déploiement terminé, la page des détails du déploiement s’affiche.

1. Sous **Point de terminaison**, observez l’**URI cible** et la **Clé**.

    Vous allez utiliser les valeurs indiquées ici dans la tâche suivante pour générer votre noyau. N’oubliez pas de mettre vos clés à l’abri, dans un emplacement privé et sécurisé.

## Préparer la configuration de l’application

1. Ouvrez un nouvel onglet de navigateur (en gardant le portail Azure AI Foundry ouvert dans l’onglet existant). Dans un nouvel onglet du navigateur, ouvrez le [portail Azure](https://portal.azure.com) à l’adresse `https://portal.azure.com` et connectez-vous en utilisant vos informations d’identification Azure.

    Fermez les notifications de bienvenue pour afficher la page d’accueil du portail Azure.

1. Utilisez le bouton **[\>_]** à droite de la barre de recherche, en haut de la page, pour créer un environnement Cloud Shell dans le portail Azure, puis sélectionnez un environnement ***PowerShell*** avec aucun stockage dans votre abonnement.

    Cloud Shell fournit une interface de ligne de commande via un volet situé en bas du portail Azure. Vous pouvez redimensionner ou agrandir ce volet pour faciliter le travail.

    > **Remarque** : si vous avez déjà créé un Cloud Shell qui utilise un environnement *Bash*, basculez-le vers ***PowerShell***.

1. Dans la barre d’outils Cloud Shell, dans le menu **Paramètres**, sélectionnez **Accéder à la version classique** (cela est nécessaire pour utiliser l’éditeur de code).

    **<font color="red">Assurez-vous d’avoir basculé vers la version classique du Cloud Shell avant de continuer.</font>**

1. Dans le volet Cloud Shell, entrez les commandes suivantes pour cloner le référentiel GitHub contenant les fichiers de code de cet exercice (saisissez la commande, ou copiez-la vers le presse-papiers et cliquez avec le bouton droit dans la ligne de commande pour la coller sous forme de texte brut) :

    ```
    rm -r semantic-kernel -f
    git clone https://github.com/MicrosoftLearning/AZ-2005-Develop-AI-agents-OpenAI-Semantic-Kernel-SDK semantic-kernel
    ```

    > **Conseil** : lorsque vous collez des commandes dans Cloud Shell, la sortie peut prendre une grande quantité de mémoire tampon d’écran. Vous pouvez effacer le contenu de l’écran en saisissant la commande `cls` pour faciliter le focus sur chaque tâche.

1. Une fois le référentiel cloné, accédez au dossier contenant les fichiers de code de l’application de conversation :

    > **Note** : suivez les étapes de votre langage de programmation choisi.

    **Python**
    ```
    cd semantic-kernel/Allfiles/Labs/Devops/python
    ```

    **C#**
    ```
    cd semantic-kernel/Allfiles/Labs/Devops/c-sharp
    ```

1. Dans le volet de ligne de commande Cloud Shell, saisissez la commande suivante pour installer les bibliothèques que vous allez utiliser :

    **Python**
    ```
    python -m venv labenv
    ./labenv/bin/Activate.ps1
    pip install python-dotenv azure-identity semantic-kernel[azure] 
    ```

    **C#**
    ```
    dotnet add package Microsoft.Extensions.Configuration
    dotnet add package Microsoft.Extensions.Configuration.Json
    dotnet add package Microsoft.SemanticKernel
    dotnet add package Microsoft.SemanticKernel.PromptTemplates.Handlebars
    ```

1. Saisissez la commande suivante pour modifier le fichier de configuration fourni :

    **Python**
    ```
    code .env
    ```

    **C#**
    ```
    code appsettings.json
    ```

    Le fichier s’ouvre dans un éditeur de code.

1. Mettez à jour les valeurs à partir du déploiement de votre modèle Azure OpenAI :

    **Python**
    ```python
    MODEL_ENDPOINT=""
    API_KEY="
    MODEL_DEPLOYMENT_NAME=""
    ```

    **C#**
    ```json
    {
        "openai_endpoint": "",
        "api_key": "",
        "model_deployment_name": "",
    }
    ```

> **Remarque** : Si vous utilisez C#, utilisez l’URL du point de terminaison **Azure OpenAI** sur la page **Accueil** de votre ressource pour la valeur `openai_endpoint`.

1. Une fois que vous avez remplacé les valeurs, utilisez la commande **Ctrl+S** pour enregistrer vos modifications, puis utilisez la commande **Ctrl+Q** pour fermer l’éditeur de code tout en gardant la ligne de commande Cloud Shell ouverte.

## Créer un plug-in Semantic Kernel

1. Saisissez la commande suivante pour modifier le fichier de code fourni :

    **Python**
    ```
    code devops.py
    ```

    **C#**
    ```
    code Program.cs
    ```

1. Ajoutez le code suivant sous le commentaire **Créer un générateur de noyau avec la saisie semi-automatique de conversation Azure OpenAI** :

    **Python**
    ```python
    # Create a kernel builder with Azure OpenAI chat completion
    kernel = Kernel()
    chat_completion = AzureChatCompletion(
        deployment_name=model_name,
        api_key=api_key,
        base_url=endpoint,
    )
    kernel.add_service(chat_completion)
    ```
    **C#**
     ```c#
    // Create a kernel builder with Azure OpenAI chat completion
    var builder = Kernel.CreateBuilder();
    builder.AddAzureOpenAIChatCompletion(modelName, endpoint, apiKey);
    var kernel = builder.Build();
    ```

1. Dans la classe **DevopsPlugin** vers le bas du fichier, recherchez le commentaire **Créer une fonction noyau pour générer l’environnement de test**, puis ajoutez le code suivant pour créer une fonction de plug-in fictive qui générera l’environnement de test :

    **Python**
    ```python
    # Create a kernel function to build the stage environment
    @kernel_function(name="BuildStageEnvironment")
    def build_stage_environment(self):
        return "Stage build completed."
    ```

    **C#**
    ```c#
    // Create a kernel function to build the stage environment
    [KernelFunction("BuildStageEnvironment")]
    public string BuildStageEnvironment() 
    {
        return "Stage build completed.";
    }
    ```

    Le décorateur `KernelFunction` déclare votre fonction native. Vous utilisez un nom descriptif pour la fonction afin que l’IA puisse l’appeler correctement. 

1. Dans la méthode **main**, accédez au commentaire **Importer les plu-ins dans le noyau** et ajoutez le code suivant pour utiliser la classe de plug-in que vous avez créée :

    **Python**
    ```python
    # Import plugins to the kernel
    kernel.add_plugin(DevopsPlugin(), plugin_name="DevopsPlugin")
    ```

    **C#**
    ```c#
    // Import plugins to the kernel
    kernel.ImportPluginFromType<DevopsPlugin>();
    ```

1. Sous le commentaire **Créer des paramètres d’exécution d’invite**, ajoutez le code suivant pour appeler automatiquement la fonction :

    **Python**
    ```python
    # Create prompt execution settings
    execution_settings = AzureChatPromptExecutionSettings()
    execution_settings.function_choice_behavior = FunctionChoiceBehavior.Auto()
    ```

    **C#**
    ```c#
    // Create prompt execution settings
    OpenAIPromptExecutionSettings openAIPromptExecutionSettings = new() 
    {
        FunctionChoiceBehavior = FunctionChoiceBehavior.Auto()
    };
    ```

    L’utilisation de ce paramètre permet au noyau d’appeler automatiquement des fonctions sans avoir à les spécifier dans l’invite.

1. Ajoutez le code suivant sous le commentaire **Créer un historique des conversations** :

    **Python**
    ```python
    # Create chat history
    chat_history = ChatHistory()
    ```

    **C#**
    ```c#
    // Create chat history
    var chatCompletionService = kernel.GetRequiredService<IChatCompletionService>();
    ChatHistory chatHistory = [];
    ```

1. Supprimer les marques de commentaire du bloc de code situé après le commentaire **Logique d’interaction utilisateur**

1. Utilisez la commande **Ctrl+S** pour enregistrer les modifications que vous avez apportées au fichier de code.

## Exécuter votre code assistant DevOps

1. Dans le volet en ligne de commande du Cloud Shell, saisissez la commande suivante pour vous connecter à Azure :

    ```
    az login
    ```

    **<font color="red">Vous devez vous connecter à Azure, même si la session Cloud Shell est déjà authentifiée.</font>**

    > **Remarque** :dans la plupart des scénarios, l’utilisation d’*az login* suffit. Toutefois, si vous avez des abonnements dans plusieurs locataires, vous devrez peut-être spécifier le locataire à l’aide du paramètre *--tenant*. Pour plus d’informations, consultez [Se connecter à Azure de manière interactive à l’aide d’Azure CLI](https://learn.microsoft.com/cli/azure/authenticate-azure-cli-interactively).

1. Lorsque l’invite apparaît, suivez les instructions pour ouvrir la page de connexion dans un nouvel onglet et entrez le code d’authentification fourni ainsi que vos informations d’identification Azure. Effectuez ensuite le processus de connexion dans la ligne de commande, en sélectionnant l’abonnement contenant votre hub Azure AI Foundry si nécessaire.

1. Une fois connecté, entrez la commande suivante pour exécuter l’application :


    **Python**
    ```
    python devops.py
    ```

    **C#**
    ```
    dotnet run
    ```

1. Quand vous y êtes invité, entrez l’invite suivante : `Please build the stage environment`.

1. Vous devez voir une réponse similaire à la sortie suivante :

    ```output
    Assistant: The stage environment has been successfully built.
    ```

1. Entrez ensuite l’invite suivante : `Please deploy the stage environment`.

1. Vous devez voir une réponse similaire à la sortie suivante :

    ```output
    Assistant: The staging site has been deployed successfully.
    ```

1. Appuyez sur <kbd>Entrée</kbd> pour mettre fin au programme.

## Créer une fonction noyau à partir d’une invite

1. Sous le commentaire `Create a kernel function to deploy the staging environment`, ajoutez le code suivant :

     **Python**
    ```python
    # Create a kernel function to deploy the staging environment
    deploy_stage_function = KernelFunctionFromPrompt(
        prompt="""This is the most recent build log:
        {{DevopsPlugin.ReadLogFile}}

        If there are errors, do not deploy the stage environment. Otherwise, invoke the stage deployment function""",
        function_name="DeployStageEnvironment",
        description="Deploy the staging environment"
    )

    kernel.add_function(plugin_name="DeployStageEnvironment", function=deploy_stage_function)
    ```

    **C#**
    ```c#
    // Create a kernel function to deploy the staging environment
    var deployStageFunction = kernel.CreateFunctionFromPrompt(
    promptTemplate: @"This is the most recent build log:
    {{DevopsPlugin.ReadLogFile}}

    If there are errors, do not deploy the stage environment. Otherwise, invoke the stage deployment function",
    functionName: "DeployStageEnvironment",
    description: "Deploy the staging environment"
    );

    kernel.Plugins.AddFromFunctions("DeployStageEnvironment", [deployStageFunction]);
    ```

1. Utilisez la commande **Ctrl+S** pour enregistrer les modifications que vous avez apportées au fichier de code.

1. Dans le volet en ligne de commande du Cloud Shell, saisissez la commande suivante pour exécuter l’application :

    **Python**
    ```
    python devops.py
    ```

    **C#**
    ```
    dotnet run
    ```

1. Quand vous y êtes invité, entrez l’invite suivante : `Please deploy the stage environment`.

1. Vous devez voir une réponse similaire à la sortie suivante :

    ```output
    Assistant: The stage environment cannot be deployed because the earlier stage build failed due to unit test errors. Deploying a faulty build to stage may cause eventual issues and compromise the environment.
    ```

    Votre réponse du LLM peut varier, mais elle empêche toujours de déployer le site intermédiaire. 
    
1. Appuyez sur <kbd>Entrée</kbd> pour mettre fin au programme.

## Créer une invite Handlebars

1. Ajoutez le code suivant sous le commentaire **Créer une invite Handlebars** :

    **Python**
    ```python
    # Create a handlebars prompt
    hb_prompt = """<message role="system">Instructions: Before creating a new branch for a user, request the new branch name and base branch name/message>
        <message role="user">Can you create a new branch?</message>
        <message role="assistant">Sure, what would you like to name your branch? And which base branch would you like to use?</message>
        <message role="user">{{input}}</message>
        <message role="assistant">"""
    ```

    **C#**
    ```c#
    // Create a handlebars prompt
    string hbprompt = """
        <message role="system">Instructions: Before creating a new branch for a user, request the new branch name and base branch name/message>
        <message role="user">Can you create a new branch?</message>
        <message role="assistant">Sure, what would you like to name your branch? And which base branch would you like to use?</message>
        <message role="user">{{input}}</message>
        <message role="assistant">
        """;
    ```

    Dans ce code, vous créez une invite avec peu d’exemples à l’aide du format de modèle Handlebars. L’invite guide le modèle pour récupérer plus d’informations de l’utilisateur avant de créer une branche.

1. Ajoutez le code suivant sous le commentaire **Créer la configuration du modèle d’invite à l’aide du format Handlebars** :

    **Python**
    ```python
    # Create the prompt template config using handlebars format
    hb_template = HandlebarsPromptTemplate(
        prompt_template_config=PromptTemplateConfig(
            template=hb_prompt, 
            template_format="handlebars",
            name="CreateBranch", 
            description="Creates a new branch for the user",
            input_variables=[
                InputVariable(name="input", description="The user input", is_required=True)
            ]
        ),
        allow_dangerously_set_content=True,
    )
    ```

    **C#**
    ```c#
    // Create the prompt template config using handlebars format
    var templateFactory = new HandlebarsPromptTemplateFactory();
    var promptTemplateConfig = new PromptTemplateConfig()
    {
        Template = hbprompt,
        TemplateFormat = "handlebars",
        Name = "CreateBranch",
    };
    ```

    Ce code crée une configuration de modèle Handlebars à partir de l’invite. Vous pouvez l’utiliser pour créer une fonction de plug-in.

1. Ajoutez le code suivant sous le commentaire **Créer une fonction de plug-in à partir de l’invite** : 

    **Python**
    ```python
    # Create a plugin function from the prompt
    prompt_function = KernelFunctionFromPrompt(
        function_name="CreateBranch",
        description="Creates a branch for the user",
        template_format="handlebars",
        prompt_template=hb_template,
    )
    kernel.add_function(plugin_name="BranchPlugin", function=prompt_function)
    ```

    **C#**
    ```c#
    // Create a plugin function from the prompt
    var promptFunction = kernel.CreateFunctionFromPrompt(promptTemplateConfig, templateFactory);
    var branchPlugin = kernel.CreatePluginFromFunctions("BranchPlugin", [promptFunction]);
    kernel.Plugins.Add(branchPlugin);
    ```

    Ce code crée une fonction de plug-in pour l’invite et l’ajoute au noyau. Vous êtes maintenant prêt à appeler votre fonction.

1. Utilisez la commande **Ctrl+S** pour enregistrer les modifications que vous avez apportées au fichier de code.

1. Dans le volet en ligne de commande du Cloud Shell, saisissez la commande suivante pour exécuter l’application :

    **Python**
    ```
    python devops.py
    ```

    **C#**
    ```
    dotnet run
    ```

1. Quand vous y êtes invité, entrez le texte suivant : `Please create a new branch`.

1. Vous devez voir une réponse similaire à la sortie suivante :

    ```output
    Assistant: Could you please provide the following details?

    1. The name of the new branch.
    2. The base branch from which the new branch should be created.
    ```

1. Saisissez le texte suivant : `feature-login main`.

1. Vous devez voir une réponse similaire à la sortie suivante :

    ```output
    Assistant: The new branch `feature-login` has been successfully created from `main`.
    ```

1. Appuyez sur <kbd>Entrée</kbd> pour mettre fin au programme.

## Exiger le consentement de l’utilisateur pour les actions

1. En bas du fichier, recherchez le commentaire **Créer un filtre de fonction** et ajoutez le code suivant :

    **Python**
    ```python
    # Create a function filter
    async def permission_filter(context: FunctionInvocationContext, next: Callable[[FunctionInvocationContext], Awaitable[None]]) -> None:
        await next(context)
        result = context.result
        
        # Check the plugin and function names
    ```

    **C#**
    ```c#
    // Create a function filter
    class PermissionFilter : IFunctionInvocationFilter
    {
        public async Task OnFunctionInvocationAsync(FunctionInvocationContext context, Func<FunctionInvocationContext, Task> next)
        {
            // Check the plugin and function names
            
            await next(context);
        }
    }
    ```

1. Ajoutez le code suivant sous le commentaire **Vérifier le plug-in et les noms de fonction** pour détecter quand la fonction `DeployToProd` est appelée :

     **Python**
    ```python
    # Check the plugin and function names
    if context.function.plugin_name == "DevopsPlugin" and context.function.name == "DeployToProd":
        # Request user approval
        
        # Proceed if approved
    ```

    **C#**
    ```c#
    // Check the plugin and function names
    if ((context.Function.PluginName == "DevopsPlugin" && context.Function.Name == "DeployToProd"))
    {
        // Request user approval

        // Proceed if approved
    }
    ```

    Ce code utilise l’objet `FunctionInvocationContext` pour déterminer le plug-in et la fonction qui ont été appelés.

1. Ajoutez la logique suivante pour demander à l’utilisateur l’autorisation de poursuivre l’opération :

    Veillez à conserver le bon niveau de retrait.

    **Python**
    ```python
    # Request user approval
    print("System Message: The assistant requires approval to complete this operation. Do you approve (Y/N)")
    should_proceed = input("User: ").strip()

    # Proceed if approved
    if should_proceed.upper() != "Y":
        context.result = FunctionResult(
            function=result.function,
            value="The operation was not approved by the user",
        )
    ```

    **C#**
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

1. Accédez au commentaire **Ajouter des filtres au noyau** et ajoutez le code suivant :

    **Python**
    ```python
    # Add filters to the kernel
    kernel.add_filter('function_invocation', permission_filter)
    ```

    **C#**
    ```c#
    // Add filters to the kernel
    kernel.FunctionInvocationFilters.Add(new PermissionFilter());
    ```

1. Utilisez la commande **Ctrl+S** pour enregistrer les modifications que vous avez apportées au fichier de code.

1. Dans le volet en ligne de commande du Cloud Shell, saisissez la commande suivante pour exécuter l’application :

    **Python**
    ```
    python devops.py
    ```

    **C#**
    ```
    dotnet run
    ```

1. Entrez une invite pour déployer la build en production. Vous devriez voir une réponse similaire à ce qui suit :

    ```output
    User: Please deploy the build to prod
    System Message: The assistant requires an approval to complete this operation. Do you approve (Y/N)
    User: N
    Assistant: I'm sorry, but I am unable to proceed with the deployment.
    ```

1. Appuyez sur <kbd>Entrée</kbd> pour mettre fin au programme.

### Révision

Dans ce labo, vous avez créé un point de terminaison pour le service de grand modèle de langage (LLM), créé un objet Semantic Kernel et exécuté des invites à l’aide du SDK de Semantic Kernel. Vous avez également créé des plug-ins et utilisé des messages système pour guider le modèle. Félicitations, vous avez terminé ce labo !