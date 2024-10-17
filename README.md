# Modicator.nvim 💡

_Cursor line number **mod**e ind**icator**._

A small Neovim plugin that changes the color of your cursor's line number based on the current Vim mode.

Modicator has [lualine.nvim](https://github.com/nvim-lualine/lualine.nvim) support [out of the box](#lualine-integration).

![Modicator in use](https://user-images.githubusercontent.com/15816726/215295831-299dc732-85ae-4668-9e7b-e88cd499f18a.gif)

## Setup

```lua
require('modicator').setup()
```

Note that modicator requires you to have `termguicolors`, `cursorline`, `number` set. In Lua this is done by adding the following somewhere in your Neovim configuration:

```lua
vim.o.termguicolors = true
vim.o.cursorline = true
vim.o.number = true
```

Modicator sets the Normal mode highlight foreground based on the default foreground color of `CursorLineNr` so if you're using a colorscheme make sure that it gets loaded before this plugin.

With [lazy.nvim](https://github.com/folke/lazy.nvim/):

```lua
{
  'mawkler/modicator.nvim',
  dependencies = 'mawkler/onedark.nvim', -- Add your colorscheme plugin here
  init = function()
    -- These are required for Modicator to work
    vim.o.cursorline = true
    vim.o.number = true
    vim.o.termguicolors = true
  end,
  opts = {
    -- Warn if any required option above is missing. May emit false positives
    -- if some other plugin modifies them, which in that case you can just
    -- ignore. Feel free to remove this line after you've gotten Modicator to
    -- work properly.
    show_warnings = true,
  }
}
```

Or with [packer.nvim](https://github.com/wbthomason/packer.nvim/):

```lua
use {
  'mawkler/modicator.nvim',
  after = 'onedark.nvim', -- Add your colorscheme plugin here
  setup = function()
    -- These are required for Modicator to work
    vim.o.cursorline = true
    vim.o.number = true
    vim.o.termguicolors = true
  end,
  config = function()
    require('modicator').setup({
    -- Warn if any required option above is missing. May emit false positives
    -- if some other plugin modifies them, which in that case you can just
    -- ignore. Feel free to remove this line after you've gotten Modicator to
    -- work properly.
    show_warnings = true,
  })
  end
}
```

## Configuration

Modicator uses the following highlight groups for each mode, respectively:

```txt
NormalMode
InsertMode
VisualMode
CommandMode
ReplaceMode
SelectMode
TerminalMode
TerminalNormalMode
```

For more information on how to create a highlight group, see `:help nvim_set_hl`.

### Default configuration:

```lua
require('modicator').setup({
  -- Warn if any required option is missing. May emit false positives if some
  -- other plugin modifies them, which in that case you can just ignore
  show_warnings = false,
  highlights = {
    -- Default options for bold/italic
    defaults = {
      bold = false,
      italic = false,
    },
    -- Use `CursorLine`'s background color for `CursorLineNr`'s background
    use_cursorline_background = false,
  },
  integration = {
    lualine = {
      enabled = true,
      -- Letter of lualine section to use (if `nil`, gets detected automatically)
      mode_section = nil,
      -- Whether to use lualine's mode highlight's foreground or background
      highlight = 'bg',
    },
  },
})
```

## Lualine integration

Modicator has built-in support [lualine.nvim](https://github.com/nvim-lualine/lualine.nvim), meaning that if it detects lualine.nvim in your setup it will use the same colors for each mode as lualine.nvim uses. To disable this feature, you can set `integration.lualine.enabled = false` in your [modicator configuration](#default-configuration).

Note that Modicator will only create a highlight group from a lualine.nvim mode highlight if that highlight group doesn't already exist.

Modicator tries to find your lualine mode section automatically. However, you can specify the section to use manually in `integration.lualine.mode_section`, and whether to use the section highlight's `bg` or `fg` with `integration.lualine.highlight`.

![modicator.nvim's lualine integration](https://github.com/mawkler/modicator.nvim/assets/15816726/dee0ae61-1435-45cc-98e2-89fd8b46d8a7)

## Issues with other plugins

### marks.nvim

By default, [marks.nvim](https://github.com/chentoast/marks.nvim) highlights number lines with marks using `CursorLineNr`, which makes all line numbers recolored by Modicator every time mode is changed.

To fix this issue, either set `MarkSignNumHL` to something else, or remove the highlight group completely by putting the following snippet anywhere in your configuration:

```lua
local marks_fix_group = vim.api.nvim_create_augroup('marks-fix-hl', {})
vim.api.nvim_create_autocmd({ 'VimEnter' }, {
  group = marks_fix_group,
  callback = function()
    vim.api.nvim_set_hl(0, 'MarkSignNumHL', {})
  end,
})
```

# Development

To run tests, execute the following:

```sh
make test
```

Alternatively, to watch the tests (requires [entr](https://github.com/eradman/entr)):

```sh
make test-watch
```
