---
layout: post   	
catalog: true 	
tags:
    - python

---





# GUI-tkinter库

## 基础语法

### 引入库

```python
import tkinter as tk
```

### 主窗口 

* 创建主窗口 

  ```python
  window = tk.Tk() #创建主窗口window
  ```

* 为窗口命名

  ```python
  window.title('My Window')
  ```

* 设置窗口大小

  ```python
  window.geometry('500x300') #长x宽，这里乘号用x
  ```

* 锁定窗口大小

  ```python
  window.resizable(0,0)
  #分别表示x,y方向的可变性；1表示可变，0表示不可变
  ```

* 主窗口循环显示

  ```python
  window.mainloop()
  ```

* 关闭主窗口

  ```python
  window.quit()
  ```

* 刷新页面

  ```
  window.update()
  ```

  



## 常用窗口部件

### **Label**标签

* 格式

```python
l = tk.Label(window, text='你好', bg='green', font=('Arial', 12), width=30, height=2)
```

* `.config`

该方法用于修改标签中的属性

```python
label.config(text='123')
```

### Button按钮

按钮能够包含文本或图象，并且你能够将按钮与一个Python函数或方法相关联。当这个按钮被按下时，Tkinter自动调用相关联的函数或方法。

* 格式

```python
b = tk.Button(window, text='hit me', font=('Arial', 12), width=10, height=1, command=hit_me)
b.pack()
```

* command

调用函数命令

在command下传递参数，使用`lambda`

```python
button = tk.Button(window, text="...", width=30,command=lambda: 函数名(参数))
```



### Entry单行文本输入域

* 格式

```python
e1 = tk.Entry(window, show='*', font=('Arial', 14))
e1.pack()
```

* show

设置输入筐中内容的显示形式

```python
e1 = tk.Entry(window, show='*', font=('Arial', 14))   # 显示成密文形式
e2 = tk.Entry(window, show=None, font=('Arial', 14))  # 显示成明文形式
```

* textvariable

tkinter字符串变量，使用`var = tk.StringVar()`定义，`.set`设置变量的内容，`.get`获取变量的内容

```python
var = StringVar()
e1 = tk.Entry(window,textvariable=var)
e1.pack()
print(e1.get())
```

### Text多行文本输入域

输入多行文本，允许你用不同的样式和属性来显示和编辑文本，同时支持内嵌图象和窗口

* 格式

```python
t = tk.Text(window, height=3)
t.pack()
```

* `.insert`

该方法表示插入，可用于GUI界面的输出

```python
import tkinter as tk 
from tkinter import *

window = tk.Tk()

window.title('My Window')

t = tk.Text(window, height=3)
t.pack()
t.insert(INSERT," I LOVE YOU")
t.insert(END,"HAHA")
```

* `.delete('1.0','end')`

清空text中的内容

### Listbox列表框

3部分

* 红列表框控件，展示给用户选项
* 显示所选按钮，点击该按钮，在区域显示所选
* 显示用户选择的列表框选项

```python
import tkinter as tk

window = tk.Tk()
window.title('列表框')          # 设置窗口的标题
window.geometry('200x240')     # 设置窗口的大小

list_itmes = tk.StringVar()#列表框初始化
list_itmes.set(('python', 'c++', 'java', 'php'))         # 设置可选项

# 创建列表框
lb = tk.Listbox(window, listvariable=list_itmes)
lb.pack()

no_select = '没有选中任何选项'

def click_button():
    select = lb.curselection()
    print(len(select))
    if len(select) == 0:
        label_text.set(no_select)
    else:
        text = lb.get(select)
        label_text.set('你选择了{text}'.format(text=text))

# 创建button
button = tk.Button(window,
    text='显示所选',             # 显示在按钮上的文字
    width=15, height=2,
    command=click_button)     # 点击按钮时执行的函数
button.pack()                 # 将按钮锁定在窗口上

# 创建label用于显示所选择的列表框选项
label_text = tk.StringVar()    #创建变量
label_text.set(no_select)
label = tk.Label(window,
    width=15, height=2,        # 标签长宽
    textvariable=label_text    # label控件将显示label_text的值
    )
label.pack()               # 将标签固定在窗口上

window.mainloop()             # 启动窗口
```

### Radiobutton单选

* variable

表示选项被选择后返回的值，使用var = IntVar()定义，单选中设定一个IntVar()变量

* value

设置一个选项对应的值

