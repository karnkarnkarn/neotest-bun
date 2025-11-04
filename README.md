# neotest-bun

Neotest adapter for [Bun](https://bun.sh/) test runner.

Based on [neotest-jest](https://github.com/nvim-neotest/neotest-jest)

## Installation

Using [lazy.nvim](https://github.com/folke/lazy.nvim)

```lua
{
  'nvim-neotest/neotest',
  requires = {
    ...,
    'arthur944/neotest-bun',
  }
  config = function()
    require("neotest").setup({
      adapters = {
        require("neotest-bun"),
      },
    })
  end,
}
```

Make sure you have the appropriate `treesitter` language parsers installed otherwise no tests will be found:

```
:TSInstall javascript
```

## Usage

![Screen Recording May 5 2025 (1)](https://github.com/user-attachments/assets/ac11c56c-ebfa-4dc5-88d3-1f6f94175180)

See neotest's documentation for more information on how to run tests. Just make sure you have bun installed and in your path.

### Debugging tests with DAP (Neovim)

This adapter supports debugging with nvim-dap via neotest's `strategy = "dap"`.

Prerequisites:
- nvim-dap installed and configured
- A Node.js debug adapter. Recommended: `mfussenegger/nvim-dap` + `mxsdev/nvim-dap-vscode-js` (which uses VSCode `js-debug`).

Example dap setup (using `pwa-node` from `js-debug`):

```lua
-- using mxsdev/nvim-dap-vscode-js
require("dap-vscode-js").setup({
  node_path = "node", -- or `bun` installs Node-compatible runtime
  debugger_path = vim.fn.stdpath("data") .. "/lazy/vscode-js-debug",
  adapters = { "pwa-node" },
})

local dap = require("dap")
-- You generally don't need filetype-specific configs here for neotest.
-- neotest-bun will provide a launch config with runtimeExecutable = "bun".
```

How to launch a debug run:

- Run the nearest test in debug mode
  ```lua
  require("neotest").run.run({ strategy = "dap" })
  ```
- Or debug an entire file from the current buffer
  ```lua
  require("neotest").run.run(vim.fn.expand("%"), { strategy = "dap" })
  ```

What the adapter does:
- Converts your test run into a DAP `launch` request with `type = "pwa-node"`, `runtimeExecutable = "bun"`, and passes Bun's test CLI as `runtimeArgs` (including JUnit reporter so results still show in the neotest summary).
- When running a single test, it appends `--test-name-pattern <name>` so only that test is executed under the debugger.

Tips:
- Set breakpoints before starting the run. Use nvim-dap keymaps to step/continue/inspect.
- If you use a different Node debug adapter type, set it up in `nvim-dap` and it should still work since the adapter provides a standard `launch` config.
- If you need to customize the DAP config further, open an issue; adapter-level overrides can be added later.

## Configuration

There is currently nothing to configure. Test files will be discovered based on these patterns:

- `%.test%.ts$`
- `%.test%.tsx$`
- `%.spec%.ts$`
- `%.spec%.tsx$`
- `%.test%.js$`
- `%.test%.jsx$`
- `%.spec%.js$`
- `%.spec%.jsx$`

The root directory is determined by where a `package.json` file is found. If you have a bunfig.toml file there, it will be used when running tests.

### Running tests in watch mode

That's not suppored right now, PRs welcome.

## Bugs and feature requests

Feel free to raise an issue or reach out to me on [X the everything app](https://x.com/bella_artur)
