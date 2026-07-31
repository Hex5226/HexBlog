---
title: 学习笔记：Python
description: 我本人学习Python时的记录
pubDate: 2026-07-09
categories: [学习笔记]
tags: [学习笔记, 编程, 编程语言]
draft: false
---
# 注意：本文章非教程，仅作记录，若需参考，请自行筛选有用部分
## 第一天
### 内容概览
> 今日核心主题：面向对象编程（OOP）与数据持久化（JSON）的结合  
> 主要方式：模拟开发银行账户程序（简易化）
1. 基础语法，变量与运算
2. Python中数据类型的转换
3. 列表，字典
4. for，while，with，if等关键字语法
5. 封装函数
6. python对于程序报错的try与except特点（异常处理）
7. 简单的语法嵌套
8. 类，方法与对象
9. 类中的静态方法
10. json保存与读取
11. 类与对象的json保存方法（字典翻译）

**学习用时：6小时**

### 重点内容
#### 一、封装边界：业务逻辑与用户交互的解耦
刚开始写 `BankAccount` 类时，差点把 `input()` 写进 `deposit()` 方法里。后来反应过来：如果这样做，这个类就被“焊死”在黑框里了。以后要是想把它搬到网页或者手机 App 上，根本复用不了。

**正确做法**：
- 类方法只接收参数（如 `deposit(amount)`）。
- 主程序负责 `input()` 拿数据，再传给类。

这样分层之后，业务逻辑和界面交互彻底分开了。

#### 二、序列化协议：to_dict 与 from_dict 模式
`json` 模块只能存字典、列表这些基础类型。若直接 `json.dump(账户对象)`，会报错。

解决办法：在类里加两个翻译方法

- `to_dict(self)`：把自己（对象）拆成一个字典。
- `@staticmethod from_dict(data)`：接收字典，重新拼出一个对象。

**一个踩坑点**：`from_dict(data)` 里的 `data` 只是一个形参（占位符）。在外面调用时，字典变量叫 `item` 还是 `x` 都无所谓，Python 会自动把它塞进 `data` 这个位置

#### 三、上下文管理协议：with 语句的资源安全保障
with 关键字用于管理实现了上下文协议的对象（如文件流）。其核心价值在于确保资源（如文件句柄、网络连接、锁）在使用后被正确释放，即使代码块内抛出异常也不会导致资源泄露

机制：with open(...) as f: 在进入代码块时调用 __enter__，退出时自动调用 __exit__（通常负责关闭或释放资源）

优势：相比手动调用 f.close()，with 提供了更安全、更简洁的资源管理模式，是 Pythonic 编码的标准范式

#### 四、数据流设计：内存对象与存储结构的转换
程序运行期间，只维护一个变量：`accounts = []`，里面全是 `BankAccount` 对象。

存文件的时候，先把对象列表“翻译”成字典列表，再写进 JSON。

读文件的时候，先从 JSON 读出字典列表，再“翻译”回对象列表，交给主程序使用。

这种做法的好处是：主程序从头到尾只操作对象，完全不知道文件是怎么存的。以后要是想换成存数据库，只需要改 `save_accounts` 和 `load_accounts` 这两个函数，主菜单一行都不用动

### 关键代码
#### to_dict()方法将对象翻译为字典
```python
def to_dict(self):
    return {"owner": self.owner, "balance": self.balance}
```
#### from_dict方法将字典翻译为对象
```python
@staticmethod
def from_dict(data):
    return BankAccount(data["owner"], data["balance"])
```
#### 存文件
```python
def save_accounts(accounts):
    dict_list = []
    for acc in accounts:
        dict_list.append(acc.to_dict())
    with open("accounts.json", "w", encoding="utf-8") as f:
        json.dump(dict_list, f, ensure_ascii=False, indent=4)
```

#### 读文件
```python
def load_accounts():
    try:
        with open("accounts.json", "r", encoding="utf-8") as f:
            dict_list = json.load(f)
    except FileNotFoundError:
        return []   # 第一次运行没有文件，直接返回空列表，不崩溃

    accounts = []
    for item in dict_list:
        accounts.append(BankAccount.from_dict(item))
    return accounts
```

#### 多账户管理
程序里只维护一个变量：accounts = []（列表）。

* 创建新账户：accounts.append(BankAccount(name, balance))。

* 登录账户：遍历列表，找 acc.owner == 输入的名字。

* 查看所有人：遍历列表，打印每个人的 acc.owner。

**查找写法：**
```python
found = None
for acc in accounts:
    if acc.owner == name:
        found = acc
        break
```

### 总结
今日的收获在于掌握了 Python 对象持久化的标准模式：通过定义序列化契约（to_dict / from_dict），结合 with 上下文管理器和 json 模块，构建了从内存对象到磁盘文件的可靠双向数据流。同时，通过明确的代码分层（UI 交互层、业务逻辑层、数据持久层），提升了程序的可维护性和扩展性