```python

def select():
    label.config(text='你选择了' + var.get())
#通过var对象获取了被选中的单选按钮的值，然后使用label的config方法修改标签上的显示文字

r1 = tk.Radiobutton(window, text='python',
                    variable=var, value='python',
                    command=select)
r1.pack()

r2 = tk.Radiobutton(window, text='java',
                    variable=var, value='java',
                    command=select)
r2.pack()


window.mainloop()
```

### Checkbutton多选

* variable

表示选项被选择后返回的值，使用var = IntVar()定义，多选中设定多个

* onvalue、offvalue

设定选项被选择、没被选择后返回的值，默认为onvalue=1, offvalue=0

```python
import tkinter as tk

window = tk.Tk()
window.title('checkbutton')
window.geometry('200x200')

label = tk.Label(window, bg='yellow', width=20, text='')
label.pack()


def select():
    select_lst = []

    if var1.get() == 1:      # var1.get()获取c1的状态值
        select_lst.append('python')

    if var2.get() == 1:
        select_lst.append('java')

    if var3.get() == 1:
        select_lst.append('php')

    text = "你选择了 " + ','.join(select_lst)
    label.config(text=text)


var1 = tk.IntVar()
var2 = tk.IntVar()
var3 = tk.IntVar()

c1 = tk.Checkbutton(window,
                    text='Python',      # 选项显示内容
                    variable=var1,      # 绑定变量var1
                    onvalue=1,          # 被选中时的状态值
                    offvalue=0,         # 没有被选中时的状态值
                    command=select)

c2 = tk.Checkbutton(window, text='java', variable=var2, onvalue=1, offvalue=0,
                    command=select)
c3 = tk.Checkbutton(window, text='php', variable=var3, onvalue=1, offvalue=0,
                    command=select)

#设置onvalue和offvalue参数，这里设置的是1和0，分别代表选中和未选中。
#设置variable参数，将控件的值与变量绑定在一起，这样，通过变量var1就可以获取控件c1的状态值。
c1.pack()
c2.pack()
c3.pack()


window.mainloop()
```

### Scale范围控件

* 格式

```python
scale = tk.Scale(window,
             label='选择价格范围',         # 提示语
             from_=50,                  # 最小值
             to=200,                    # 最大值
             orient=tk.HORIZONTAL,      # 横向显示
             length=400,                # 长度
             tickinterval=20,           # 刻度间隔
             resolution=0.1,            # 精确程度
             command=select)
scale.pack()
```

* label

 提示语

* from、to

确定范围，最大与最小

* orient

控件显示方式，默认是纵向显示，`HORIZONTAL`可设为横向显示

* tickinterval

设置刻度间隔

* resolution

设置精确程度

### Canvas画布控件

 画各种图形，也可以展示图片，同时还可以移动他们

* 画线

```python
import tkinter as tk

window = tk.Tk()
window.title('画布')
window.geometry('300x300')

canvas = tk.Canvas(window,
                   bg='white',       # 设置背景色
                   height=200,      # 设置高度
                   width=300)       # 设置宽度

canvas.pack()
# 左上角是0，0  右下角是300, 200
line = canvas.create_line(10, 10, 100, 10)
#create_line创建一条直线时，需要指定4个坐标
window.mainloop()
```



* 画圆、长方形

```python
import tkinter as tk

window = tk.Tk()
window.title('画布')
window.geometry('300x300')

canvas = tk.Canvas(window,
                   bg='blue',       # 设置背景色
                   height=200,      # 设置高度
                   width=300)       # 设置宽度

canvas.pack()
oval = canvas.create_oval(50, 50, 90, 90, fill='red')
rect = canvas.create_rectangle(100, 50, 150, 100)

window.mainloop()
```

* 图形移动

```python
import tkinter as tk

window = tk.Tk()
window.title('画布')
window.geometry('300x300')

canvas = tk.Canvas(window,
                   bg='blue',       # 设置背景色
                   height=200,      # 设置高度
                   width=300)       # 设置宽度

canvas.pack()
oval = canvas.create_oval(50, 50, 90, 90, fill='red')


def moveit():
    canvas.move(oval, 50, 50)   # x方向移动50， y方向移动50


button = tk.Button(window, text='移动圆', command=moveit)
button.pack()

window.mainloop()
```

* 显示图片

tkinter自己的PhotoImage只支持gif格式的图片，因此，我们需要使用PIL的ImageTk，这样，所有格式的图片都可以显示

```python
import tkinter as tk
from PIL import ImageTk

window = tk.Tk()
window.title('画布')
window.geometry('500x500')

canvas = tk.Canvas(window,
                   bg='blue',       # 设置背景色
                   height=500,      # 设置高度
                   width=500)       # 设置宽度


canvas.pack()

image_file = ImageTk.PhotoImage(file='./pic/coolpython.png')
image = canvas.create_image(10, 10, anchor='nw', image=image_file)

window.mainloop()
```



