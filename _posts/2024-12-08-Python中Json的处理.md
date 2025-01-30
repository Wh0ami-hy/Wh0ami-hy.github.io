---
layout: post
catalog: true
tags:
  - Python
---





- **JSON 格式** 中，布尔值应为小写的 `true` 和 `false`。
- **Python 格式** 中，布尔值应为大写的 `True` 和 `False`。
- 使用 `json` 库或者 `requests` 库时，Python 会自动处理这个转换，无需手动干预。

如果你手动操作 JSON 数据，请确保正确使用 Python 的 `True` 和 `False`。

```
import json

# 正确的 JSON 字符串，注意小写的 "true"
json_string = '{"success": true, "message": "Request completed successfully"}'

# 使用 json.loads() 解析 JSON 字符串
response_data = json.loads(json_string)

# 输出解析后的数据
print(response_data)

```

