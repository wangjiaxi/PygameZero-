

Pygame Zero会自动识别并调用您定义的事件钩，这种方法可以使您不必自己实现事件循环机制。

<!--more-->

## 游戏循环挂钩

一个典型的循环钩看起来有点像这样：

```python
while game_has_not_ended():
    process_input()
    update()
    draw()
```

输入处理要更加复杂一些，但Pygame Zero可以让您在自己的游戏模块中轻松地定义`update()`和`draw()`函数。

## ` draw()`

​		当需要重绘游戏窗口时，Pygame Zero就会调用这个函数。

​		`draw()` 函数必须是一个无参函数。

​		Pygame Zero会在游戏窗口需要重绘时进行计算，以避免屏幕没有任何改变时重绘。它将在游戏循环的每一		步的以下的情况中绘制屏幕：

- 如果已经定义了一个`update()`功能（见下文）。

- 如果有时钟事件。

- 如果一个输入事件被触发。

  如果您尝试修改`draw()`函数中的某些内容或使其具有动画效果，这种做法会让你很难受。例如，这段代码就是错误的：外星人并不一定会在屏幕上继续移动：

```python
def draw():
    alien.left += 1
    alien.draw()
```

​		正确的做法是使用`update()`函数来修改或让元素动起来，`draw()`函数仅仅是在屏幕上绘制画面。

```python
def draw():
    alien.draw()

def update():
    alien.left += 1
```

## `update()`或 `update(dt)`

​		Pygame Zero会调用这个函数来实现您的游戏逻辑。`update()`会被重复调用，每秒钟60次。

​		编写`update()`函数有两种不同的方法。

​		在简单的游戏中，您可以假设每次调用`update()`函数都经过了一小段时间（几分之一秒）。也许你甚至并不关心时间的步长多大：您可以只按每帧固定的像素数移动对象（或者通过一个常量来让它们加速等等）。

​		一种更高级的方法，是根据两次调用之间实际花费的时间，来进行移动和物理计算。这样可以提供更流畅的动画，但涉及的计算可能更难，并且当时间步长变大时，你要更小心的处理来避免意外发生。

​		要使用基于时间的方法，您可以修改`update()`函数让它只接收一个参数。这样做以后，Pygame Zero会以**秒**为单位传递经过的时间。您可以使用它来衡量有段移动的计算。

# 事件处理挂钩

与游戏循环挂钩类似，你的Pygame Zero程序可以通过定义具有特定名称的函数来响应输入事件。

有点像`update()`函数这种情况，Pygame Zero会通过检查事件处理函数来决定如何调用。所以你不需要让事件处理函数传参。例如，Pygame Zero会调用 `on_mouse_down`函数的任何变体：

```python
def on_mouse_down():
    print("Mouse button clicked")

def on_mouse_down(pos):
    print("Mouse button clicked at", pos)

def on_mouse_down(button):
    print("Mouse button", button, "clicked")

def on_mouse_down(pos, button):
    print("Mouse button", button, "clicked at", pos)
```

它通过查看参数的名称来完成此操作，所以参数必须完全拼写正确，就像上边代码列出的一样。每个事件挂钩都有一组可以使用的不同参数，如下所述。

`on_mouse_down([pos][, button])`

​		按下鼠标按键时调用。

参数：

- **pos** – 一个元组(x, y) 表示按键按下时鼠标指针的位置。
- **button** – 一个 `mouse` 枚举值，表示按下的鼠标按键。

`on_mouse_up`([*pos*][, *button*])

`on_mouse_up([pos][, button])`

​		释放鼠标按键时调用。

参数：

- **pos** – 一个元组(x, y) 表示按键释放时鼠标指针的位置。
- **button** – 一个 `mouse` 枚举值，表示释放的鼠标按键。

`on_mouse_move([pos][, rel][, buttons])`

​		移动鼠标时调用。

参数：

- **pos** – 一个元组(x, y) 表示鼠标指针移动到的位置。

