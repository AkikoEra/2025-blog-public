# Neovim + lazyvim + markdownpreview 配置
**System：Archlinux**<br>
**Terimnal：Kitty**<br>
**Shell：Oh-my-zsh**<br>
**Editor：Neovim + lazyvim + Yazi**<br>
  
  
目前的解决方案是：使用lazyvim插件管理系统直接安装markdownpreview<br>
如果想要指定浏览器就需要在.vim或者.lua中写入全局变量设置<br>
vim:<br>
> let g:mkdp_browser = '/usr/bin/firefox'<br>

lua:<br>
>vim.g.mkdp_browser = '/usr/bin/firefox'<br>

然后我想要为Catppuccin主题设置透明度，这个在 **~/.local/share/nvim/lazy/catppuccin/** 里面强行修改的话会会在插件管理系统中报错<br>
所以我在 **~/.config/nvim/lua/config/** 中创建了catpuccin.lua中写入全局变量设置<br>
lua:<br>
<!-- ````   Three Backticks to start block -->
-- ~/.config/nvim/lua/config/catppuccin.lua  
local M = {}  
  
function M.setup()  
  require('catppuccin').setup({  
    transparent_background = true,  -- 启用透明背景  
    flavour = "macchiato",  
    dim_inactive = {  
      enabled = true,  
      percentage = 0.15,  
    },  
    float = {  
      transparent = true,  -- 启用浮动窗口透明  
      solid = false,       -- 不使用固体背景  
    },  
  })  
  
  vim.cmd.colorscheme "catppuccin"  
  end

return M
<!-- ```   Three Backticks to end block -->

这里启用浮动窗口透明没有成功.<br>
<!-- ```  Three backticks to start block -->
sudo npm install vsoagent-installer -g
<!-- ```  Three backticks to end block -->
