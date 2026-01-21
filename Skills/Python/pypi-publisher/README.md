# Python PyPI发布助手

## 技能描述

**Python PyPI发布助手**是一个专为Python项目设计的专业技能，专注于提供高质量的打包和发布解决方案。该技能涵盖了setup.py/pyproject.toml配置、打包分发、版本号管理、README渲染和上传PyPI等核心领域，帮助开发人员将Python项目成功发布到PyPI，提高项目的可分发性和可访问性。

### 专注领域
- **项目配置**：配置setup.py或pyproject.toml文件
- **打包分发**：构建Python包并准备分发
- **版本号管理**：管理项目版本号，支持版本递增
- **README渲染**：确保README.md在PyPI上正确渲染
- **上传发布**：将包上传到PyPI或TestPyPI

## 目录结构

```plaintext
skills/Python/pypi-publisher/
├── SKILL.md          # 技能技术规范文件
├── README.md         # 中文文档
├── references/       # 参考资料
├── scripts/          # 相关脚本
└── assets/           # 资源文件
```

## 快速开始

### 基本使用

1. **配置项目文件**
   - 创建setup.py或pyproject.toml文件
   - 配置项目元数据和依赖

2. **准备项目文件**
   - 创建README.md文件
   - 创建LICENSE文件
   - 组织项目目录结构

3. **构建包**
   - 安装构建工具
   - 执行构建命令

4. **上传到PyPI**
   - 安装twine
   - 执行上传命令

5. **版本管理**
   - 管理项目版本号
   - 支持版本递增

## 使用示例

### 示例1：基本打包和发布

```bash
# 创建项目结构
mkdir -p example_package
cd example_package

# 创建__init__.py
echo "__version__ = \"0.1.0\"" > __init__.py

# 创建示例模块
echo "def hello():\n    return \"Hello from example package!\"" > hello.py

# 创建setup.py
cat > setup.py << 'EOF'
from setuptools import setup, find_packages

with open("README.md", "r", encoding="utf-8") as f:
    long_description = f.read()

setup(
    name="example-package-yourusername",
    version="0.1.0",
    author="Your Name",
    author_email="your.email@example.com",
    description="A small example package",
    long_description=long_description,
    long_description_content_type="text/markdown",
    url="https://github.com/yourusername/example-package",
    packages=find_packages(),
    classifiers=[
        "Programming Language :: Python :: 3",
        "License :: OSI Approved :: MIT License",
        "Operating System :: OS Independent",
    ],
    python_requires=">=3.6",
)
EOF

# 创建README.md
cat > README.md << 'EOF'
# Example Package

This is a simple example package.

## Installation

```bash
pip install example-package-yourusername
```

## Usage

```python
from example_package import hello

print(hello())  # Output: Hello from example package!
```
EOF

# 创建LICENSE
cat > LICENSE << 'EOF'
MIT License

Copyright (c) [2026] [Your Name]

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
EOF

# 构建包
pip install build
python -m build

# 上传到PyPI
pip install twine
twine upload dist/*
```

### 示例2：使用pyproject.toml

```bash
# 创建pyproject.toml
cat > pyproject.toml << 'EOF'
[build-system]
requires = ["setuptools>=42", "wheel"]
build-backend = "setuptools.build_meta"

[project]
name = "example-package-yourusername"
version = "0.1.0"
authors = [
    {
        name = "Your Name",
        email = "your.email@example.com"
    }
]
description = "A small example package"
readme = "README.md"
license = {
    file = "LICENSE"
}
classifiers = [
    "Programming Language :: Python :: 3",
    "License :: OSI Approved :: MIT License",
    "Operating System :: OS Independent",
]
requires-python = ">=3.6"

[project.urls]
"Homepage" = "https://github.com/yourusername/example-package"
"Bug Tracker" = "https://github.com/yourusername/example-package/issues"
EOF

# 构建和上传（同上例）
python -m build
twine upload dist/*
```

### 示例3：版本号管理

