# Emacs的文件管理器

### dired[^1]

dired 是 Emacs 自带的文件管理器，操作非常方便，再加上一些扩展之后无疑是一个理想的文件管理器。看看这里来了解如何增强你的 dired 。

### Mark & Flag

dired 最方便的一点就是可以对许多文件进行标记，并进行批量操作。标记的方 法有很多，最普通的标记就是 d 为当前文件贴上删除标签，之后可以使用 x 来 真正删除所有贴上删除标签的文件。

dired 还提供了许多预定义的方便的标记操作(当使用 C-u 传递一个前缀参数 时，他们执行相反操作，即去掉标记)，例如：

+ `* *` 标记所有可执行文件。
+ `* @` 标记所有符号链接。
+ `* /` 标记所有目录(不包括 . 和 .. )。
+ `* s` 标记所有文件(不高考 . 和 .. )。
+ `* .` 标记具有给定扩展名的文件。
+ `% m` REGEXP <RET> 或 * % REGEXP <RET> 标记所有匹配到给定的正则表达式 的文件。
+ `% g` REGEXP <RET> 标记所有文件 内容 匹配到给定的正则表达式的文件。

另外，还有一些相关的命令：

+ `u` 去除当前行的标记。
+ `<DEL>` 上移一行并去除该行的标记。
+ `U` 去除所有标记。
+ `* ? MARKCHAR` 或 `M-<DEL>` 去除所有以 MARKCHAR 标记的文件的标记，如果 传递一个前缀参数，则会对每一个文件要求你确认是否去除标记。
+ `t` 交换标记，即所有原来标记为 * 的文件被置于未标记状态，原来未标记的 文件被标记为 * ，原来有其他标记的文件不受影响。

上面的操作都是使用 `*` 进行标记，但是 dired 可以使用更多的字符进行标记， 只是没有提供相应的快捷键操作而已，你可以先以 `*` 标记，然后使用 `* c OLD-MARKCHAR NEW-MARKCHAR` 来把 `*` 标记变换成其他标记，几乎任何字符(当然不包括中文这种多字节的字符)都可以作为标记，不过空格被特殊对待，用于表示所有未标记的文件。

列举了这么多命令，多少有些枯燥，作为一个例子，我们来把当前目录下的所有备份文件移动到 `~/backup` 目录下。假设当前目录已经有一些文件被你以 `D` 标记，但是暂时还不想删除：

1. 选择个临时标记，比如 `t` ，只要保证当前 buffer 里面没有已经存在的 这种标记就行了。
2. `* c D t` 把当前所有 `D` 标签换为 `t` 标签。
3. `~` 以 `D` 标记所有备份文件。
4. `* c D *` 把 `D` 标签换为 `*` 标签。
5. `R ~/backup <RET>` 来把所有标记为 `*` 的文件移动到 `~/backup` 目录里面。
6. `* c t D` 恢复原来的 `D` 标记。

当然这要假设你原来没有设定其他的 `*` 标记，要不然你也可以再添加一个临时标记。总之操作和清晰也很方便，感觉像在汇编语言里面使用寄存器一样，大多数批量操作都是针对 `*` 标记的，所以对某个标记操作之前需要把他先转换为 `*` 标记。

另外，还有一个非常强大的标记的方法，绑定到 `M-(` 或 `* (` 上。它可以让你使 用断言来决定标记哪些文件。 `C-h f dired-mark-sexp RET` 可以得到详细的文档。 这个功能非常强大，有点类似于 find 程序，例如，标记所有没有编译的 Elisp 文件(如果编译了，那么会有一个同名，但是扩展名为 `.elc` 的文件存在) 的方法 是输入这个断言： `(and (string-match "\\.el$" name) (not (file-exists-p (concat name "c")))) `。

### 文件操作

dired 内建了很多文件操作，对于操作的文件有一个统一的约定，按照顺序是：

1. 如果你通过 `C-u` 传递一个前缀参数 `N` ，那么它对从当前行开始的 `N` 行执行操作(`N`也可以是负数)。
2. 如果有被标记为 `*` 的文件，则以这些文件为操作对象。
3. 只对当前光标所在的文件进行操作。

#### 常用操作

这些命令全部绑定到大写字母上，记忆也非常方便：

