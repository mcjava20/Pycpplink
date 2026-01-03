# PyCppLink

一个 Python 库，提供对高级 C++ 功能的直接访问，包括内存池、数据处理和错误处理。

## 特性

- **内存池**：基于栈的高效内存管理，支持零拷贝操作
- **高级处理器**：具有 C++ 后端的高性能数据处理
- **指针操作**：全面的指针操作，用于底层内存访问
- **错误处理**：全面的错误报告和详细的消息
- **跨平台**：支持 Windows、Linux 和 macOS

## 安装

### 从源代码安装

```bash
cd pycpplink
pip install .
```

### 开发模式

```bash
cd pycpplink
pip install -e .
```

## 使用方法

### 基础示例

```python
from pycpplink import MemoryPool, AdvancedProcessor, PyCPPError

try:
    # 创建内存池
    pool = MemoryPool()
    print(f"内存池已创建，剩余 {pool.get_remaining()} 字节")

    # 创建处理器
    processor = AdvancedProcessor(pool)

    # 初始化数据
    processor.init_data(10)
    print("数据已初始化，包含 10 个元素")

    # 处理数据
    processor.process_data(0, 100)
    processor.process_data(5, 500)

    # 获取数据
    value = processor.get_data(0)
    print(f"索引 0 的值: {value}")

    value = processor.get_data(5)
    print(f"索引 5 的值: {value}")

    # 重置内存池
    pool.reset()
    print("内存池已重置")

except PyCPPError as e:
    print(f"发生错误: {e}")
```

### 高级示例

```python
from pycpplink import MemoryPool, AdvancedProcessor, PyCPPError

def process_large_dataset():
    pool = MemoryPool()
    processor = AdvancedProcessor(pool)

    try:
        # 使用最大允许大小初始化
        processor.init_data(100)

        # 处理所有元素
        for i in range(100):
            processor.process_data(i, i * 10)

        # 验证数据
        for i in range(100):
            value = processor.get_data(i)
            assert value == i * 10, f"期望 {i * 10}，实际得到 {value}"

        print("所有数据处理成功！")

### 指针操作示例

```python
from pycpplink import MemoryPool, Pointer, PyCPPError

def pointer_operations():
    pool = MemoryPool()

    try:
        # 分配内存
        ptr = pool.allocate(64)
        print(f"已分配 {ptr.size} 字节")

        # 写入各种数据类型
        ptr.write_byte(0, 0x01)
        ptr.write_short(1, 0x0203)
        ptr.write_int(3, 0x04050607)
        ptr.write_long(7, 0x08090A0B0C0D0E0F)
        ptr.write_float(15, 3.14159)
        ptr.write_double(19, 2.71828)

        # 读回数据
        print(f"字节: {hex(ptr.read_byte(0))}")
        print(f"短整型: {hex(ptr.read_short(1))}")
        print(f"整型: {hex(ptr.read_int(3))}")
        print(f"长整型: {hex(ptr.read_long(7))}")
        print(f"浮点数: {ptr.read_float(15)}")
        print(f"双精度浮点数: {ptr.read_double(19)}")

        # 指针偏移
        offset_ptr = ptr.offset(10)
        print(f"从偏移处读取: {hex(offset_ptr.read_byte(0))}")

        # 内存复制
        ptr2 = pool.allocate(64)
        ptr.copy(ptr2, 32)
        print(f"复制的值: {hex(ptr2.read_int(3))}")

        # 内存填充
        ptr.fill(0xFF, 20)
        print(f"填充后的值: {hex(ptr.read_byte(5))}")

        # 内存清零
        ptr.zero()
        print(f"清零后的值: {hex(ptr.read_byte(0))}")

    except PyCPPError as e:
        print(f"发生错误: {e}")

if __name__ == "__main__":
    pointer_operations()
```

## API 参考

### MemoryPool（内存池）

#### 方法

- `allocate(size: int) -> Pointer`: 从内存池分配内存
- `reset()`: 重置内存池
- `get_remaining() -> int`: 获取内存池中剩余的字节数

### AdvancedProcessor（高级处理器）

#### 方法

- `init_data(length: int)`: 初始化数据数组（1-100 个元素）
- `process_data(index: int, value: int)`: 设置指定索引的值
- `get_data(index: int) -> int`: 获取指定索引的值
- `get_last_error() -> Tuple[int, str]`: 获取最后的错误代码和消息

### Pointer（指针）

#### 方法

**写入操作：**
- `write_byte(offset: int, value: int)`: 在指定偏移处写入字节（8位）
- `write_short(offset: int, value: int)`: 在指定偏移处写入短整型（16位）
- `write_int(offset: int, value: int)`: 在指定偏移处写入整型（32位）
- `write_long(offset: int, value: int)`: 在指定偏移处写入长整型（64位）
- `write_float(offset: int, value: float)`: 在指定偏移处写入浮点数（32位）
- `write_double(offset: int, value: float)`: 在指定偏移处写入双精度浮点数（64位）

**读取操作：**
- `read_byte(offset: int) -> int`: 从指定偏移处读取字节（8位）
- `read_short(offset: int) -> int`: 从指定偏移处读取短整型（16位）
- `read_int(offset: int) -> int`: 从指定偏移处读取整型（32位）
- `read_long(offset: int) -> int`: 从指定偏移处读取长整型（64位）
- `read_float(offset: int) -> float`: 从指定偏移处读取浮点数（32位）
- `read_double(offset: int) -> float`: 从指定偏移处读取双精度浮点数（64位）

**内存操作：**
- `offset(offset: int) -> Pointer`: 创建具有指定字节偏移的新指针
- `copy(dest: Pointer, size: int)`: 将内存复制到另一个指针
- `compare(other: Pointer, size: int) -> int`: 与另一个指针比较内存（返回 -1、0 或 1）
- `fill(value: int, size: int)`: 用字节值填充内存
- `zero()`: 将指针中的所有内存清零

**属性：**
- `address -> int`: 获取指针的内存地址
- `size -> int`: 获取已分配内存的大小

### PyCPPError（错误异常）

当 C++ 操作失败时抛出的异常。

#### 属性

- `code`: 错误代码（整数）
- `message`: 错误消息（字符串）

## 错误代码

- `0`: 成功
- `1`: 内存分配失败
- `2`: 无效参数
- `3`: 超出范围
- `4`: 内部错误

## 从源代码构建

该库在安装期间会自动编译 C++ 代码，无需预编译的二进制文件。

### 系统要求

- Python 3.7+
- C++ 编译器（Windows 上使用 MSVC，Linux 上使用 GCC，macOS 上使用 Clang）

### 构建命令

```bash
# 构建 wheel 包
python setup.py bdist_wheel

# 从 wheel 包安装
pip install dist/pycpplink-0.1.0-*.whl
```

## 许可证

MIT License

## 贡献

欢迎贡献！请随时提交 Pull Request。
