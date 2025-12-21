# AI-Assisted Installation Guide for Unreal MCP Server

This guide is optimized for AI assistants like Cline to help users set up the Unreal Engine MCP server automatically. Follow these steps in order.

## Prerequisites Validation

Before starting installation, verify these prerequisites are met:

### Required Software
```bash
# Check Unreal Engine installation (UE 5.5+)
# On macOS/Linux:
ls "/Users/Shared/Epic Games/UE_5.5" || ls "$HOME/UnrealEngine"
# On Windows:
# dir "C:\Program Files\Epic Games\UE_5.5"

# Check Python version (3.10+ required, 3.12+ recommended)
python3 --version

# Check if 'uv' is installed (Python package manager)
uv --version || echo "uv needs to be installed"

# Check for Visual Studio or appropriate C++ compiler
# On Windows: Check for MSBuild
# On macOS: Check for Xcode command line tools
xcode-select -p
```

### Install Missing Prerequisites

If `uv` is not installed:
```bash
# Install uv (cross-platform Python package manager)
curl -LsSf https://astral.sh/uv/install.sh | sh
# Or on Windows:
# powershell -c "irm https://astral.sh/uv/install.ps1 | iex"
```

## Installation Steps

### Step 1: Choose Your Installation Method

**Option A: Use the Sample Project (Recommended for Quick Start)**
- Ideal for testing and learning
- UE 5.5 Blank Project with UnrealMCP plugin pre-configured
- Located in `MCPGameProject/` directory

**Option B: Install Plugin in Existing Project**
- For integrating into your own UE project
- Requires copying plugin and rebuilding

### Step 2: Setup for Sample Project (Option A)

```bash
cd MCPGameProject

# On macOS/Linux: Generate project files
# Right-click the .uproject file and select "Generate Visual Studio project files"
# Or use command line if available for your UE installation

# On Windows: Generate Visual Studio project files
# Right-click MCPGameProject.uproject → "Generate Visual Studio project files"
```

After generating project files:
```bash
# Open the generated .sln file
# Build Configuration: Development Editor
# Platform: Win64 (Windows) or Mac (macOS) or Linux
# Build the solution

# This compiles both the project and the UnrealMCP plugin
```

### Step 3: Setup for Existing Project (Option B)

```bash
# Navigate to your Unreal project directory
cd /path/to/your/unreal/project

# Create Plugins directory if it doesn't exist
mkdir -p Plugins

# Copy the UnrealMCP plugin
cp -r /path/to/unreal-5-mcp/MCPGameProject/Plugins/UnrealMCP ./Plugins/

# Generate project files
# Right-click your .uproject file → "Generate Visual Studio project files"

# Open the .sln file and build with Development Editor configuration
```

After building, open your project in Unreal Editor:
```bash
# Enable the plugin
# Edit → Plugins → Search "UnrealMCP" → Enable → Restart Editor
```

### Step 4: Setup Python MCP Server

```bash
cd Python

# Install dependencies using uv
uv sync

# Verify installation
uv run python -c "import fastmcp; print('FastMCP installed successfully')"
```

### Step 5: Configure MCP Client

Determine your MCP client configuration file location:
- **Cline**: Typically uses workspace-specific or global settings
- **Claude Desktop**: `~/.config/claude-desktop/mcp.json` (or `%USERPROFILE%\.config\claude-desktop\mcp.json` on Windows)
- **Cursor**: `.cursor/mcp.json` in your project root
- **Windsurf**: `~/.config/windsurf/mcp.json` (or `%USERPROFILE%\.config\windsurf\mcp.json` on Windows)

Create or update the MCP configuration file:
```json
{
  "mcpServers": {
    "unrealMCP": {
      "command": "uv",
      "args": [
        "--directory",
        "/ABSOLUTE/PATH/TO/unreal-5-mcp/Python",
        "run",
        "unreal_mcp_server.py"
      ]
    }
  }
}
```

