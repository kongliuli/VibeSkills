---
name: python-pypi-publisher
description: "Python PyPI发布助手技能：负责setup.py/pyproject.toml配置、打包分发、版本号管理、README渲染、上传PyPI。使用时需要配置项目文件、打包项目、管理版本号、确保README正确渲染、上传到PyPI。"
---

# python-pypi-publisher Skill

为Python项目提供专业的打包和发布能力，确保项目能够正确打包、版本管理和发布到PyPI，提高项目的可分发性和可访问性。

## When to Use This Skill

Trigger when any of these applies:
- 需要配置setup.py或pyproject.toml
- 需要打包Python项目
- 需要管理项目版本号
- 需要确保README正确渲染
- 需要上传项目到PyPI
- 需要优化现有发布流程

## Not For / Boundaries

- 不负责具体业务逻辑的实现
- 不替代开发人员进行项目设计决策
- 不处理项目的深度性能优化（仅发布层面）
- 不负责项目的安全审计

## Quick Reference

### Common Patterns

**Pattern 1:** setup.py配置
```python
# setup.py文件
from setuptools import setup, find_packages

with open("README.md", "r", encoding="utf-8") as f:
    long_description = f.read()

setup(
    name="example-package",
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
    install_requires=[
        "requests>=2.25.0",
    ],
    extras_require={
        "dev": [
            "pytest>=6.0",
            "black>=21.0",
            "mypy>=0.812",
        ],
    },
)
```

**Pattern 2:** pyproject.toml配置
```python
# pyproject.toml文件
'''
[build-system]
requires = ["setuptools>=42", "wheel"]
build-backend = "setuptools.build_meta"

[project]
name = "example-package"
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
dependencies = [
    "requests>=2.25.0",
]

[project.optional-dependencies]
dev = [
    "pytest>=6.0",
    "black>=21.0",
    "mypy>=0.812",
]

[project.urls]
"Homepage" = "https://github.com/yourusername/example-package"
"Bug Tracker" = "https://github.com/yourusername/example-package/issues"
'''
```

**Pattern 3:** 打包分发
```python
# 打包命令
'''
# 安装构建工具
pip install build

# 构建包
python -m build

# 安装twine
pip install twine

# 上传到PyPI
twine upload dist/*
'''

# 示例代码：打包分发工具类
import os
import subprocess
import shutil

class PackageManager:
    """打包分发工具类"""
    
    @staticmethod
    def clean_dist():
        """清理dist目录"""
        if os.path.exists("dist"):
            print("清理dist目录...")
            shutil.rmtree("dist")
        if os.path.exists("build"):
            print("清理build目录...")
            shutil.rmtree("build")
        if os.path.exists("*.egg-info"):
            print("清理egg-info目录...")
            for item in os.listdir("."):
                if item.endswith(".egg-info") and os.path.isdir(item):
                    shutil.rmtree(item)
    
    @staticmethod
    def build_package():
        """构建包"""
        print("构建包...")
        subprocess.run(["python", "-m", "build"], check=True)
        print("包构建成功")
    
    @staticmethod
    def upload_to_pypi(repository="pypi"):
        """上传到PyPI"""
        print(f"上传到{repository}...")
        subprocess.run(["twine", "upload", "dist/*"], check=True)
        print("上传成功")
    
    @staticmethod
    def upload_to_testpypi():
        """上传到TestPyPI"""
        print("上传到TestPyPI...")
        subprocess.run(["twine", "upload", "--repository", "testpypi", "dist/*"], check=True)
        print("上传到TestPyPI成功")
```

**Pattern 4:** 版本号管理
```python
# 版本号管理
# 方法1: 在setup.py中硬编码版本号
version="0.1.0"

# 方法2: 使用单独的版本文件
'''
# version.py
__version__ = "0.1.0"

# setup.py
from example_package import __version__ as version
'''

# 方法3: 使用 versioneer
'''
# 安装versioneer
pip install versioneer

# 初始化versioneer
versioneer install
'''

# 示例代码：版本号管理工具类
import os
import re

class VersionManager:
    """版本号管理工具类"""
    
    @staticmethod
    def get_version(version_file="version.py"):
        """从版本文件获取版本号"""
        if os.path.exists(version_file):
            with open(version_file, "r") as f:
                content = f.read()
            match = re.search(r"__version__\s*=\s*['\"]([^'\"]+)['\"]", content)
            if match:
                return match.group(1)
        return "0.0.0"
    
    @staticmethod
    def update_version(new_version, version_file="version.py"):
        """更新版本号"""
        if os.path.exists(version_file):
            with open(version_file, "r") as f:
                content = f.read()
            
            new_content = re.sub(
                r"__version__\s*=\s*['\"]([^'\"]+)['\"]",
                f"__version__ = \"{new_version}\"",
                content
            )
            
            with open(version_file, "w") as f:
                f.write(new_content)
            
            print(f"版本号更新为: {new_version}")
            return True
        return False
    
    @staticmethod
    def bump_version(part="patch", version_file="version.py"):
        """递增版本号"""
        current_version = VersionManager.get_version(version_file)
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
        return VersionManager.update_version(new_version, version_file)
```

