---
layout: post   	
catalog: true 	
tags:
    - Google
---

# 1. Tab and window

|Shortcut|Action|
|---|---|
|`Ctrl` `N`|Open a new window|
|`Ctrl` `Shift` `N`|Open a new window in Incognito mode|
|`Ctrl` `T`|Open a new tab, and jump to it|
|`Ctrl` `Shift` `T`|Reopen the last closed tab, and jump to it|
|`Ctrl` `Tab`|Jump to the next open tab|
|`Ctrl` `Shift` `Tab`|Jump to the previous open tab|
|`Ctrl` `1-8`|Jump to a specific tab|
|`Ctrl` `9`|Jump to the last tab|
|`Alt` `Home`|Open your home page in the current tab|
|`Alt` `Left`|Open the previous page from your history in the current tab|
|`Alt` `Right`|Open the next page from your history in the current tab|
|`Ctrl` `W`|Close the current tab|
|`Ctrl` `Shift` `W`|Close the current window|
|`Alt` `Space` `N`|Minimize the current window|
|`Alt` `Space` `X`|Maximize the current window|
|`Alt` `F4`|Close the current window|
|`Ctrl` `Shift` `Q`|Quite Google Chrome|


# 2. Google Chrome features

|Shortcut|Action|
|---|---|
|`Alt` `F`|Open the Chrome menu|
|`Ctrl` `Shift` `B`|Show or hide the Bookmarks bar|
|`Ctrl` `Shift` `O`|Open the Bookmarks manager|
|`Ctrl` `H`|Open the History page in a new tab|
|`Ctrl` `J`|Open the Downloads page in a new tab|
|`Shift` `Esc`|Open the Chrome Task Manager|
|`Shift` `Alt` `T`|Set focus on the first item in the Chrome toolbar|
|`F10`|Set focus on the last item in the Chrome toolbar|
|`F6`|Switch focus to unfocused dialog (if showing)|
|`Ctrl` `F`|Open the Find Bar to search the current page|
|`Ctrl` `G`|Jump to the next match to your Find Bar search|
|`Ctrl` `Shift` `G`|Jump to the previous match to your Find Bar search|
|`F12`|Open Developer Tools|
|`Ctrl` `Shift` `Delete`|Open the Clear Browsing Data options|
|`F1`|Open the Chrome Help Center in a new tab|
|`Ctrl` `Shift` `M`|Log in a different user or browse as a Guest|
|`Alt` `Shift` `I`|Open a feedback form|

# 3. Address bar

|Shortcut|Action|
|---|---|
|`(type)` `Enter`|Search with your default search engine|
|`(type)` `Tab`|Search using a different search engine|
|`Ctrl` `Enter`|Add www. and .com to a site name, and open in the current tab|
|`Alt` `Enter`|Open a new tab and perform a Google search|
|`Ctrl` `L`|Jump to the address bar|
|`Ctrl` `K`|Search from anywhere on the page|
|`Shift` `Delete`|Remove predictions from your address bar|


# 4. Webpage shortcuts

|Shortcut|Action|
|---|---|
|`Ctrl` `P`|Open options to print the current page|
|`Ctrl` `S`|Open options to save the current page|
|`Ctrl` `R`|Reload the current page|
|`Ctrl` `Shift` `R`|Reload the current page, ignoring cached content|
|`Esc`|Stop the page loading|
|`Tab`|Browse clickable items moving forward|
|`Shift` `Tab`|Browse clickable items moving backward|
|`Ctrl` `O`|Open a file from your computer in Chrome|
|`Ctrl` `U`|Display non-editable HTML source code for the current page|
|`Ctrl` `D`|Save your current webpage as a bookmark|
|`Ctrl` `Shift` `D`|Save all open tabs as bookmarks in a new folder|
|`F11`|Turn full-screen mode on or off|
|`Ctrl` `+`|Make everything on the page bigger|
|`Ctrl` `-`|Make everything on the page smaller|
|`Ctrl` `0`|Return everything on the page to default size|
|`Space`|Scroll down a webpage, a screen at a time|
|`Shift` `Space`|Scroll up a webpage, a screen at a time|
|`Home`|Go to the top of the page|
|`End`|Go to the bottom of the page|
|`Shift` `(scroll mouse)`|Scroll horizontally on the page|
|`Ctrl` `Left`|Move your cursor to the front of the previous word in a text field|
|`Ctrl` `Right`|Move your cursor to the back of the next word in a text field|
|`Ctrl` `Backspace`|Delete the previous word in a text field|
|`Alt` `Home`|Open the Home page in the current tab|
# 5. 允许在控制台粘贴内容

默认是不允许在控制台进行粘贴内容的

Warning: Don’t paste code into the DevTools Console that you don’t understand or haven’t reviewed yourself. This could allow attackers to steal your identity or take control of your computer. Please [type](https://so.csdn.net/so/search?q=type&spm=1001.2101.3001.7020) ‘allow pasting’ below to allow pasting.

在控制台输入：allow pasting 回车即可

# 6. 修改User-Agent的方法

添加启动参数 `--user-agent="自定义的User-Agent值"`，可在命令行或快捷方式的“目标”框中使用

# 7. 请求的重放

通过鼠标在请求的列表中找到我们需要复制的请求，右键——Copy——Copy as fetch

到这里我们的请求已经被复制下来了，那怎么实现重新请求呢？很简单，还是刚才的页面，我们切换到Console页面，直接粘贴回车即可，需要多次请求则粘贴多次。