# Lab 06: Add your data for RAG with Microsoft Foundry Agent

## Estimated Duration: 75 Minutes

## Lab Scenario

A travel company wants an assistant that answers customer questions from its own brochures rather than from the open internet, and you are the developer building it. You first ask the deployed model about travel destinations with no grounding data so you have a baseline to compare against, then create a Foundry agent, turn off web search, and upload the company's PDF brochures so Foundry chunks and stores them in a new vector index. Chatting with the grounded agent, you repeat the same questions and see answers backed by citations to the source files, then probe a destination that is missing from the index to observe how the agent distinguishes grounded answers from general model knowledge. Finally, you set up the sample app in Cloud Shell, configure it with your project endpoint, agent name, and agent version, and run it to query the same grounded agent programmatically.

## Lab Overview

In this lab, you will learn how to connect your own data to the Microsoft Foundry for Retrieval-Augmented Generation (RAG).

The Microsoft Foundry models enables you to use your own data with the intelligence of the underlying LLM. You can limit the model to only use your data for pertinent topics or blend it with results from the pre-trained model.

## Lab Scenario

A travel company wants an assistant that answers customer questions from its own brochures rather than from the open internet, and you are the developer building it. You first ask the deployed model about travel destinations with no grounding data so you have a baseline to compare against, then create a Foundry agent, turn off web search, and upload the company's PDF brochures so Foundry chunks and stores them in a new vector index. Chatting with the grounded agent, you repeat the same questions and see answers backed by citations to the source files, then probe a destination that is missing from the index to observe how the agent distinguishes grounded answers from general model knowledge. Finally, you set up the sample app in Cloud Shell, configure it with your project endpoint, agent name, and agent version, and run it to query the same grounded agent programmatically.

## Lab Objectives

In this lab, you will complete the following tasks:

- Task 1: Observe normal chat behavior without adding your own data
- Task 2: Create an Agent and connect your data
- Task 3: Chat with a model grounded in your data
- Task 4: Set up an application in Cloud Shell
- Task 5: Configure your application
- Task 6: Run your application

## Task 1: Observe normal chat behavior without adding your own data

In this task, you will observe how the base model responds to queries without any grounding data.

1. In the Microsoft Foundry portal, select the **Build (1)** tab in the top bar, then select **Deployments (2)** or **Models**, depending on your portal experience. Under **Deployed models (3)**, select your deployed model by clicking on the model name **(4)** `gpt-5-mini`.

    ![](../media/l3-model-nav.png)

1. You are taken to the model page under the **Deployments** or **Models** **(1)** tab in the left pane. Make sure the correct **model deployment (3)** is selected under **Playground (2)** tab. In the **Instructions (4)** box, you can provide a system message that tells the model how to behave in response to the prompts you send.

    ![](../media/l1-nf-model-page.png)

1. On the model page, make sure the **Playground** tab is selected. The playground lets you experiment with the model and test its capabilities. In the **Instructions** box, you can provide a system message that tells the model how to behave in response to the prompts you send.

      - Existing system message - `You are an AI assistant that helps people find informations`. 

1. In the **Chat** on the right side, submit the following queries, and review the **User query (1)** and **responses (2)**:

    ```
    I'd like to take a trip to New York. Where should I stay?
    ```

   ![](../media/l6-gpt-inst-1.png)

    ```
    What are some facts about New York?
    ```

   ![](../media/l6-gpt-inst-2.png)

    Try similar questions about tourism and places to stay for other locations that will be included in our grounding data, such as London or San Francisco. You'll likely get complete responses about areas or neighbourhoods, and some general facts about the city.

## Task 2: Create an Agent and connect your data

In this task, you will create an agent that will  responds to queries with grounding data.

1. From the **Build** tab, select **Agents (1)** to open the Agents page. On the **Agents (2)** tab, click **New agent (3)** dropdown and choose **Build an agent (4)** to create a new agent in Foundry.

    ![](../media/l6-gpt-inst-3-2.png)

1. In the **Create an agent** pop-up, enter `my-gpt-agent` **(1)** as the Agent name, then select **Create and open playground (2)** to create the agent. 

    ![](../media/l6-new-agent-create.png)

1. On the **Agents (1)** tab in the left pane, select the agent you created, `my-gpt-agent` **(2)**. On its **Playground (3)** tab, click the model dropdown **(4)** and confirm the correct model(`my-gpt-model` or `gpt-5-mini`) under Deployments is selected.

    ![](../media/l6-new-agent-2.png)

