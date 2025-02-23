# Who Bot

This bot can tell you about your personal work-related information, who your manager is, and what your profile picture is. It uses a predefined prompt with the sequence augmentation and the action planner to map user's message to predefined actions. These actions then make graph calls to fulfil user's request.

Note that this bot will only work in tenants where the following graph scopes are permitted:

- `User.Read`
- `User.ReadBasic.All`
- `People.Read`
- `Mail.Read`
- `Calendar.Read.Shared`

*Table of contents*

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [Who Bot](#who-bot)
  - [Setting up the sample](#setting-up-the-sample)
  - [Interacting with the bot](#interacting-with-the-bot)
  - [Multiple ways to test](#multiple-ways-to-test)
    - [Using Teams Toolkit for Visual Studio Code](#using-teams-toolkit-for-visual-studio-code)
    - [Using Teams Toolkit CLI](#using-teams-toolkit-cli)
    - [Manually upload the app to a Teams desktop client](#manually-upload-the-app-to-a-teams-desktop-client)
  - [Deploy the bot to Azure](#deploy-the-bot-to-azure)
  - [Further reading](#further-reading)

<!-- /code_chunk_output -->

## Setting up the sample

1. Clone the repository

    ```bash
    git clone https://github.com/Microsoft/teams-ai.git
    ```

2. In the root JavaScript folder, install and build all dependencies

    ```bash
    cd teams-ai/js
    yarn install
    yarn build
    ```

3. In a terminal, navigate to the sample root.

    ```bash
    cd teams-ai/js/samples/07.whoBot/
    ```

4. Duplicate the `sample.env` in the `teams-ai/js/samples/07.whoBot` folder. Rename the file to `.env`. 

5. If you are using OpenAI then only keep the `OPENAI_KEY` and add in your key. Otherwise if you are using AzureOpenAI then only keep the `AZURE_OPENAI_KEY`, `AZURE_OPENAI_ENDPOINT` variables and fill them in appropriately.

6. Update `config.json` and `bot.ts` with your model deployment name.

## Interacting with the bot

You can interact with this bot by sending it a message and ask it about your personal work-related information.

## Multiple ways to test

The easiest and fastest way to get up and running is with Teams Toolkit as your development guide. To use Teams Toolkit to continue setup and debugging, please continue below.

Otherwise, if you only want to run the bot locally and build manually, please jump to the [BotFramework Emulator](#testing-in-BotFramework-emulator) section.

### Using Teams Toolkit for Visual Studio Code

The simplest way to run this sample in Teams is to use Teams Toolkit for Visual Studio Code.

1. Ensure you have downloaded and installed [Visual Studio Code](https://code.visualstudio.com/docs/setup/setup-overview)
1. Install the [Teams Toolkit extension](https://marketplace.visualstudio.com/items?itemName=TeamsDevApp.ms-teams-vscode-extension)
   1. Login with your M365 account.
   2. Login with your Azure account. Ensure that you have a valid subscription and resource group. This will be required to provision your bot.
2. Select **File > Open Folder** in VS Code and choose this sample's directory from the repo
3. Using the extension, sign in with your Microsoft 365 account where you have permissions to upload custom apps
4. Select **Debug > Start Debugging** or **F5** to run the app in a Teams web client.
5. In the browser that launches, select the **Add** button to install the app to Teams.

> If you do not have permission to upload custom apps (sideloading), Teams Toolkit will recommend creating and using a Microsoft 365 Developer Program account - a free program to get your own dev environment sandbox that includes Teams.

### Using Teams Toolkit CLI

You can also use the Teams Toolkit CLI to run this sample.

1. Install the CLI

    ```bash
    npm install -g @microsoft/teamsfx-cli
    ```

1. Open a second shell instance and run ngrok tunneling service - point to port 3978

    ```bash
    ngrok http --host-header=rewrite 3978
    ```

1. Copy the ngrok URL and put the URL and domain in the `/env/env.local` file

    ```bash
    BOT_ENDPOINT=https://{ngrok-url}.ngrok.io
    BOT_DOMAIN={ngrok-url}.ngrok.io
    ```

1. In the repository directory, run the Teams Toolkit CLI commands to automate the setup needed for the app

    ```bash
    cd teams-ai/js/samples/07.whoBot/
    teamsfx provision --env local

    ```

1. Next, use the CLI to validate and create an app package

    ```bash
    teamsfx deploy --env local
    ```

1. Finally, use the CLI to preview the app in Teams

    ```bash
    teamsfx preview --env local
    ```

### Manually upload the app to a Teams desktop client

> If you used Teams Toolkit in the above steps, you can [upload a custom app](https://learn.microsoft.com/en-us/microsoftteams/platform/concepts/deploy-and-publish/apps-upload) to a desktop client using the `/appPackage/appPackage.local.zip` file created by the tools and skip to step 6.

1. In a terminal, navigate to `teams-ai/js/samples/07.whoBot/`

    ```bash
    cd teams-ai/js/samples/07.whoBot/
    ```

1. Run ngrok tunneling service - point to port 3978

    ```bash
    ngrok http --host-header=rewrite 3978
    ```

1. Create [Bot Framework registration resource](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-quickstart-registration) in Azure

    - Use the current `https` URL you were given by running ngrok. Append with the path `/api/messages` used by this sample.
    - Ensure that you've [enabled the Teams Channel](https://docs.microsoft.com/en-us/azure/bot-service/channel-connect-teams?view=azure-bot-service-4.0)

1. Update the `.env` configuration for the bot to use the Microsoft App Id and App Password from the Bot Framework registration. (Note the App Password is referred to as the "client secret" in the Azure Portal and you can always create a new client secret anytime.)

1. **_This step is specific to Teams._**

    - **Edit** the `manifest.json` contained in the `appPackage` folder to replace your Microsoft App Id (that was created when you registered your bot earlier) _everywhere_ you see the place holder string `${{TEAMS_APP_ID}}` (depending on the scenario the Microsoft App Id may occur multiple times in the `manifest.json`). If you haven't created an Azure app service yet, you can use your bot id for the above. You're bot id should be pasted in where you see `${{BOT_ID}}`. Replace everywhere you see `${{BOT_DOMAIN}}` with the domain part of the URL created by your tunneling solution.
    - **Zip** up the contents of the `appPackage` folder to create a `manifest.zip`

1. Run your app from the command line:

    ```bash
    yarn start
    ```

1. [Upload the app](https://learn.microsoft.com/en-us/microsoftteams/platform/concepts/deploy-and-publish/apps-upload) file (manifest.zip created in the previous step) in Teams.


## Deploy the bot to Azure

You can use Teams Toolkit for VS Code or CLI to host the bot in Azure. The sample includes Bicep templates in the `/infra` directory which are used by the tools to create resources in Azure. Use the **Provision** and **Deploy** menus of the Teams Toolkit extension or from the CLI with `teamsfx provision` and `teamsfx deploy`. [Visit the documentation](https://learn.microsoft.com/en-us/microsoftteams/platform/toolkit/provision) for more info on hosting your app in Azure with Teams Toolkit.

Alternatively, you can learn more about deploying a bot to Azure manually in the [Deploy your bot to Azure](https://aka.ms/azuredeployment) documentation.

## Further reading

-   [Teams Toolkit overview](https://learn.microsoft.com/en-us/microsoftteams/platform/toolkit/teams-toolkit-fundamentals)
-   [How Microsoft Teams bots work](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-basics-teams?view=azure-bot-service-4.0&tabs=javascript)
