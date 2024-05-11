# roslyn.nvim

This plugin adds support for the new Roslyn-based C# language server [introduced](https://devblogs.microsoft.com/visualstudio/announcing-csharp-dev-kit-for-visual-studio-code) in the [vscode C# extension](https://github.com/dotnet/vscode-csharp).

## Dependencies

Ideally I would like to depend on the Dotnet SDK and everything else to be optional. But for now:

- Dotnet SDK (Tested with .net7).
- [`nvim-lspconfig`](https://github.com/neovim/nvim-lspconfig) for some path utility functions.
- Neovim nightly required. Tested on `831d662ac6756cab4fed6a9b394e68933b5fe325` but anything after August 2023 would probably work.

## Setup

### Installing Roslyn
1. Navigate to https://dev.azure.com/azure-public/vside/_artifacts/feed/vs-impl to see the latest package feed for `Microsoft.CodeAnalysis.LanguageServer`
2. Locate the version matching your OS + Arch and click to open it, for example `Microsoft.CodeAnalysis.LanguageServer.linux-x64` (Note that some OS/Arch specific packages may have an extra version ahead of the "core" non specific package)
3. On the package page, click the "Download" button to begin downloading it's .nupkg
   a. (Note, if you need to get a copyable link for the download you can obtain it on chrome by then opening the downloads page, right clicking the file just downloaded, and hitting "copy link address"
4. `.nupkg` files can be opened the same as a zip, in the case of linux you can just use `unzip` on the downloaded the file as if it was a `.zip`.
5. Copy the contents of `<zip root>/content/LanguageServer/<yourArch/` to `~/.local/share/nvim/roslyn`
   a. if you did it right the file `~/.local/share/nvim/roslyn/Microsoft.CodeAnalysis.LanguageServer.dll` should exist (along with many other .dlls and etc in that dir)
6. To test it is working, `cd` into the aformentioned roslyn directory and invoke `dotnet Microsoft.CodeAnalysis.LanguageServer.dll --version`, it should output its version

### Installing the nvim plugin

Install `tomiscout/roslyn.nvim` using your plugin manager.

```lua
require("roslyn").setup({
    dotnet_cmd = "dotnet", -- this is the default
    roslyn_version = "4.8.0-3.23475.7", -- this is the default
    on_attach = <on_attach you would pass to nvim-lspconfig>, -- required
    capabilities = <capabilities you would pass to nvim-lspconfig>, -- required
})
```

## Usage

1. The plugin will look for a `.sln` file in parent
   directories until it finds one. Make sure to have a `.sln` file somewhere in
   a parent dir. 
2. If it only finds one `.sln` file, it will use that to start the server.
   If it finds multiple, you have to run `CSTarget` to choose which target you want to use.
3. You'll see two notifications if everything goes well. The first one will say
   `Roslyn client initialized for target <target>`, which means the server is
   running, but it will just start indexing your `sln`. The second one will say
   `Roslyn project initialization complete`, it means that the server indexed
   your `sln`, only after you see the second notification will the `go to definition`
   and other lsp features be available.

## Importan note!

Roslyn requires that `.csproj` projects are referenced by the matched `.sln` it discovers, otherwise it won't actually discover and load suggestions for `.cs` files under the `.csproj`. Ensure you `dotnet sln add` your projects to the `.sln` or Roslyn won't load up properly!

## Features

Please note that some features from the vscode extension might not yet be supported by this plugin. Most of them are part of the roadmap, however I don't use vscode myself, so I'm not aware of all the features available, feel free to open an issue if you notice something is missing.
