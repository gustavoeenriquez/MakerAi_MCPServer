# MakerAi MCP Server for Delphi

[![GitHub license](https://img.shields.io/badge/license-MIT-blue.svg)](https://github.com/gustavoeenriquez/MakerAi_MCPServer/blob/main/LICENSE)
[![GitHub issues](https://img.shields.io/github/issues/gustavoeenriquez/MakerAi_MCPServer)](https://github.com/gustavoeenriquez/MakerAi_MCPServer/issues)
[![GitHub stars](https://img.shields.io/github/stars/gustavoeenriquez/MakerAi_MCPServer)](https://github.com/gustavoeenriquez/MakerAi_MCPServer/stargazers)

This project provides both a framework and a demo implementation for building **MCP (Model Context Protocol) Servers** using Delphi. It is designed to work as a standalone component or as part of the comprehensive **[MakerAi Delphi Suite](https://github.com/gustavoeenriquez/MakerAi)**.

An MCP server acts as a bridge, allowing client applications (like an LLM, e.g., Claude) to interact with local tools and resources (such as your file system, databases, or custom business logic written in Delphi) in a secure, standardized manner.

## What is the MCP (Model Context Protocol)?

The **MCP (Model Context Protocol)** is an open standard based on JSON-RPC that standardizes communication between a client (an AI, an IDE, etc.) and a tool server. In simple terms, it allows an AI to discover and execute functions that you expose from your Delphi application.

The basic communication flow is as follows:
1.  **Connection**: The client connects to the server (via StdIO or HTTP).
2.  **Discovery**: The client requests the list of available tools (`tools/list`).
3.  **Response**: The server returns a list of the functions it can execute (e.g., `readFile`, `get_datetime`).
4.  **Execution**: The client requests to execute a specific tool with certain parameters (`tools/call`).
5.  **Result**: The server executes the logic in Delphi and returns the result to the client.

## ✨ Key Features

*   **Framework for Custom Tools**: A modular and easy-to-extend structure for creating your own tools and exposing any functionality from your application.
*   **Demo Server Included**: Includes a demonstration project (`AiMCPServerDemo`) with pre-built tools for file access (`uTool.FileAccess`) and system information (`uTool.SysInfo`).
*   **Multiple Protocol Support**:
    *   **StdIO**: Ideal for integrating with local applications like Claude Desktop or VS Code extensions.
    *   **HTTP**: Allows exposing tools over the network.
*   **Cross-Platform Compatibility**: Designed to be compiled and run on Windows and Linux.
*   **Simple Integration**: Easily connect from any MCP-compatible client, such as **Claude Desktop** or the `TAiFunctions` component from the **MakerAi Delphi Suite**.

## 🚀 Getting Started (Compiling and Running the Demo)

1.  **Clone the Repository**:
    ```bash
    git clone https://github.com/gustavoeenriquez/MakerAi_MCPServer.git
    ```
2.  **Open the Project**: Open the `AiMCPServerDemo.dpr` project in your Delphi IDE.
3.  **Compile**: Compile the project to generate the executable.
4.  **Run from the Console**: The server can be started from the command line, specifying the protocol and port.

    **To start in HTTP mode on port 3000:**
    ```bash
    .\\AiMCPServerDemo.exe -p http --port 3000
    ```

    **To start in StdIO mode (used by clients like Claude Desktop):**
    ```bash
    .\\AiMCPServerDemo.exe -p stdio
    ```
    The server will start and wait for connections. You will see a message like "Registering tools and resources... Registration complete.".

## 🛠️ How to Create Your Own Tool

Extending the server with your own functions is the main purpose of this framework. Follow these three steps (using the `get_datetime` example from the documentation):

#### Step 1: Create the Tool's Unit
Create a new Pascal file (e.g., `uTool.MyTool.pas`) that will contain your tool's logic.

#### Step 2: Implement the Tool's Logic
Inside the unit, you need three core elements:
1.  **Parameters Class**: A class that defines the input parameters your function will receive.
2.  **Tool Class**: The main class that inherits from `TAiMCPToolBase`, where `T` is your parameters class. Here, you implement the `ExecuteWithParams` method, which contains your tool's actual logic.
3.  **Registration Procedure**: A simple procedure that registers the tool with the server instance.

```delphi
// Example of a registration procedure in uTool.MyTool.pas
procedure RegisterTools(ALogicServer: TAiMCPServer);
begin
  if not Assigned(ALogicServer) then
    Exit;

  ALogicServer.RegisterTool('my_custom_tool',
    function: IAiMCPTool
    begin
      Result := TMyTool.Create;
    end);
end;
Step 3: Register the New Tool in the Server
Finally, in the main AiMCPServerDemo.dpr file, add your new unit to the uses clause and call its registration procedure within RegisterAllToolsAndResources.
code
Delphi
// In AiMCPServerDemo.dpr
uses
  ...,
  uTool.MyTool in 'uTool.MyTool.pas'; // <-- Add your unit

...

procedure RegisterAllToolsAndResources(ALogicServer: TAiMCPServer);
begin
  ...
  uTool.FileAccess.RegisterTools(ALogicServer);
  uTool.SysInfo.RegisterTools(ALogicServer);
  uTool.MyTool.RegisterTools(ALogicServer); // <-- Add the registration call
  ...
end;
Recompile, and your new tool will be available to any connected MCP client!
🔌 Connecting with a Client
Claude Desktop
You can configure Claude Desktop to automatically launch and use your MCP server.
Go to Settings -> Developer and click Edit Config.
The claude_desktop_config.json file will open.
Add the configuration to point to your compiled executable.
code
JSON
{
  "mcpServers": {
    "delphi_server": {
      "command": "C:\\path\\to\\your\\project\\AiMCPServerDemo.exe",
      "args": [
        "-p",
        "stdio"
      ]
    }
  }
}
Save the file, restart Claude, and you should see a tools icon indicating a successful connection.
MakerAi Delphi Suite
The TAiFunctions component from the main suite can be configured to connect to any MCP server, including this one. Simply set up a new MCPClient with the appropriate TransportType (tpStdio or tpHttp) and the corresponding parameters.
📄 License
This project is licensed under the MIT License.
🤝 Contributions
Contributions are welcome! If you have ideas to improve this framework, feel free to open an issue or submit a pull request.
💬 Support
If you have any questions or need help, you can contact me through:
GitHub Issues
Email: gustavoeenriquez@gmail.com
Telegram Group
Acknowledgements
I thank the entire Delphi community for their constant support.