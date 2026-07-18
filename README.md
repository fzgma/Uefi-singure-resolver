# UEFI Secure Boot Resolver

一个用于生成和解析 UEFI Secure Boot 签名数据库的 Python 小工具。

项目包含两个脚本：

- `create.py`：生成用于测试的模拟 UEFI 签名数据库文件
- `resolver.py`：解析 `PK`、`KEK`、`db`、`dbx` 文件并导出为 CSV

> 说明：这里生成的是模拟数据，主要用于学习、测试和验证解析流程，不是从真实固件导出的完整安全启动数据库。

## 功能

- 生成模拟的 `PK.bin`、`KEK.bin`、`db.bin`、`dbx.bin`
- 解析 UEFI `EFI_SIGNATURE_LIST` 结构
- 尝试把签名内容识别为 X.509 证书
- 导出证书主题、签发者、序列号、有效期等信息到 CSV
- 支持 `dbx` 中的 SHA256 黑名单条目解析

## 环境依赖

- Python 3.10+
- `cryptography`

安装依赖：

```bash
pip install -r requirements.txt
```

或者单独安装：

```bash
pip install cryptography
```

## 快速开始

### 1. 生成测试数据

运行：

```bash
python create.py
```

脚本会提示输入保存路径：

- 直接回车：保存到当前工作目录
- 输入目录：保存到指定目录

生成后会得到以下文件：

- `PK.bin`
- `KEK.bin`
- `db.bin`
- `dbx.bin`

### 2. 解析数据并导出 CSV

运行：

```bash
python resolver.py
```

脚本会提示输入包含上述文件的目录路径：

- 直接回车：使用当前目录
- 输入目录：从指定目录中查找文件

解析完成后会生成：

- `UEFI_SecureBoot_entries.csv`

## 输出说明

CSV 字段如下：

- `Store`：来源数据库名称，`PK` / `KEK` / `db` / `dbx`
- `SignatureType`：签名列表类型 GUID
- `OwnerGUID`：条目所有者 GUID
- `is_x509`：是否识别为 X.509 证书
- `subject`：证书主题
- `issuer`：证书签发者
- `serial_number`：证书序列号
- `not_before`：证书生效时间
- `not_after`：证书失效时间
- `data_length`：签名数据长度

## 文件格式说明

解析器处理的是 UEFI `EFI_SIGNATURE_LIST` 结构。当前实现支持两类常见内容：

- `EFI_CERT_X509_GUID`：X.509 证书
- `EFI_CERT_SHA256_GUID`：SHA256 哈希条目

当签名数据能被识别为 DER 编码证书时，脚本会提取证书信息；否则会保留原始条目元数据。

## 使用限制

- `create.py` 生成的是模拟数据，不保证与真实平台导出的内容完全一致
- `resolver.py` 使用的是前缀匹配查找文件名，因此目录里如果存在其他以 `PK`、`KEK`、`db`、`dbx` 开头的文件，也可能被选中
- 当前输出格式是 CSV，不是 Excel
- 项目没有内置单元测试

## 示例

生成测试文件：

```bash
python create.py
```

解析并导出结果：

```bash
python resolver.py
```

## 目录结构

```text
.
├── create.py
├── resolver.py
├── requirements.txt
├── README.md
└── LICENSE
```

## 许可证

见 `LICENSE` 文件。
