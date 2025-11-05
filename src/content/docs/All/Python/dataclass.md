---
title: dataclass
date: 2025-11-05
---

## 使用dataclass来统一管理配置

一些心得。

1. 尽量不要使用多种数据类型。类型检查会经常报错。
2. 带默认值的参数，必须放在最后。不能放在前面。也不能放在中间。
3. 参数的值可以计算值，但不能依赖其它参数来计算。如果要依赖其它参数，则必须使用函数。

```python
from dataclasses import dataclass, field
from pathlib import Path

@dataclass
class Example:
    a: str  # 必填，无默认
    b: Path = field(init=False)  # 不在 __init__ 中初始化

    def __post_init__(self):
        self.b = Path.cwd() / self.a  # 正确使用 self.a
```