1. Since the agent should answer from your uploaded documents rather than the internet, disable web search. In the **Tools (1)** section, click the **Add (2)** dropdown and switch the **Web search (3)** toggle to **off**. This removes the tool from the agent's tool list.

    > Alternatively, locate **Web search** in the tools list, click the ellipsis (**⋮**) next to it, and select **Delete**.

    > **Note:** **Web search** uses Grounding with Bing, which has additional costs and terms. Customer data flows outside the Azure compliance boundary when this tool is enabled. 

    ![](../media/l6-new-agent-3.png)

1. Next, click **Upload files**, located beside the **Add** dropdown in the **Tools** section. This lets you add files from your local machine for the agent to reference.

    ![](../media/l6-agent-4-3.png)

1. In the **Attach files** pop-up, leave **Index option (1)** set to **Create a new index**. A **Vector index name (2)** is generated automatically — for example, `index_green_bear_drhrz2jl9r`. The name in your environment will differ from the one shown here; leave it as is.

1. Then click **browse for files (3)** to select documents from your local machine, or drag and drop them into the upload area.

    ![](../media/l6-agent-4.png)

    > **Note:** **The vector index** is what allows the agent to answer from your documents. When you attach files, Foundry splits them into chunks, converts each chunk into a numeric vector using a text embedding model, and stores those vectors in the index. At query time, your question is embedded the same way and the closest matching chunks are retrieved and passed to the model as grounding context — so answers come from your documents rather than the public web.
        >
        > **A text embedding model** deployment is created automatically as part of this process, so you don't need to deploy one separately.

1. Now Search for navigate to `C:\AllFiles\mslearn-openai-main\Labfiles\06-use-own-data\data` **(1)**. Select all the **PDF files (2)** and click on **Open (3)**.

    ![](../media/pdop.png)

1. Verify that the selected files **(1)** are listed, then click **Attach (2)** to upload them to the vector index.

    ![](../media/l6-agent-5.png)

1. Once the upload completes, a **File search** tool appears in the **Tools** section, listing your vector index along with its size and vector store ID — for example, `index_gray_vase_y9jwx8p1y6`. The index name in your environment will differ from the one shown here.

    ![](../media/l6-agent-6.png)

1. Once the tools are configured, click **Save (1)** in the top-right corner to save your changes. Note the **Version (2)** indicator beside it, which currently shows **Version 1**.

    ![](../media/l6-agent-7.png)

1. After saving, the version increments — for example, from **Version 1** to **Version 2** and the **Save** button becomes greyed out, confirming there are no unsaved changes.

    ![](../media/l6-agent-8.png)

1. Click the **Version (1)** dropdown to review your agent's version history. The most recent version is marked **Active (2)**, and earlier versions remain listed. From here you can also **Compare versions**, **Show all version history**, or **Delete current version**.

    ![](../media/l6-agent-9.png)

    > **Note:** Foundry creates a new version each time you save changes to the agent, so you can track configuration changes and roll back if needed. The timestamps in your environment will differ from those shown in the screenshots.


## Task 3: Chat with a model grounded in your data

In this task, you will ask the same questions as before in the chat section after adding your data, and observe how the responses differ.

1. In the **Chat** panel on the right, test the agent by entering a prompt in the **Message the agent...** box and pressing **Enter**.

   ```
   I'd like to take a trip to New York. Where should I stay?
   ```

   ![](../media/l6-agent-11.png) 

   ```
   What are some facts about New York?
   ```

   ![](../media/l6-agent-12.png) 

1. Continue testing the agent with the other cities included in the grounding data — **Dubai**, **Las Vegas**, **London**, and **San Francisco**. Try prompts such as:

    - `What are some facts about Dubai?`
    - `I'm planning a trip to Las Vegas. Where should I stay?`
    - `What attractions are covered in the London brochure?`
    - `Recommend hotels in San Francisco.`

1. Each response should be grounded in the uploaded brochures and include citations to the matching source file.

1. Next, ask about a city that is **not** in the grounding data, for example:

    - `I'd like to visit Japan. Which hotels do you recommend in Tokyo?`
    - `What are the top attractions in Paris?`

1. The agent runs a file search against the vector index and reports that no matching file was found. It confirms that the index contains only the Margie's Travel company information and brochures for **Dubai, London, New York, Las Vegas, and San Francisco**. Notice that the agent still produces a general itinerary for the unavailable destination, but states clearly that this content comes from general model knowledge rather than your uploaded documents.

