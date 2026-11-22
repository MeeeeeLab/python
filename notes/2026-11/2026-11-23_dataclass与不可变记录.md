# 2026-11-23｜dataclass 与不可变记录

> 主线延伸——类与对象 · Python · 建议用时 50–60 分钟 · 发布内容不表示已经完成。

## 今日目标

- 用 `@dataclass` 根据字段自动生成构造、显示和相等比较方法。
- 用 `frozen=True` 表达不应原地修改的记录。
- 用 `replace` 创建成绩更正后的新对象，并验证正常与边界情况。

## 先理解概念

普通数据类常要重复编写 `__init__`、`__repr__` 和 `__eq__`。`@dataclass`读取带类型注解的字段并生成这些常用方法；它不会自动验证字段值，业务校验仍要写在 `__post_init__` 中。

`frozen=True`模拟不可变实例。创建以后给字段重新赋值会抛出 `FrozenInstanceError`。需要更正成绩时，用 `dataclasses.replace(record, score=91)`得到新记录，旧对象继续保持原值。

## 跟着代码学

```python
from dataclasses import dataclass, replace

@dataclass(frozen=True, order=True)
class StudentRecord:
    student_id: str
    name: str
    score: int

old = StudentRecord("S02", "Amy", 88)
new = replace(old, score=91)
print(old)
print(new)
```

输出：

```text
StudentRecord(student_id='S02', name='Amy', score=88)
StudentRecord(student_id='S02', name='Amy', score=91)
```

`order=True`按字段声明顺序生成比较方法；本例先比较 `student_id`，再比较姓名和成绩。

## 易错点

| 易错理解 | 正确理解 |
| --- | --- |
| dataclass会自动检查类型和值 | 类型注解通常不做运行时校验，仍需显式检查 |
| frozen表示对象绝对不可变 | 它阻止字段重新赋值；字段内部若是可变对象仍需谨慎 |
| 更正成绩应直接改旧对象 | 用replace生成新对象并替换集合中的记录 |
| order=True按成绩排序 | 默认按字段定义顺序逐个比较 |

## 动手与验收

运行配套项目，覆盖自动相等、稳定排序、重复学号、冻结修改、非法成绩和缺失学号。实际完成后再自行勾选：

- [ ] 能解释dataclass生成了哪些方法。
- [ ] 能说明frozen与replace的配合。
- [ ] 能运行正常与边界测试。

## 来源

- [Python 官方：dataclasses](https://docs.python.org/3/library/dataclasses.html)
- [Python 官方教程：Classes](https://docs.python.org/3/tutorial/classes.html)
- [Python 官方：dataclasses.replace](https://docs.python.org/3/library/dataclasses.html#dataclasses.replace)

以上资料于 2026-11-23 实际核对；笔记与练习为原创教学内容。
