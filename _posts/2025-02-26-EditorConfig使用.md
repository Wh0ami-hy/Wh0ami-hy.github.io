---
layout: post   	
catalog: true 	
tags:
    - IDE
---
# 1. EditorConfig

团队中如何统一格式化风格呢？使用EditorConfig，只需要把`.editorconfig`文件放在项目根目录即可，同时提交到代码库

它通过一个配置文件（通常命名为 .editorconfig）来定义代码的缩进风格、换行符、字符编码等规则。它的主要目的是在团队协作或跨不同编辑器时，确保代码风格的一致性，避免因编辑器默认设置不同而导致的格式混乱。


```
# 表示这是根级配置文件
root = true

# 匹配所有文件
[*]
indent_style = space    # 缩进使用空格
indent_size = 4         # 缩进大小为4个空格
end_of_line = lf        # 换行符使用Unix风格（\n）
charset = utf-8         # 字符编码为UTF-8
trim_trailing_whitespace = true  # 删除行尾多余空格

# 针对特定文件类型覆盖规则
[*.py]
indent_size = 2         # Python文件缩进改为2个空格

[*.md]
trim_trailing_whitespace = false  # Markdown文件保留行尾空格
```

**常见属性说明**

- indent_style: 缩进风格，可以是 space（空格）或 tab（制表符）。
  
- indent_size: 缩进的字符数。
  
- end_of_line: 换行符类型（lf 表示 Unix 风格，crlf 表示 Windows 风格）。
  
- charset: 文件编码，如 utf-8、latin1 等。
  
- trim_trailing_whitespace: 是否删除行尾多余空格。
  
- root: 是否为项目的根配置文件，设为 true 表示不再向上查找其他 .editorconfig 文件

# 2. IDEA中使用

IntelliJ IDEA 默认内置了对 EditorConfig 的支持。只要项目中存在 .editorconfig 文件，IDEA 会自动读取并应用其中的规则。无需额外安装插件。

开启 `File > Settings > Editor > Code Style > Enable EditorConfig support`。

开启 `File > Settings > Tools > Action on Save > Reformat code`

如果有冲突（比如 IDEA 的代码风格设置与 .editorconfig 不一致），EditorConfig 的优先级通常更高。

```
# .editorconfig based on Google Java Style Guide for IntelliJ IDEA  
# 遵循 Google Java Style Guide (https://google.github.io/styleguide/javaguide.html)root = true  
  
# 通配所有文件的基本设置  
[*]  
charset = utf-8  
end_of_line = lf # Unix 风格换行符  
insert_final_newline = true # 文件末尾空行  
trim_trailing_whitespace = true # 移除行尾空白  
  
# Java 文件特定设置  
[*.java]  
indent_style = space # 使用空格缩进  
indent_size = 2 # Google 规范：2 空格缩进  
continuation_indent_size = 4 # Google 规范：续行缩进 4 空格  
max_line_length = 100 # Google 规范：行长度限制 100 字符  
  
# 数组初始化（Google 规范推荐换行和对齐）  
ij_java_array_initializer_new_line_after_left_brace = true # 左括号后换行  
ij_java_array_initializer_right_brace_on_new_line = true # 右括号新行  
ij_java_align_array_initializer_expressions = true # 对齐数组元素  
  
# 方法参数和调用（Google 规范偏好紧凑但清晰）  
ij_java_method_parameters_new_line_after_left_paren = false # 左括号后不换行  
ij_java_method_parameters_right_paren_on_new_line = false # 右括号不换行  
ij_java_call_parameters_wrap = normal # 必要时换行  
ij_java_align_multiline_parameters = false # 多行参数不对齐（Google 倾向自然换行）  
  
# 导入设置（Google 规范要求特定顺序）  
ij_java_imports_layout = java.**, javax.**, |, org.**, |, com.**, |, * # 按包名前缀分组  
ij_java_layout_static_imports_separately = true # 静态导入单独分组  
ij_java_use_single_class_imports = true # 使用单类导入而非 *  
# 空白行（Google 规范要求适度分隔）  
ij_java_blank_lines_after_package = 1 # 包声明后 1 空行  
ij_java_blank_lines_before_imports = 0 # 导入前无空行  
ij_java_blank_lines_after_imports = 1 # 导入后 1 空行  
ij_java_blank_lines_after_class_header = 0 # 类头后无空行  
ij_java_blank_lines_before_fields = 0 # 字段前无空行  
ij_java_blank_lines_after_fields = 0 # 字段后无空行  
ij_java_blank_lines_before_methods = 1 # 方法前 1 空行（类中第一个方法除外）  
  
# 空格（Google 规范的严格要求）  
ij_java_space_after_colon = true # 冒号后空格（如三元运算符）  
ij_java_space_before_colon = false # 冒号前无空格  
ij_java_space_after_comma = true # 逗号后空格  
ij_java_space_before_comma = false # 逗号前无空格  
ij_java_space_around_assignment_operators = true # 赋值运算符前后空格  
ij_java_space_inside_one_line_enum_braces = false # 枚举单行定义无多余空格  
  
# 特殊规则  
ij_java_wrap_long_lines = true # 超出 100 字符时自动换行  
ij_java_do_not_indent_top_level_class_members = true # 顶级类成员不缩进（Google 特殊规则）
```

# 3. VSCode中使用


打开 **Settings**（Ctrl+, / Cmd+,）。

搜索 editorconfig，确保 “EditorConfig: Enabled” 是开启状态。

```
# 表示这是根配置文件
root = true

# 适用于所有文件
[*]
charset = utf-8
indent_style = space
indent_size = 4
end_of_line = lf
trim_trailing_whitespace = true
insert_final_newline = true

# 针对特定文件类型
[*.js]
indent_size = 2

[*.md]
indent_size = 2
trim_trailing_whitespace = false
```