1. To see how the agent distinguishes its sources, follow up with:

    - `Which of your previous answers came from my uploaded files, and which did not?`

1. The agent responds with a breakdown showing which replies were grounded in the brochures (with citations) and which were generated from general knowledge without a supporting file.

    > **Note:** This feature is still in preview and may not always behave as expected. For a destination outside the grounding data, the agent can occasionally return an incorrect or irrelevant citation instead of reporting that no matching information was found. If that happens, re-run or rephrase the prompt.

## Task 4: Set up an application in Cloud Shell

In this task, you will use a short command-line application running in Cloud Shell on Azure to demonstrate integration with an Azure OpenAI model. Open a new browser tab to access Cloud Shell.

1. In the **Azure portal**, select the **[>_] (Cloud Shell)** button at the top of the page to the right of the search box. A Cloud Shell pane will open at the bottom of the portal.

      ![](../media/cshell.png)

2. Make sure the type of shell indicated on the top left of the Cloud Shell pane is **Switch to PowerShell**. If it's *Bash*, select **Switch to Bash** and choose **Confirm** from the pop-up box.

    ![](../media/new/e6.png)

4. In the cloud shell pane, enter the following commands to clone the GitHub repo containing the code files for this exercise.

     ```
     rm -r mslearn-openai -f
     git clone https://github.com/microsoftlearning/mslearn-openai mslearn-openai
     ```

5. After the repo has been cloned, navigate to the folder containing the chat application code files.
   
    ```bash
    cd mslearn-openai/Labfiles/02-use-own-data
    ```

    Applications for both C# and Python have been provided, as well as sample code we'll be using in this lab.

5. Open the built-in code editor, and you can observe the code files we'll be using in `sample-code`. Use the following command to open the lab files in the code editor.

    ```bash
   code .
    ```

## Task 5: Configure your application

In this task, you will complete key parts of the application to enable it to use your Azure OpenAI resource.

1. In the code editor, expand the language folder for your preferred language.

1. Open the configuration file for your language and update the code.

    - **C#**: `appsettings.json`
        ```
        {
        "Foundry": {
            "ProjectEndpoint": "https://YOUR-FOUNDRY-RESOURCE.services.ai.azure.com/api/projects/YOUR-PROJECT-NAME",
            "AgentName": "YOUR-AGENT-NAME",
            "AgentVersion": "YOUR-AGENT-VERSION"
            }
        }
        ```

    - **Python**: `.env`

        ```
        FOUNDRY_PROJECT_ENDPOINT=https://YOUR-FOUNDRY-RESOURCE.services.ai.azure.com/api/projects/YOUR-PROJECT-NAME
        FOUNDRY_AGENT_NAME=YOUR-AGENT-NAME
        FOUNDRY_AGENT_VERSION=YOUR-AGENT-VERSION
        ```

1. Update the configuration file for your chosen language with the following values:

    - **Project endpoint**: Paste the endpoint URL of your Foundry project (found on the **Overview** page of your project in the Microsoft Foundry portal on **Home** page).
    - **Agent name**: Enter the name of the agent you created earlier — `my-gpt-agent`.
    - **Agent version**: Enter the version number of the agent, shown in the **Version** dropdown at the top-right of the agent's **Playground** tab (for example, `2`).
    - Save your changes after updating these values.

        ![](../media/l6-code-c1.png)

        ![](../media/l6-code-p1.png)

1. If you're using **C#**, navigate to `CSharp.csproj`, delete the existing code, then replace it with the following code, and then press **Ctrl+S** to save the file.

    ```
    <Project Sdk="Microsoft.NET.Sdk">

      <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>net9.0</TargetFramework>
        <ImplicitUsings>enable</ImplicitUsings>
        <Nullable>enable</Nullable>
        <LangVersion>12</LangVersion>
      </PropertyGroup>

      <ItemGroup>
        <PackageReference Include="Azure.AI.Projects" Version="2.0.0" />
        <PackageReference Include="Azure.AI.Extensions.OpenAI" Version="2.0.0" />
        <PackageReference Include="Azure.Identity" Version="1.20.0" />
        <PackageReference Include="Microsoft.Extensions.Configuration" Version="8.0.0" />
        <PackageReference Include="Microsoft.Extensions.Configuration.Json" Version="8.0.0" />
      </ItemGroup>

      <ItemGroup>
        <None Update="appsettings.json">
          <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
        </None>
      </ItemGroup>

    </Project>
    ```    

     ![](../media/l6-code-c2.png)    