### Menu菜单控件

关于菜单，我们分清两部分，一部分是在菜单栏里直接显示的，一部分是点击某个菜单后下拉显示的

```python
from tkinter import *

window = Tk()
window.wm_title("菜单")
window.geometry("400x300")
window.protocol("WM_DELETE_WINDOW", window.iconify)

def exit():
    window.destroy()

menubar = Menu(window)

# 点击菜单栏下拉显示的菜单
filemenu = Menu(menubar)
for item in ['新建','打开','保存','另存为']:
    filemenu.add_command(label=item)

filemenu.add_command(label="退出", command=exit)
editmenu = Menu(menubar)
for item in ['复制','粘贴','剪切']:
    editmenu.add_command(label=item)

# 在菜单栏上显示的部分
menubar.add_cascade(label="文件", menu =filemenu)
menubar.add_cascade(label="编辑", menu =editmenu)


# 这两种方法都可以
window['menu']= menubar
# window.config(menu=menubar)
window.mainloop()
```



### Combobox 复合框

Combobox是ListBox的改进版，用户除了可以选择外，还可以直接输入，因此被称之为复合框

* `current()` 来获取当前所选元素的索引，并使用 `get()` 方法来获取元素本身
* `bind()` 方法是当用户在下拉列表中选择元素时将回调函数与下拉列表虚拟事件绑定的方法。

```python
import tkinter as tk
from tkinter import ttk, messagebox


window = tk.Tk()
window.title('复合框')       # 设置窗口的标题
window.geometry('200x200')     # 设置窗口的大小

cb = ttk.Combobox(window,values=['Python', 'java', 'c++', 'php'])

cb.pack(side='top')

window.mainloop()
```

### Spinbox 高级输入框

Spinbox相比于Entry更高级一些，它不仅支持直接输入，还允许通过点击上下箭头的按钮调节输入内容

```python
import tkinter as tk
from tkinter import messagebox


window = tk.Tk()
window.title('Spinbox输入框')        # 设置窗口的标题
window.geometry('200x200')          # 设置窗口的大小

int_value_var = tk.IntVar()
sb1 = tk.Spinbox(window,
                  from_=0,          # 最小值0
                  to=100,           # 最大值100
                  increment=5,      # 点击一次变化幅度为5
                  textvariable=int_value_var        # 绑定变量
                  )

sb1.pack()

def press():
    language = sb2.get()
    messagebox.showinfo(title='提示', message='你选择了{language}'.format(language=language))

sb2 = tk.Spinbox(window,
                 values=('python', 'java', 'c++', 'php'),
                 command=press)     # 点击向上按钮或者向下按钮都会粗发press方法

sb2.pack()

window.mainloop()
```

### filedialog 文件对话框

文件对话框是桌面应用里经常使用的功能，你想从本地选择一个文件，需要使用文件对话框，你想将一个文件保存起来，选择保存路径的时候，需要使用文件对话框

* 使用文件对话框选择一个文件，获得文件的绝对路径

```python
import tkinter as tk
from tkinter import filedialog


window = tk.Tk()
window.title('文件对话框')        # 设置窗口的标题
window.geometry('300x50')          # 设置窗口的大小

path_var = tk.StringVar()
entry = tk.Entry(window, textvariable=path_var)
entry.place(x=10, y=10, anchor='nw')


def click():
    # 设置可以选择的文件类型，不属于这个类型的，无法被选中
    filetypes = [("文本文件", "*.txt"), ('Python源文件', '*.py')]
    file_name= filedialog.askopenfilename(title='选择单个文件',
                                  filetypes=filetypes,
                                  initialdir='./'           # 打开当前程序工作目录
                                             )
    path_var.set(file_name)

tk.Button(window, text='选择', command=click).place(x=220, y=10, anchor='nw')
window.mainloop()
```

* 使用文件对话框选择一个文件目录

注意：

```
filedialog.askdirectory()的返回值是一个路径
该路径是用 / 来分割的，如：C:/webshell_Source/php
```



```python
import tkinter as tk
from tkinter import filedialog


window = tk.Tk()
window.title('文件对话框')        # 设置窗口的标题
window.geometry('300x50')          # 设置窗口的大小

path_var = tk.StringVar()
entry = tk.Entry(window, textvariable=path_var)
entry.place(x=10, y=10, anchor='nw')


def click():
    file_name= filedialog.askdirectory(title='选择一个文件夹',
                                  initialdir='./'           # 打开当前程序工作目录
                                             )
    path_var.set(file_name)

tk.Button(window, text='选择', command=click).place(x=220, y=10, anchor='nw')
window.mainloop()
```