**Important**: Replace `/ABSOLUTE/PATH/TO/unreal-5-mcp/Python` with the actual absolute path to the Python directory.

Example paths:
- macOS/Linux: `/Users/username/Desktop/unreal-5-mcp/Python`
- Windows: `C:\\Users\\username\\Desktop\\unreal-5-mcp\\Python`

### Step 6: Start Unreal Editor

```bash
# Launch Unreal Editor with your project
# The UnrealMCP plugin should be loaded and running
# It listens on TCP port 55557 for MCP connections

# Verify the plugin is active:
# Check Window → Developer Tools → Output Log for UnrealMCP messages
```

### Step 7: Test the Connection

```bash
cd Python

# Run the MCP server manually to test
uv run unreal_mcp_server.py

# You should see connection messages if Unreal Editor is running
# The server will connect to localhost:55557 (the C++ plugin)
```

If the connection is successful, you should see:
- Server output: "Connected to Unreal Engine on port 55557"
- Unreal Editor output log: "MCP client connected"

### Step 8: Verify Setup with AI Client

Restart your MCP client (Cline, Claude Desktop, Cursor, or Windsurf) to load the new configuration.

Test with a simple command:
```
"List all actors in the current Unreal Engine level"
```

The AI should be able to use the MCP tools to query Unreal Engine.

## Troubleshooting

### Issue: "Cannot connect to Unreal Engine"
**Solution**:
- Ensure Unreal Editor is running with your project loaded
- Verify the UnrealMCP plugin is enabled (Edit → Plugins)
- Check that no firewall is blocking port 55557
- Look for error messages in the Unreal Editor Output Log

### Issue: "uv: command not found"
**Solution**:
```bash
# Install uv
curl -LsSf https://astral.sh/uv/install.sh | sh

# Add to PATH if needed (usually automatic)
export PATH="$HOME/.cargo/bin:$PATH"
```

### Issue: "Python version too old"
**Solution**:
```bash
# Install Python 3.12+
# On macOS:
brew install python@3.12

# On Windows: Download from python.org
# On Linux:
sudo apt install python3.12  # Ubuntu/Debian
```

### Issue: "Cannot build C++ plugin"
**Solution**:
- **Windows**: Ensure Visual Studio 2022 is installed with "Game Development with C++" workload
- **macOS**: Ensure Xcode command line tools are installed: `xcode-select --install`
- **Linux**: Ensure build-essential and clang are installed

### Issue: "MCP client not detecting server"
**Solution**:
- Verify the configuration file path is correct for your client
- Check that the JSON is valid (no syntax errors)
- Ensure the absolute path to Python directory is correct
- Restart the MCP client completely

## Success Validation

You've successfully installed the Unreal MCP server when:

1. ✅ Unreal Editor launches without plugin errors
2. ✅ The Output Log shows "UnrealMCP plugin initialized"
3. ✅ Python MCP server connects to port 55557
4. ✅ Your AI client shows "unrealMCP" as an available server
5. ✅ AI commands can query and manipulate Unreal Engine actors

## Next Steps

After successful installation:

1. **Explore the tools**: Ask your AI assistant what it can do with Unreal Engine
2. **Read the docs**: Check `Docs/README.md` for detailed feature documentation
3. **Try examples**: Look at `Python/scripts/` for example workflows
4. **Build something**: Start creating actors, blueprints, and scenes with AI assistance

## Additional Resources

- **Full README**: [README.md](README.md)
- **Python Setup Details**: [Python/README.md](Python/README.md)
- **Documentation**: [Docs/README.md](Docs/README.md)
- **Issues & Support**: [GitHub Issues](https://github.com/jl-codes/unreal-5-mcp/issues)

## Notes for AI Assistants

When helping users install this MCP server:

1. Always verify prerequisites before starting
2. Use absolute paths in configuration files
3. Check for platform-specific differences (Windows vs macOS vs Linux)
4. Provide clear error messages and solutions
5. Validate each step before moving to the next
6. Offer to check installation status at any point