1. Navigate to the **CSharp** folder and install the necessary packages. These commands set up the environment for a local installation of the .NET SDK in Cloud Shell.

   For **C#:**

    ```
    cd CSharp
    ```
1. If you have perfomed this steps in Lab 05 then skip the **steps 6-8** and proceed with package installation **step - 9**.

    ```
    export DOTNET_ROOT=$HOME/.dotnet
    mkdir -p $DOTNET_ROOT
    ```

     >**Note:** Azure Cloud Shell often does not have admin privileges, so you need to install .NET in your home directory. So here you are creating a separate `.dotnet` directory under your home directory to isolate your configuration.

     - `DOTNET_ROOT` specifies where your .NET runtime and SDK are located (in your `$HOME/.dotnet directory`).
     - `mkdir -p $DOTNET_ROOT` This creates the directory where the .NET runtime and SDK will be installed.

1. Run the following command to install the required SDK version locally:     

    ```
    curl -fsSL https://dot.net/v1/dotnet-install.sh -o dotnet-install.sh
    chmod +x dotnet-install.sh
    ``` 

    ```
    ./dotnet-install.sh --channel 9.0 --install-dir $DOTNET_ROOT
    ```

    ```
    export PATH=$DOTNET_ROOT:$PATH
    ```

1. Enter the following command to restore any required workloads for your project, such as additional tools or libraries that are part of the .NET SDK.

    ```
    dotnet restore
    ```

1. If you prefer **Python**, navigate to the **Python** folder and install the necessary packages using the commands below:

    ```
    cd Python
    python -m venv labenv
    source labenv/bin/activate
    pip install "azure-ai-projects>=2.1.0" azure-identity python-dotenv
    ```

1. In the code editor, replace your entire file code.

    For **C#**: OwnData.cs

    ```csharp
        using Azure.AI.Projects;
        using Azure.AI.Extensions.OpenAI;
        using Azure.Identity;
        using Microsoft.Extensions.Configuration;
        using OpenAI.Responses;

        #pragma warning disable OPENAI001

        // Load configuration from appsettings.json
        IConfiguration config = new ConfigurationBuilder()
            .SetBasePath(AppContext.BaseDirectory)
            .AddJsonFile("appsettings.json", optional: false, reloadOnChange: false)
            .Build();

        // Read Microsoft Foundry configuration
        string projectEndpoint =
            config["Foundry:ProjectEndpoint"]
            ?? throw new InvalidOperationException(
                "Foundry:ProjectEndpoint is missing in appsettings.json");

        string agentName =
            config["Foundry:AgentName"]
                ?? throw new InvalidOperationException(
                "Foundry:AgentName is missing in appsettings.json");

        string agentVersion =
            config["Foundry:AgentVersion"]
            ?? throw new InvalidOperationException(
            "Foundry:AgentVersion is missing in appsettings.json");

        // Connect to Microsoft Foundry using your Azure identity
        AIProjectClient projectClient = new(
            endpoint: new Uri(projectEndpoint),
            tokenProvider: new DefaultAzureCredential()
        );

        // Reference the existing Foundry Agent
        AgentReference agentReference = new(
            name: agentName,
            version: agentVersion
        );

        // Create a Responses client configured for the agent
        ProjectResponsesClient responseClient =
            projectClient.ProjectOpenAIClient
                .GetProjectResponsesClientForAgent(agentReference);

        Console.WriteLine("Microsoft Foundry Agent connected.");
        Console.WriteLine($"Agent: {agentName}");
        Console.WriteLine($"Version: {agentVersion}");
        Console.WriteLine();
        Console.WriteLine("Enter your question.");
        Console.WriteLine("Type 'exit' to quit.");
        Console.WriteLine();

        while (true)
        {
            Console.Write("Question: ");

            string question = Console.ReadLine() ?? "";

            if (string.Equals(question, "exit", StringComparison.OrdinalIgnoreCase))
            {
                break;
            }

            if (string.IsNullOrWhiteSpace(question))
            {
                Console.WriteLine("Please enter a question.");
                Console.WriteLine();
                continue;
            }

        try
        {
            Console.WriteLine("\nSearching the agent's configured tools...");

            // Send the question to the existing Foundry Agent.
            // The agent's File Search/vector store configuration
            // is used by the agent automatically.
            ResponseResult response =
                responseClient.CreateResponse(question);

            Console.WriteLine("\nAgent response:");
            Console.WriteLine(response.GetOutputText());
            Console.WriteLine();
        }
        catch (Exception ex)
        {
            Console.WriteLine("\nError calling Microsoft Foundry Agent:");
            Console.WriteLine(ex.Message);
            Console.WriteLine();
        }
    }
    ```

    For **Python**: ownData.py

    ```python
    import os
    from dotenv import load_dotenv
    from azure.identity import DefaultAzureCredential
    from azure.ai.projects import AIProjectClient

    # Load values from .env
    load_dotenv()

    # Read Microsoft Foundry configuration
    endpoint = os.getenv("FOUNDRY_PROJECT_ENDPOINT")
    agent_name = os.getenv("FOUNDRY_AGENT_NAME")
    agent_version = os.getenv("FOUNDRY_AGENT_VERSION")

    # Validate configuration
    if not endpoint:
        raise ValueError("FOUNDRY_PROJECT_ENDPOINT is missing from .env")

    if not agent_name:
        raise ValueError("FOUNDRY_AGENT_NAME is missing from .env")

    if not agent_version:
        raise ValueError("FOUNDRY_AGENT_VERSION is missing from .env")

    # Connect to Microsoft Foundry
    project_client = AIProjectClient(
        endpoint=endpoint,
        credential=DefaultAzureCredential(),
    )

    # Get the OpenAI client for the Foundry project
    openai_client = project_client.get_openai_client()

    print("Microsoft Foundry Agent connected.")
    print(f"Agent: {agent_name}")
    print(f"Version: {agent_version}")
    print()
    print("Type 'exit' to quit.")
    print()

    # Continuously accept questions
    while True:
        question = input("Question: ")

        if question.lower() == "exit":
            break

        if not question.strip():
            print("Please enter a question.")
            continue

        try:
            # Send the question to the existing Foundry Agent
            response = openai_client.responses.create(
                input=[
                    {
                        "role": "user",
                        "content": question
                    }
                ],
                extra_body={
                    "agent_reference": {
                        "name": agent_name,
                        "version": agent_version,
                        "type": "agent_reference"
                    }
                },
            )

            print("\nAgent response:")
            print(response.output_text)
            print()

        except Exception as e:
            print("\nError calling Microsoft Foundry Agent:")
            print(e)
            print()
    ```


