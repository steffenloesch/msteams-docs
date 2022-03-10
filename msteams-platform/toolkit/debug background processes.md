---
title: Debug background processes
author: zyxiaoyuer
description: Function of Visual studio code and Teams Toolkit during local debug
ms.author: surbhigupta
ms.localizationpriority: high
ms.topic: overview
ms.date: 03/03/2022
---

# Debug background process

The background process of debugging involves the `.vscode/launch.json` and `.vscode/tasks.json` files, which controls the entire local debug workflow.  

* The `launch.json` file configures the debugger in Visual Studio Code. Visual Studio Code runs the compound configurations preLaunchTask, Pre Debug Check, and Start All, which is defined in `.vscode/tasks.json` file.
* Visual Studio Code launches the debuggers specified in the compound configurations, such as Attach to bot, Attach to backend, Attach to Frontend, and launch bot.
* Microsoft Edge or Chrome debugger launches a new browser instance and opens a web page to load Teams client.

## Prerequisites

The following table lists the limitations if the Node.js software is unavailable for debugging:

|Software | Installation | Limitation |
| --- | --- | --- |
|Node.js | Install Node.js [Node.js](https://nodejs.org/) | The local debug terminates, if you haven't installed Node.js or the version doesn't match the requirement.|

The following table lists the limitations if the software is unavailable for Teams app capability:

| Teams app capability | Node.js LTS versions| Limitation |
| --- | --- | --- |
| Tab without Azure functions | 10, 12, **14 (recommended)**, 16 | The local debug terminates, if you have not installed Node.js or the version doesn't match the requirement.|
| Tab with Azure functions | 10, 12, **14 (recommended)** |The local debug terminates, if you have not installed Node.js or the version doesn't match the requirement.|
| Bot | 10, 12, **14 (recommended)**, 16 |The local debug terminates, if you have not installed Node.js or the version doesn't match the requirement.|
| Messaging extension | 10, 12, **14 (recommended)**, 16 |The local debug terminates, if you have not installed Node.js or the version doesn't match the requirement.|
|Sign in to Microsoft 365 account | Teams toolkit prompts to sign in to Microsoft 365 account, if you have not signed in |Microsoft 365 credentials. |
|Bot, messaging extension | Install Ngrok. The Ngrok binary requires version 2.3.| <br> - If you have not installed Ngrok or the version doesn't match the requirement, the toolkit installs Ngrok NPM package `ngrok@4.2.2` in `~/.fx/bin/ngrok`. </br> <br>- The Ngrok binary is managed by Ngrok NPM package in `/.fx/bin/ngrok/node modules/ngrok/bin`.|</br>
|Azure functions | Install Azure Functions Core Tools. The Azure Functions Core Tools requires binary version 3.|<br> - If you have not installed Azure Functions Core Tools or the version doesn't match the requirement, the toolkit installs Azure Functions Core Tools NPM package, azure-functions-core-tools@3 for **Windows** and for **macOs** in  `~/.fx/bin/func`. </br> <br>- The  Azure Functions Core Tools NPM package in  `~/.fx/bin/func/node_modules/azure-functions-core-tools/bin` manages Azure Functions Core Tools binary. For Linux, the local debug terminates.|</br>
|Azure functions | Install Azure functions binding extensions defined in `api/extensions.csproj`|Limitation to be added|
|NPM packages| Install NPM packages for tab app, bot app, messaging extension app, and Azure functions|Limitation to be added|
|Azure functions | Install .NET Core SDK|<br> - If .NET Core SDK is not installed or the version  doesn't match the requirement, the toolkit installs .NET Core SDK for Windows and macOS in `~/.fx/bin/dotnet`.</br> <br> - For Linux, the local debug terminates.|</br>

Use the following .NET Core versions:

| Platform  | .NET core SDK versions |- |
| --- | --- | --- |
|  Windows, macOs (x64), Linux |  **3.1 (recommended)**, 5.0, 6.0 |-|
|  macOs (arm64) |  6.0 |-|


> [!NOTE]
> *If you don't have a development certificate for localhost installed in **Windows or macOS**, the tab prompts you to install it.
> *Start Ngrok to create a HTTP tunnel for bot and messaging extension.


The following table shows the ports available for components:

| Component  | Port |
| --- | --- |
| Tab | 53000 |
| Bot or messaging extension | 3978 |
| Node inspector for bot or messaging extension | 9239 |
| Azure functions | 7071 |
| Node inspector for Azure functions | 9229 |

> [!NOTE]
> If tab, bot, messaging extension, and Azure functions ports are unavailable, the local debug terminates.

The Teams Toolkit output channel displays the progress and result after checking the prerequisites.

:::image type="content" source="../assets/images/teams-toolkit-v2/pre-toolkit.png" alt-text="prerequisites":::

### Register and configure Azure AD application

1. Register an Azure AD application.

1. Create a Client Secret.

1. Expose an API.

    a. Configure Application ID URI. For tab, set `api://localhost/{appId}`. For bot or messaging extension, set `api://botid-{botid}`.

    b. Add a scope named `access_as_user`. Enable it for **Admin and users**.

The following table shows the configuration of Microsoft 365 client application with their client Ids:

| Microsoft 365 client application |  Client ID  |
| --- | --- |
| Teams desktop, mobile | 1fec8e78-bce4-4aaf-ab1b-5451cc387264 |
| Teams web | 5e3ce6c0-2b1f-4285-8d4b-75ee78787346 |
| Office.com | 4345a7b9-9a63-4910-a426-35363201d503 |
| Office.com | 4765445b-32c6-49b0-83e6-1d93765276ca |
| Office desktop | 0ec893e0-5785-4de6-99da-4ed124e5296c |
| Outlook desktop | d3590ed6-52b3-4102-aeff-aad2292ab01c |
| Outlook Web Access | 00000002-0000-0ff1-ce00-000000000000 |
| Outlook Web Access | bc59ab01-8403-45c6-8796-ac3ef710b3e3 |

4. Configure API permissions. Add Microsoft Graph permission to **User.Read**.

Configure the authentication as follows:

| Project type | Redirect URIs for web | Redirect URIs for single-page application |
| --- | --- | --- |
| Tab | [Local](https://localhost:53000/auth-end.html) | [Localhost](https://localhost:53000/auth-end.html?clientId={appId>}) |
| Bot or messaging extension | [Ngrok](https://ngrok.io/auth-end.html) | NA |

## Register and configure bot

For tab app or messaging extension app:

1. Register an Azure AD application.

1. Create a Client Secret for the Azure AD application.

1. Register a bot in [Microsoft Bot Framework](https://dev.botframework.com/) using the Azure AD application.

1. Add Microsoft Teams channel.

1. Configure messaging endpoint as [Messages](https://ngrok.io/api/messages).

## Register and configure Teams app

Register a Teams app in Teams Developer Portal [Developer](https://dev.teams.microsoft.com/home) using the manifest template in `templates/appPackage/manifest.local.template.json`.

## Start app services

To start app services, run tasks defined in `.vscode/tasks.json` to start app services as follows:

|  Component |  Task name  | Folder |
| --- | --- | --- |
|  Tab |  Start Frontend |  tabs |
|  Bot or messaging extensions |  Start Bot |  bot |
|  Azure functions |  Start backend |  api |

## Launch debuggers

Launch the debug configurations defined in `.vscode/launch.json` as follows:

:::image type="content" source="../assets/images/teams-toolkit-v2/Terminal.png" alt-text="Start frontend task":::

The table shows debug configuration type for project with tab app and bot app:

|  Component |  Debug configuration name  | Debug configuration type |
| --- | --- | --- |
|  Tab |  Attach to Frontend (Edge) or Attach to Frontend (Chrome)  |  pwa-msedge or pwa-mschrome  |
|  Bot or messaging extensions |  Attach to Bot |  pwa-node |
|  Azure functions |  Attach to backend |  pwa-node |

The table shows debug configuration type for project with bot app and without tab app:

|  Component |  Debug configuration name  | Debug configuration type  |
| --- | --- | --- |
|  Bot or messaging extension  |  Launch Bot (Edge) or Launch Bot (Chrome)  |   pwa-msedge or pwa-mschrome  |
|  Bot or messaging extension  |  Attach to Bot |  pwa-node  |
|  Azure functions |  Attach to backend |  pwa-node |


## Sideload Teams app

 The configuration attach to Frontend or Launch Bot launches a new Edge or Chrome browser instance and opens a web page to load Teams client. After the Teams client is completely loaded, Teams sideloads the Teams app controlled by the sideloading url defined in the launch configurations,
[Microsoft Teams](https://teams.microsoft.com/l/app/>${localTeamsAppId}?installAppPackage=true&webjoin=true&${account-hint}).

## Local debug files

The following lists the local debug files:

-`.fx/configs/localSettings.json:` local debug configuration file. The values of each configuration generates and saves during local debug.

-`templates/appPackage/manifest.local.template.json:` Teams app manifest template file for local debug. The placeholders in the file resolves during local debug.

-`tabs/.env.teams.local:` environment variables file for tab. The values of each environment variable generates and saves during local debug.

-`bot/.env.teamsfx.local:` environment variables file for bot and messaging extension. The values of each environment variable generates and saves during local debug.

-`api/.env.teamsfx.local:` environment variables file for Azure functions. The values of each environment variable generates and saves during local debug.

## Customize Teams Toolkit local debug

### Clear prerequisites

You can clear some of the prerequisites in the VS Code settings.

1. Select **Settings**.

1. Navigate to **Extensions** in the list and expand it, then select **Teams Toolkit (Preview)** and **Prerequisite Check**.

1. Uncheck the checkbox you want to skip.

The following image shows the list that can be unchecked:

:::image type="content" source="../assets/images/teams-toolkit-v2/prerequisite check.png" alt-text="vsc setting":::

### Use your own bot endpoint

1. In Visual Studio Code settings, uncheck **Ensure Ngrok is installed and started (ngrok)**.

1. Set botDomain and botEndpoint configuration in `.fx/configs/localSettings.json` to your own domain and endpoint.

### Use your own development certificate

1. In Visual Studio Code settings, uncheck **Ensure development certificate is trusted (devCert)**.

1. Set sslCertFile and sslKeyFile configuration in `.fx/configs/localSettings.json` to your own certificate file path and key file path.

### Use your own start scripts to start app services

1. For tab, update `dev:teamsfx script in tabs/package.json`.

1. For bot or messaging extension, update`dev:teamsfx script in bot/package.json`.

1. For Azure functions, update `dev:teamsfx script in api/package.json` and for TypeScript update `watch:teamsfx script`.


 > [!NOTE]
 > Currently, the tab, bot, messaging extension apps, and Azure functions doesn't support customization.

### Add environment variables

1. You can add environment variables to `.env.teamsfx.local` file for tab, bot, messaging extension and Azure functions. Teams Toolkit loads the environment variables you added to start services during local debug.

 > [!NOTE]
 > Ensure to start a new local debug after adding new environment variables as the environment variables doesn't support to hot reload.

### Debug partial component

Teams Toolkit utilizes Visual Studio Code multi-target debugging to debug tab, bot, messaging extension and Azure functions at the same time. You can update `.vscode/launch.json`` and .vscode/tasks.json` to debug partial component. If you want to debug tab only in a tab plus bot with Azure functions project, use the following steps:

1. Comment **Attach to Bot** and **Attach to Backend** from Debug compound in `.vscode/launch.json`

 ```json
{
            "name": "Debug (Edge)",
            "configurations": [
                "Attach to Frontend (Edge)",
                // "Attach to Bot",
                // "Attach to Backend"
            ],
            "preLaunchTask": "Pre Debug Check and Start All",
            "presentation": {
                "group": "all",
                "order": 1
            },
            "stopAll": true
        
}
```

1. Comment **Start Backend** and Start Bot from Start All task in .vscode/tasks.json

```json
{
                    
            "label": "Start All",
            "dependsOn": [
                "Start Frontend",
                // "Start Backend",
                // "Start Bot"
            ]
        

}
```

## See also

* [Debug overview](debug.md).