* 获取保存文件的路径

```python
import tkinter as tk
from tkinter import filedialog, messagebox


window = tk.Tk()
window.title('文件对话框')        # 设置窗口的标题
window.geometry('300x50')          # 设置窗口的大小


path_var = tk.StringVar()
entry = tk.Entry(window, textvariable=path_var)
entry.place(x=10, y=10, anchor='nw')

def click():
    filetypes = [("文本文件", "*.txt"), ('Python源文件', '*.py')]
    file_name= filedialog.asksaveasfilename(title='保存文件',
                                            filetypes=filetypes,
                                            initialdir='./'           # 打开当前程序工作目录
                                             )
    path_var.set(file_name)

tk.Button(window, text='选择', command=click).place(x=220, y=10, anchor='nw')
window.mainloop()
```

### 布局

#### 设置部件布局

* pack

可以指定上下左右

```python
import tkinter as tk

window = tk.Tk()
window.title('frame示例')       # 设置窗口的标题
window.geometry('400x300')     # 设置窗口的大小

tk.Label(window, text='1').pack(side='top')      # 上
tk.Label(window, text='2').pack(side='left')     # 左
tk.Label(window, text='3').pack(side='bottom')   # 下
tk.Label(window, text='4').pack(side='right')    # 右

window.mainloop()
```

* grid

可以划分方格

```python
import tkinter as tk

window = tk.Tk()
window.title('定位示例')       # 设置窗口的标题
window.geometry('400x300')     # 设置窗口的大小

for i in range(3):
    for j in range(3):
        tk.Label(window, text=i*3+j).grid(row=i, column=j, padx=10, pady=10)
#row指定了行，column指定列，padx设置单元格左右间距，pady设置上下行间距
window.mainloop()
```



* place

就是给精确的坐标来定位，如(50, 100)，就是将部件放在坐标为(x=50, y=100)的这个位置

```python
import tkinter as tk

window = tk.Tk()
window.title('定位示例')         # 设置窗口的标题
window.geometry('400x300')     # 

tk.Label(window, text="我就在这里x=100,y=150").place(x=100, y=150, anchor='nw')

window.mainloop()
#place指明了控件出现的位置，但是控件自身也是有大小的，因此，还需要指明使用控件的哪个部分进行定位，我的代码里设置anchor='nw'，这就就表示用控件的西北角，也就是左上角进行定位。

#上北，下南，左西，右东， 上n,下s，左w,右e 英语单词north,south,west,east的首字母
```



#### frame 容器布局

能将 Windows 分成不同的区,然后存放不同的其他部件. 同时一个 Frame 上也能再分成两个 Frame

```python
import tkinter as tk

window = tk.Tk()
window.title('frame示例')       # 设置窗口的标题
window.geometry('200x100')     # 设置窗口的大小

# 在window上创建一个frame
frm = tk.Frame(window)
frm.pack()

# 在frm上创建两个frame， 一个在左，一个在右
frm_left = tk.Frame(frm)
frm_right = tk.Frame(frm)

frm_left.pack(side='left')      # 设置相对位置，一个在左，一个在右
frm_right.pack(side='right')

# 创建两个label，一个放在frm_left上，一个放在frm_right上
tk.Label(frm_left, text='在左侧').pack()
tk.Label(frm_right, text='在右侧').pack()

window.mainloop()
```



#### LabelFrame 容器控件

LabelFrame与Frame十分相似，都可以用来做布局控件，不同之处在于，LabelFrame有明显的边界，并且可以在边界上设置文字，它划分的区域更加明显。

* 格式

```python
LabelFrame(master=None, **options)
# master -- 父组件
```

```python
#假设我们想让用户选择一门编程语言进行学习，可以使用一组RadioButton供用户选择，这组RadioButton就可以放在LabelFrame中
import tkinter as tk
from tkinter import ttk


window = tk.Tk()
window.title('labelframe')       # 设置窗口的标题
window.geometry('300x100')       # 设置窗口的大小

lf = ttk.Labelframe(window, text='请选择教程', padding=20)
lf.pack()

language_lst = ['python', 'java', 'php', 'go']
i = 0
intVar = tk.IntVar()

for language in language_lst:
    tk.Radiobutton(lf, text=language, value=i,
            variable=intVar).pack(side='left')
    i += 1

window.mainloop()
```



#### Panedwindow 管理窗口布局的容器