1. Save the changes to the code file.

## Task 6: Run your application

In this task, you will run your configured app to send a request to your model and observe the response, noting that the only difference between options is the prompt content while all other parameters (such as token count and temperature) remain consistent.

In this task, you will run the reviewed code to generate some images.

1. In the **Cloudshell** bash terminal, navigate to the folder for your preferred language.

2. In the interactive terminal pane, ensure the folder context is the folder for your preferred language. Then enter the following command to run the application.

    - **C#**: `dotnet run`
    - **Python**: `python ownData.py`

        >**Note**: If you encounter any errors after running the Python script, try upgrading the OpenAI package by running the following command:
        
        ```
        python -m pip install --upgrade "azure-ai-projects>=2.1.0" azure-identity python-dotenv
        ```

3. Review the response to the prompt `Tell me about London`, which should include an answer as well as some details of the data used to ground the prompt, which was obtained from your search service.

    ![](../media/optown.png)

4. Once you have finished sending queries to the agent and reviewing its responses, stop the running application in the Bash terminal.

    - Press `Ctrl+C` to stop the running **python** application, then deactivate the Python virtual environment in the Bash terminal:

        ``` 
        deactivate
        ```
    - Press `Ctrl+C` to stop the running C# application and end the interaction with the model.

## Summary

In this lab, you began by evaluating how an Azure OpenAI model responds without grounding data to understand its baseline behavior. You then created an assistant with file search capabilities and connected it to a vector store containing your custom data, enabling grounded responses. By comparing outputs before and after grounding, you observed how Retrieval-Augmented Generation (RAG) improves the relevance and accuracy of responses. You also set up a development environment in Azure Cloud Shell, explored the provided application code, and configured it with your Azure OpenAI credentials and assistant details. Finally, you executed the application to interact programmatically with a grounded AI model, completing an end-to-end implementation of a RAG-based solution.

### You have successfully completed the lab. Click on **Next >>** to proceed with the next lab.
     
![](../media/7nct.png)
