# shon-arch-hyprland

Актуальные конфиги для Arch Linux + Hyprland (август 2026).

## ⚠️ Важно про Hyprland

Конфиг в `hypr/` пока написан в старом `.conf` (hyprlang) синтаксисе.
Начиная с Hyprland 0.55 основной формат — Lua (`hyprland.lua`), а поддержка
`.conf` будет полностью убрана в 0.57. Планируется миграция на Lua —
см. https://wiki.hypr.land/Configuring/Start/

## Система

|              |                                 |
| ------------ | ------------------------------- |
| **OS**       | Arch Linux                      |
| **WM**       | Hyprland (Wayland)              |
| **Терминал** | Kitty                            |
| **Бар**      | Waybar                          |
| **Меню**     | Wofi                            |
| **Тема**     | Eva theme (+ Catppuccin Macchiato как база) |
| **Шрифт**    | JetBrains Mono Nerd Font        |
| **Монитор**  | eDP-1, 1920x1080 @ 144Hz        |
| **Скриншоты**| hyprshot                        |
| **Обои**     | awww                            |

## Установка

# Step 1

Зависимости:

```bash
sudo pacman -S hyprland waybar wofi kitty thunar hyprlock \
  bluez bluez-utils blueman \
  pipewire pipewire-pulse wireplumber \
  brightnessctl wl-clipboard \
  ttf-jetbrains-mono-nerd noto-fonts noto-fonts-emoji \
  flatpak
```

AUR (через yay):

```bash
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si

yay -S awww hyprshot wlogout
```

# Step 2

Клонировать репозиторий:

```bash
cd ~
git clone https://github.com/shon-88/shon-arch-hyprland.git
```

# Step 3

Скопировать конфиги:

```bash
cd shon-arch-hyprland
cp -r hypr waybar kitty nvim wofi wlogout fastfetch ~/.config
# kitty.conf в репо содержит только личные отличия от дефолта — если хочешь
# сразу увидеть все стандартные опции kitty с комментариями, сгенерируй полный
# шаблон и добавь в конец нужные строки:
#   kitty +runpy 'from kitty.config import *; print(defconf)' > ~/.config/kitty/kitty.conf

mkdir -p ~/Pictures/wallpaper
# положи свои обои в ~/Pictures/wallpaper/ — путь задан в hyprland.conf (exec-once = awww img ...)

mkdir -p ~/Pictures/screenshots
```

# Step 4

Перезапуск:

```bash
hyprctl reload
killall waybar && waybar --log-level error &
```

## Файлы

- **fastfetch** — конфиг fastfetch (`config.jsonc`)
- **hypr** — конфиг Hyprland (`hyprland.conf`, `keybind.conf`, `hyprlock.conf`)
- **kitty** — конфиг терминала kitty (только личные отличия от дефолта)
- **nvim** — конфиг Neovim (packer, LSP, keymaps, colorscheme)
- **waybar** — конфиг и стили waybar
- **wlogout** — layout для wlogout
- **wofi** — конфиг и стили wofi

## Горячие клавиши (основные)

> `$mainMod = SUPER`

| Комбинация            | Действие                         |
| ---------------------- | --------------------------------- |
| `Super + Enter`         | Терминал (kitty)                  |
| `Super + R`             | Меню (wofi)                       |
| `Super + E`             | Файловый менеджер (thunar)        |
| `Super + B`             | Браузер                            |
| `Super + T`             | Telegram                          |
| `Super + I`             | Заблокировать экран (hyprlock)    |
| `Super + C`             | Закрыть окно                      |
| `Super + M`             | Выйти из Hyprland                 |
| `Super + S`             | Scratchpad (special workspace)    |
| `Super + 1–0`           | Переключить workspace             |
| `Ctrl + Alt + 1–9`      | Переместить окно на workspace     |
| `Super + стрелки`       | Переключение фокуса               |
| `Super + H/J/K/L`       | Изменение размера окна            |
| `Super + Shift + H/J/K/L` | Swap window                     |
| `Alt + Shift`           | Сменить раскладку (us/ru)         |
| `Print`                 | Скриншот региона (hyprshot)       |
| `Super + Shift + O`     | Перезапустить waybar              |

## TODO

- [ ] Мигрировать `hyprland.conf` / `keybind.conf` на новый Lua-синтаксис до релиза Hyprland 0.57
- [ ] Проверить биндинг `$mainMod + V` — три подряд идущих `bind` на одну клавишу,
      реально сработает только последний зарегистрированный (`centerwindow`).
      Скорее всего стоит объединить в один bind через `exec + hyprctl dispatch` цепочку.
- [ ] `style.css` импортирует только `eva-theme.css`; `macchiato.css` сейчас не используется —
      либо удалить как мёртвый файл, либо решить, какая тема активна на самом деле.
- [ ] В `eva-theme.css` дублируются переменные `rosewater` и `flamingo` (объявлены дважды подряд) —
      второе объявление просто перетирает первое, стоит почистить.
- [ ] `nvim/init.lua` дублирует LSP-логику (packer bootstrap, clangd, nvim-cmp, autopairs
      настроены инлайн), при этом `require('lsp')` нигде не вызывается, а `nvim/lua/lsp.lua`
      сейчас пустой — решить, переносить ли LSP-конфиг туда или оставить как есть в init.lua.
- [ ] `nvim/lua/plugins.lua` подключает `wbthomason/packer.nvim` через сам packer,
      хотя `init.lua` уже клонирует и грузит packer вручную — избыточно, но не критично.
- [ ] `nvim/lua/colorscheme.lua` ставит тему дважды: сначала `dracula` (+ lualine с темой
      dracula), затем сразу `tokyonight` — реально применяется только tokyonight,
      строки про dracula сейчас мёртвый код.
- [ ] `wlogout/layout` → кнопка Exit ссылается на `$HOME/dotfiles/hypr/scripts/exit.sh` —
      старый путь, такого скрипта нет в репозитории. Поправить путь или создать
      `hypr/scripts/exit.sh`.