**Pattern 5:** README渲染和上传PyPI
```python
# README.md渲染
'''
# 确保README.md使用正确的Markdown格式
# 测试README.md在PyPI上的渲染效果

# 安装readme_renderer
pip install readme_renderer

# 测试README.md
python -c "import readme_renderer.markdown; print(readme_renderer.markdown.render(open('README.md').read()))"
'''

# 示例代码：README渲染和上传工具类
import os
import subprocess

class ReadmeManager:
    """README渲染和上传工具类"""
    
    @staticmethod
    def test_readme():
        """测试README.md渲染"""
        print("测试README.md渲染...")
        try:
            # 安装readme_renderer
            subprocess.run(["pip", "install", "readme_renderer"], check=True)
            
            # 测试渲染
            test_code = '''
import readme_renderer.markdown
with open('README.md', 'r', encoding='utf-8') as f:
    content = f.read()
rendered = readme_renderer.markdown.render(content)
print('README.md渲染成功!' if rendered else 'README.md渲染失败!')
'''
            
            with open("test_readme.py", "w") as f:
                f.write(test_code)
            
            subprocess.run(["python", "test_readme.py"], check=True)
            print("README.md渲染测试通过")
            return True
        except Exception as e:
            print(f"README.md渲染测试失败: {e}")
            return False
        finally:
            if os.path.exists("test_readme.py"):
                os.remove("test_readme.py")
    
    @staticmethod
    def prepare_upload():
        """准备上传"""
        print("准备上传...")
        # 清理dist目录
        if os.path.exists("dist"):
            import shutil
            shutil.rmtree("dist")
        
        # 构建包
        subprocess.run(["python", "-m", "build"], check=True)
        print("构建完成，准备上传")
    
    @staticmethod
    def upload_with_credentials(username, password, repository="pypi"):
        """使用凭证上传"""
        print(f"上传到{repository}...")
        if repository == "testpypi":
            subprocess.run([
                "twine", "upload", "--repository", "testpypi",
                "--username", username, "--password", password,
                "dist/*"
            ], check=True)
        else:
            subprocess.run([
                "twine", "upload",
                "--username", username, "--password", password,
                "dist/*"
            ], check=True)
        print("上传成功")
```

## Examples

### Example 1: 基本打包和发布

**Scenario:** 创建一个简单的Python包并发布到PyPI。

**Steps:**
1. 创建项目结构
2. 编写setup.py或pyproject.toml
3. 编写README.md
4. 构建包
5. 上传到PyPI

**Code:**
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

**Expected Output:**
- 项目结构创建成功
- 包构建成功
- 包上传到PyPI成功

### Example 2: 使用pyproject.toml

**Scenario:** 使用现代的pyproject.toml配置文件。

**Steps:**
1. 创建项目结构
2. 编写pyproject.toml
3. 构建包
4. 上传到TestPyPI

**Code:**
```bash
# 创建项目结构
mkdir -p example_package
cd example_package

# 创建__init__.py
echo "__version__ = \"0.1.0\"" > __init__.py

# 创建示例模块
echo "def hello():\n    return \"Hello from example package!\"" > hello.py

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

# 创建README.md和LICENSE（同上例）

# 构建包
pip install build
python -m build

# 上传到TestPyPI
twine upload --repository testpypi dist/*
```

**Expected Output:**
- 项目结构创建成功
- 包构建成功
- 包上传到TestPyPI成功

### Example 3: 版本号管理

**Scenario:** 管理项目的版本号，支持版本递增。

**Steps:**
1. 创建版本文件
2. 编写版本管理脚本
3. 递增版本号
4. 构建和上传

**Code:**
```bash
# 创建版本文件
echo "__version__ = \"0.1.0\"" > version.py

# 创建版本管理脚本
cat > manage_version.py << 'EOF'
from version_manager import VersionManager

# 获取当前版本
current_version = VersionManager.get_version()
print(f"当前版本: {current_version}")

# 递增补丁版本
VersionManager.bump_version("patch")

# 获取新版本
new_version = VersionManager.get_version()
print(f"新版本: {new_version}")
EOF

# 运行版本管理脚本
python manage_version.py

# 构建和上传
python -m build
twine upload dist/*
```

**Expected Output:**
- 版本号递增成功
- 包构建成功
- 包上传成功

### Example 4: README渲染测试

**Scenario:** 测试README.md在PyPI上的渲染效果。

**Steps:**
1. 创建README.md
2. 测试渲染效果
3. 修复渲染问题
4. 构建和上传

**Code:**
```bash
# 创建README.md
cat > README.md << 'EOF'
# Example Package

This is a simple example package with **Markdown** formatting.

## Features

- Feature 1
- Feature 2
- Feature 3

## Installation

```bash
pip install example-package-yourusername
```

## Usage

```python
from example_package import hello

print(hello())
```

## License

MIT
EOF

# 测试README.md渲染
pip install readme_renderer
python -c "import readme_renderer.markdown; print(readme_renderer.markdown.render(open('README.md').read()))"

# 构建和上传
python -m build
twine upload dist/*
```

**Expected Output:**
- README.md渲染测试通过
- 包构建成功
- 包上传成功

### Example 5: 完整发布流程

**Scenario:** 执行完整的发布流程，包括版本递增、构建、测试和上传。

**Steps:**
1. 递增版本号
2. 运行测试
3. 构建包
4. 上传到TestPyPI
5. 测试安装
6. 上传到PyPI

**Code:**
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

**Expected Output:**
- 版本号递增成功
- 测试通过
- 包构建成功
- 包上传到TestPyPI成功
- 测试安装成功
- 包上传到PyPI成功

## References

- `references/index.md`: PyPI发布最佳实践导航
- `references/setup-configuration.md`: setup.py配置指南
- `references/pyproject-toml.md`: pyproject.toml配置指南
- `references/packaging.md`: 打包分发指南
- `references/version-management.md`: 版本号管理指南
- `references/readme-rendering.md`: README渲染指南

## Maintenance

- Sources: PyPI官方文档和Python打包最佳实践
- Last updated: 2026-01-21
- Known limits: 不负责项目的安全审计