- **rel** – 一个元组(delta_x, delta_y)表示鼠标指针位置的变化。

- **buttons** – 一组 `mouse`枚举值，表示在移动过程中按下的按键。

要处理鼠标拖动，请使用下面这样的代码：

```python
def on_mouse_move(rel, buttons):
    if mouse.LEFT in buttons:
        # the mouse was dragged, do something with `rel`
        ...
```

`on_key_down([key][, mod][, unicode])`

​		按下按键时调用。

参数：

- **key** – 一个整数，表示按下的键。
- **unicode** – 相关时键入的字符编码。 并非所有按键都会产生可打印的字符 - 许多可能是控制字符。如果按键与Unicode字符编码不对应，则为空字符串。
- **mod** –  按下的修饰键的位掩码。

`on_key_up([key][, mod])`

​		释放按键时调用。

参数:

- **key** – 一个整数，表示释放的按键。
- **mod** – 按下的修饰键的位掩码。

`on_music_end()`

​		音乐曲目结束时调用。

​		请注意，如果将音轨配置为循环，则不会调用此方法。

# 鼠标和键盘按键

内置对象`mouse`，`keys`可用于确定在上述事件中按下了哪些按钮或按键。

请注意，鼠标滚轮事件显示为按下按钮 `WHEEL_UP`/ `WHEEL_DOWN`按钮常量。

- `mouse`类

  可以由`on_mouse_*`处理程序接收的内置枚举按钮 。

  `LEFT` `MIDDLE` `RIGHT` `WHEEL_UP` `WHEEL_DOWN`

- `keys`类

  可由`on_key_*` 处理程序接收的内置枚举按钮。
  
  `BACKSPACE` `TAB` `CLEAR` `RETURN` `PAUSE` `ESCAPE` `SPACE` `EXCLAIM` `QUOTEDBL` `HASH` `DOLLAR` `AMPERSAND` `QUOTE` `LEFTPAREN` `RIGHTPAREN` `ASTERISK` `PLUS` `COMMA` `MINUS` `PERIOD` `SLASH` `K_0` `K_1` `K_2` `K_3` `K_4` `K_5` `K_6` `K_7` `K_8` `K_9` `COLON` `SEMICOLON` `LESS` `EQUALS` `GREATER` `QUESTION` `AT` `LEFTBRACKET` `BACKSLASH` `RIGHTBRACKET` `CARET` `UNDERSCORE` `BACKQUOTE` `A` `B` `C` `D` `E` `F` `G` `H` `I` `J` `K` `L` `M` `N` `O` `P` `Q` `R` `S` `T` `U` `V` `W` `X` `Y` `Z` `DELETE` `KP0` `KP1` `KP2` `KP3` `KP4` `KP5` `KP6` `KP7` `KP8` `KP9` `KP_PERIOD` `KP_DIVIDE` `KP_MULTIPLY` `KP_MINUS` `KP_PLUS` `KP_ENTER` `KP_EQUALS` `UP` `DOWN` `RIGHT` `LEFT` `INSERT` `HOME` `END` `PAGEUP` `PAGEDOWN` `F1` `F2` `F3` `F4` `F5` `F6` `F7` `F8` `F9` `F10` `F11` `F12` `F13` `F14` `F15` `NUMLOCK` `CAPSLOCK` `SCROLLOCK` `RSHIFT` `LSHIFT` `RCTRL` `LCTRL` `RALT` `LALT` `RMETA` `LMETA` `LSUPER` `RSUPER` `MODE` `HELP` `PRINT` `SYSREQ` `BREAK` `MENU` `POWER` `EURO` `LAST`

此外，您可以访问一组表示**修饰键**的常量：

- `keymods`类

  表示在`on_key_up`/ `on_key_down`事件期间可能已被压下的修饰键的常量。
  
  `LSHIFT` `RSHIFT` `SHIFT` `LCTRL` `RCTRL` `CTRL` `LALT` `RALT` `ALT` `LMETA` `RMETA` `META` `NUM` `CAPS` `MODE`