```python
# version.py
__version__ = "0.1.0"

# manage_version.py
import re

def get_version():
    """获取当前版本号"""
    with open("version.py", "r") as f:
        content = f.read()
    match = re.search(r"__version__\s*=\s*['\"]([^'\"]+)['\"]", content)
    if match:
        return match.group(1)
    return "0.0.0"

def bump_version(part="patch"):
    """递增版本号"""
    current_version = get_version()
    major, minor, patch = map(int, current_version.split("."))
    
    if part == "major":
        major += 1
        minor = 0
        patch = 0
    elif part == "minor":
        minor += 1
        patch = 0
    elif part == "patch":
        patch += 1
    
    new_version = f"{major}.{minor}.{patch}"
    
    # 更新version.py
    with open("version.py", "r") as f:
        content = f.read()
    
    new_content = re.sub(
        r"__version__\s*=\s*['\"]([^'\"]+)['\"]",
        f"__version__ = \"{new_version}\"",
        content
    )
    
    with open("version.py", "w") as f:
        f.write(new_content)
    
    print(f"版本号从 {current_version} 更新到 {new_version}")
    return new_version

# 使用示例
if __name__ == "__main__":
    bump_version("patch")
```

### 示例4：README渲染测试

```bash
# 测试README.md渲染
pip install readme_renderer
python -c "import readme_renderer.markdown; print(readme_renderer.markdown.render(open('README.md').read()))"

# 构建和上传
python -m build
twine upload dist/*
```

### 示例5：完整发布流程

```bash
# 递增版本号
python manage_version.py

# 运行测试
pytest

# 构建包
python -m build

# 上传到TestPyPI
twine upload --repository testpypi dist/*

# 测试安装
pip install --index-url https://test.pypi.org/simple/ example-package-yourusername

# 上传到PyPI
twine upload dist/*
```

## 最佳实践

1. **项目配置最佳实践**
   - 使用现代的pyproject.toml配置文件
   - 提供完整的项目元数据
   - 明确指定Python版本要求
   - 合理配置依赖项

2. **打包分发最佳实践**
   - 使用官方推荐的build工具
   - 构建源码分发包和wheel包
   - 清理旧的构建文件
   - 测试构建过程

3. **版本号管理最佳实践**
   - 使用语义化版本号（Semantic Versioning）
   - 从单独的版本文件读取版本号
   - 支持版本递增操作
   - 在发布前更新版本号

4. **README渲染最佳实践**
   - 使用标准的Markdown格式
   - 测试README.md在PyPI上的渲染效果
   - 包含完整的项目信息和使用说明
   - 使用适当的标题层级和格式

5. **上传发布最佳实践**
   - 先上传到TestPyPI进行测试
   - 使用twine工具上传包
   - 配置适当的上传认证
   - 保持发布过程的一致性

## 常见问题

### 1. 上传失败

**问题**：上传到PyPI时失败，提示认证错误。

**解决方案**：
- 确保已在PyPI注册账号
- 使用正确的用户名和密码
- 考虑使用API token进行认证
- 检查网络连接

### 2. README.md渲染问题

**问题**：README.md在PyPI上渲染不正确。

**解决方案**：
- 使用标准的Markdown格式
- 避免使用不支持的Markdown扩展
- 测试README.md的渲染效果
- 确保README.md使用UTF-8编码

### 3. 版本号冲突

**问题**：上传时提示版本号已存在。

**解决方案**：
- 使用新的版本号
- 遵循语义化版本号规范
- 在发布前检查PyPI上的版本

### 4. 依赖项问题

**问题**：安装包时依赖项解析失败。

**解决方案**：
- 明确指定依赖项版本范围
- 避免依赖项冲突
- 测试不同环境下的安装

### 5. 构建失败

**问题**：构建包时失败，提示缺少文件。

**解决方案**：
- 确保所有必要的文件都已包含
- 检查项目结构是否正确
- 确保setup.py或pyproject.toml配置正确

## 资源链接

- [Python打包用户指南](https://packaging.python.org/)
- [PyPI官方文档](https://pypi.org/help/)
- [setuptools文档](https://setuptools.pypa.io/en/latest/)
- [twine文档](https://twine.readthedocs.io/en/latest/)
- [语义化版本号规范](https://semver.org/lang/zh-CN/)
- [PyPI上传指南](https://packaging.python.org/tutorials/packaging-projects/)

## 总结

**Python PyPI发布助手**技能为Python项目提供了全面的打包和发布解决方案，涵盖了从项目配置、打包构建到版本管理和上传发布的各个方面。通过遵循本技能提供的最佳实践和示例代码，开发人员可以：

- 构建符合标准的Python包
- 成功发布包到PyPI
- 有效管理项目版本
- 确保包的质量和可访问性
- 提高项目的专业度和可信度

无论是构建新的Python库还是发布现有项目，**Python PyPI发布助手**技能都能为开发人员提供有力的支持，帮助他们将项目成功分享给全球Python社区。