+ `C` 拷贝文件。把 `dired-recursive-copies` 设为非 `nil` 的值可以递归拷贝目录，通常我们设定为 `top` ，这表示对于顶层目录 dired 会先进行询问是否要递归拷贝，而其中的子目录则不再询问。如果嫌询问太麻烦，可以直接设置为 `always` 。
+ `D` 删除文件。类似的有一个 `dired-recursive-deletes` 变量可以控制递归删 除。
+ `R` 重命名文件，也就是移动文件。
+ `H` 创建硬链接。
+ `S` 创建软链接。
+ `M` 修改权限位，即 shell 里面的 chmod 命令。
+ `G` 修改文件所属的组。
+ `O` 修改文件的所有者。
+ `T` 修改文件的修改时间，类似于 shell 命令 touch 。
+ `P` 打印文件。
+ `Z` 压缩或解压文件。
+ `L` 把 Elisp 文件加载进 Emacs 。
+ `B` 对 Elisp 文件进行 Byte compile 。
+ `A` 对文件内容进行正则表达式搜索，搜索会在第一个匹配的地方停下，然后 可以使用 M-, 搜索下一个匹配。
+ `Q` 对文件内容进行交互式的正则表达式替换。

#### shell 命令
除了这些操作，还可以使用 `!` 来执行 shell 命令。这里介绍了自动猜测 shell 命令的办法，就类似于通常的文件管理器里面以关联的程序打开了。

#### 强大的重命名功能
dired 有一个文件名转换的理念，所以转换，并不一定是重命名，还可以是复制和创建链接。所以，除了 `% u` 和 `% l` 重命名原文件为大写、小写外，一个使用正则表达式进行转换的命令提供了四个选项： `% X` 其中 `X` 可以是 `R` , `C` , `H` 和 `S` ，分别代表重命名、复制、创建硬链接和创建软链接，他们使用匹配和替换的机制，这有点像 rename 这个程序，例如： `% R \.[^.]*$ <RET> .1\& <RET>` 给原来的文件名加个标号 1 ，把 `foo.txt` 变成 `foo.1.txt` 。

另外，dired 还有一个叫做 Wdired 的扩展可以直接在 dired 的 buffer 里面编辑文件名来达到重命名的效果。使用 `M-x wdired-change-to-wdired-mode` 进入编辑模式，这个时候可以直接像编辑普通文本一样编辑文件名，还可以添加路径来实现把文件移动到其他目录。除了文件名可以编辑以外，其他部分被标记为只读，但是如果把 `wdired-allow-to-change-permissions` 设为 t 的话，还可以编辑文件的权限位。编辑完成之后使用 `C-c C-c` 来应用所做的编辑。非常方便。

### 排序和过滤
用 `C-u s` 可以编辑 dired 的 `dired-listing-switches` 这个变量，从而达到控制排序的方法的目的。
另外 dired 还有一个 `k` 用于去掉不想显示出来的文件，它并不删除磁盘上的文件，只是临 时从 dired 的 buffer 中去掉他们， `g` 刷新一下它们又会显示出来，这样，首先用强大的标记功能进行标记，然后使用 `k` 去掉，就实现了过滤的功能。

### 子目录操作
dired 允许同时操作当前目录和子目录。在 `dired-listing-switches` 里面加入 `R` 选项就可以显示子目录，如果只是想临时显示某个子目录的内容，对该目录执 行 `i` 操作就会把该子目录的内容添加到 dired 当前 buffer 的末尾并把光标移 动到那里，dired 在移动之前会先设置一个 mark ，所以可以使用 `C-u C-<SPC>`。 

### 其他功能
还有一些方便的功能，我把几个常用的命令列在这里：

* `+` 创建目录
* `w` 复制文件名，如果通过 `C-u` 传递一个前缀参数 `0` ，则复制决定路径名， 如果只是 `C-u` 则复制相对于 dired 当前目录的相对路径。
* `I` 把当前文件以 info 文档的格式打开。
* `N` 把当前文件以 man 格式打开(使用 WoMan)。
* `Y` 为所有标记的文件创建一个到指定目录的相对符号连接(即使用相对路径进 行引用，而不是绝对路径)。

[^1]: <http://lifegoo.pluskid.org/wiki/EmacsAsFileManger.html>

tags: #emacs #dired