Panedwindow 允许添加多个子组件，不需要使用pack，grid，place进行布局，它为每个子组件划分一个区域，你可以通过移动各个区域的分割线来改变区域的大小。在创建Panedwindow时，你可以指定它是垂直布局还是水平布局，Panedwindow可以嵌入到另一个Panedwindow中。

```python
#现在，我们来设计一个布局，将界面分割成3部分
#首先创建一个水平的Panedwindow，然后加入一个label,再加入一个垂直布局的Panedwindow，在这个垂直布局的Panedwindow中，加入两个label
import tkinter as tk
from tkinter import *


window = tk.Tk()
window.title('PanedWindow')       # 设置窗口的标题
window.geometry('600x600')        # 设置窗口的大小


m1 = PanedWindow(window)          # 默认是左右分布的
m1.pack(fill=BOTH, expand=1)

left = Label(m1, text='标签1', bg='blue', width=20)
m1.add(left)

m2 = PanedWindow(orient=VERTICAL)
m1.add(m2)

top = Label(m2, text='标签2', bg='green', height=20)
m2.add(top)

bottom = Label(m2, text='标签3', bg='red')
m2.add(bottom)

window.mainloop()
```



### 弹窗

* messagebox 消息对话框

语法

```python
# 导入消息对话框子模块
import tkinter.messagebox
```

(askokcancel)类型

```python
def test():
    # 弹出对话框
    result = tkinter.messagebox.askokcancel(title = '标题',message='内容：要吃饭嘛？')
    print(result)		# 返回值为True或者False
```

(askquestion)类型

```python
def question():
    # 弹出对话框
    result = tkinter.messagebox.askquestion(title = '标题',message='内容：你吃饭了嘛？')
    print(result)		# 返回值为：yes/no
```

(askretrycancel)类型

```python
def retry():
    # 弹出对话框
    result = tkinter.messagebox.askretrycancel(title = '标题',message='内容：女生拒绝了你！？')
    print(result)		# 返回值为：True或者False
```

(askyesno)类型

```python
def yesno():
    # 弹出对话框
    result = tkinter.messagebox.askyesno(title = '标题',message='内容：你喜欢我吗？')
    print(result)		# 返回值为：True或者False
```

(showerror) 出错提示

```python
def error():
    # 弹出对话框
    result = tkinter.messagebox.showerror(title = '出错了！',message='内容：你的年龄不符合要求。')
    print(result)		# 返回值为：ok
```

(showwarning) 警告

```python
def warning():
    # 弹出对话框
    result = tkinter.messagebox.showwarning(title = '出错了！',message='内容：十八岁以下禁止进入。')
    
    print(result)		# 返回值为：ok
```

(showinto)  信息提示

```python
def info():
    # 弹出对话框
    result = tkinter.messagebox.showinfo(title = '信息提示！',message='内容：您的女朋友收到一只不明来历的口红！')
    
    print(result)		# 返回值为：ok
```



### 创建分页

使用Notebook()创建分页栏，Frame()创建多个框架来当做不同的页面

语法

使用Notebook()需要导入 ttk方法

```python
from tkinter import ttk
```

布局

```python
tab_main=ttk.Notebook()#创建分页栏
tab_main.place(relx=0.02, rely=0.02, relwidth=0.887, relheight=0.876)	#布局时用相对坐标
```

例子

```python
from tkinter import *
import tkinter
from tkinter import ttk

root = Tk()
root.title('测试')
root.geometry('900x600')

tab_main=ttk.Notebook()#创建分页栏
tab_main.place(relx=0.02, rely=0.02, relwidth=0.887, relheight=0.876)

tab1=Frame(tab_main)#创建第一页框架
tab1.place()
tab_main.add(tab1,text='第一页')#将第一页插入分页栏中

Text = Text(tab1,width = 60,height=30)#显示文本框
Text.place(x=10,y=60)


tab2=Frame(tab_main)
tab2.place()
tab_main.add(tab2,text='第二页')

button = Button(tab2,text='1',width=5)	#显示按钮
button.place(x=50,y=10)


root.mainloop()
```



## 技巧总结

### 获取窗口部件的值

使用`StringVar()`对象来完成，把窗口部件的`textvariable`属性设置为`StringVar()`，再通过`StringVar()`的`get()`和`set()`函数可以读取和输出相应内容

## 美化

### 库

* [Sun-Valley-ttk-theme](https://github.com/Wh0ami-hy/Azure-ttk-theme)

* [ttkbootstrap库](https://github.com/Wh0ami-hy/ttkbootstrap)

### 窗口透明化

```python
windows.attributes('-alpha', 0.5)        #0.5表示[0,1]范围的透明度
```

