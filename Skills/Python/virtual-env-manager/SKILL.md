---
name: python-virtual-env-manager
description: "Python虚拟环境管家技能：负责venv/conda环境管理、requirements.txt维护、依赖锁定(pip freeze)、环境隔离、多Python版本管理。使用时需要配置虚拟环境、管理依赖项、锁定依赖版本、确保环境隔离、管理多Python版本。"
---

# python-virtual-env-manager Skill

为Python项目提供专业的虚拟环境和依赖管理能力，确保项目能够在隔离、可控的环境中运行，提高开发效率和项目稳定性。

## When to Use This Skill

Trigger when any of these applies:
- 需要创建和管理venv/conda虚拟环境
- 需要维护requirements.txt文件
- 需要锁定依赖版本(pip freeze)
- 需要确保环境隔离
- 需要管理多Python版本
- 需要优化依赖项管理流程

## Not For / Boundaries

- 不负责具体业务逻辑的实现
- 不替代开发人员进行依赖项版本选择
- 不处理依赖项的深度性能优化（仅管理层面）
- 不负责依赖项的安全审计

## Quick Reference

### Common Patterns

**Pattern 1:** venv虚拟环境管理
```python
# 创建和激活venv虚拟环境
# Windows
python -m venv venv
venv\Scripts\activate

# macOS/Linux
python3 -m venv venv
source venv/bin/activate

# 示例代码：虚拟环境管理工具类
import os
import subprocess
import sys

class VenvManager:
    """虚拟环境管理工具类"""
    
    @staticmethod
    def create_venv(venv_path="venv"):
        """创建虚拟环境"""
        print(f"创建虚拟环境: {venv_path}")
        subprocess.run([sys.executable, "-m", "venv", venv_path], check=True)
        return venv_path
    
    @staticmethod
    def get_activate_script(venv_path="venv"):
        """获取激活脚本路径"""
        if os.name == "nt":  # Windows
            return os.path.join(venv_path, "Scripts", "activate")
        else:  # macOS/Linux
            return os.path.join(venv_path, "bin", "activate")
    
    @staticmethod
    def install_package(package, venv_path="venv"):
        """在虚拟环境中安装包"""
        pip_path = VenvManager._get_pip_path(venv_path)
        print(f"安装包: {package}")
        subprocess.run([pip_path, "install", package], check=True)
    
    @staticmethod
    def uninstall_package(package, venv_path="venv"):
        """在虚拟环境中卸载包"""
        pip_path = VenvManager._get_pip_path(venv_path)
        print(f"卸载包: {package}")
        subprocess.run([pip_path, "uninstall", "-y", package], check=True)
    
    @staticmethod
    def _get_pip_path(venv_path="venv"):
        """获取pip路径"""
        if os.name == "nt":  # Windows
            return os.path.join(venv_path, "Scripts", "pip.exe")
        else:  # macOS/Linux
            return os.path.join(venv_path, "bin", "pip")
```

**Pattern 2:** conda环境管理
```python
# 创建和激活conda环境
conda create -n myenv python=3.10
conda activate myenv

# 示例代码：conda环境管理工具类
import subprocess

class CondaManager:
    """conda环境管理工具类"""
    
    @staticmethod
    def create_env(env_name, python_version="3.10"):
        """创建conda环境"""
        print(f"创建conda环境: {env_name}, Python版本: {python_version}")
        subprocess.run(["conda", "create", "-n", env_name, f"python={python_version}", "-y"], check=True)
        return env_name
    
    @staticmethod
    def activate_env(env_name):
        """激活conda环境（注意：在脚本中激活需要特殊处理）"""
        print(f"激活conda环境: {env_name}")
        # 注意：在脚本中激活conda环境需要特殊处理
        # 这里仅返回激活命令
        return f"conda activate {env_name}"
    
    @staticmethod
    def install_package(package, env_name):
        """在conda环境中安装包"""
        print(f"在环境 {env_name} 中安装包: {package}")
        subprocess.run(["conda", "install", "-n", env_name, package, "-y"], check=True)
    
    @staticmethod
    def list_envs():
        """列出所有conda环境"""
        print("列出所有conda环境:")
        result = subprocess.run(["conda", "env", "list"], capture_output=True, text=True)
        print(result.stdout)
    
    @staticmethod
    def remove_env(env_name):
        """删除conda环境"""
        print(f"删除conda环境: {env_name}")
        subprocess.run(["conda", "remove", "-n", env_name, "--all", "-y"], check=True)
```

**Pattern 3:** requirements.txt维护
```python
# 生成requirements.txt文件
pip freeze > requirements.txt

# 安装requirements.txt中的依赖
pip install -r requirements.txt

# 示例代码：requirements.txt管理工具类
import os
import subprocess

class RequirementsManager:
    """requirements.txt管理工具类"""
    
    @staticmethod
    def generate_requirements(output_file="requirements.txt", pip_path="pip"):
        """生成requirements.txt文件"""
        print(f"生成requirements.txt文件: {output_file}")
        with open(output_file, "w") as f:
            subprocess.run([pip_path, "freeze"], stdout=f, check=True)
        print(f"requirements.txt文件生成成功: {output_file}")
    
    @staticmethod
    def install_requirements(requirements_file="requirements.txt", pip_path="pip"):
        """安装requirements.txt中的依赖"""
        if not os.path.exists(requirements_file):
            raise FileNotFoundError(f"requirements.txt文件不存在: {requirements_file}")
        
        print(f"安装requirements.txt中的依赖: {requirements_file}")
        subprocess.run([pip_path, "install", "-r", requirements_file], check=True)
        print("依赖安装成功")
    
    @staticmethod
    def update_requirement(package, version=None, requirements_file="requirements.txt"):
        """更新requirements.txt中的依赖版本"""
        if not os.path.exists(requirements_file):
            raise FileNotFoundError(f"requirements.txt文件不存在: {requirements_file}")
        
        # 读取当前requirements.txt
        with open(requirements_file, "r") as f:
            lines = f.readlines()
        
        # 更新或添加依赖
        updated_lines = []
        found = False
        
        for line in lines:
            if line.startswith(f"{package}=="):
                if version:
                    updated_lines.append(f"{package}=={version}\n")
                else:
                    updated_lines.append(line)
                found = True
            else:
                updated_lines.append(line)
        
        if not found:
            if version:
                updated_lines.append(f"{package}=={version}\n")
            else:
                updated_lines.append(f"{package}\n")
        
        # 写回requirements.txt
        with open(requirements_file, "w") as f:
            f.writelines(updated_lines)
        
        print(f"依赖 {package} 更新成功")
```

**Pattern 4:** 依赖锁定和环境隔离
```python
# 锁定依赖版本
pip freeze > requirements.txt

# 使用pip-tools进行依赖管理
pip install pip-tools
pip-compile requirements.in > requirements.txt
pip-sync requirements.txt

# 示例代码：依赖锁定和环境隔离工具类
import os
import subprocess
import sys

class DependencyManager:
    """依赖锁定和环境隔离工具类"""
    
    @staticmethod
    def lock_dependencies(output_file="requirements.txt", venv_path="venv"):
        """锁定依赖版本"""
        pip_path = DependencyManager._get_pip_path(venv_path)
        print(f"锁定依赖版本到: {output_file}")
        
        with open(output_file, "w") as f:
            subprocess.run([pip_path, "freeze"], stdout=f, check=True)
        
        print(f"依赖版本锁定成功: {output_file}")
    
    @staticmethod
    def ensure_isolation(venv_path="venv"):
        """确保环境隔离"""
        if not os.path.exists(venv_path):
            print(f"虚拟环境不存在，创建中: {venv_path}")
            subprocess.run([sys.executable, "-m", "venv", venv_path], check=True)
        
        # 检查是否在虚拟环境中运行
        if hasattr(sys, 'base_prefix') and sys.base_prefix != sys.prefix:
            print("当前已在虚拟环境中运行")
            return True
        else:
            print("警告: 当前不在虚拟环境中运行")
            return False
    
    @staticmethod
    def _get_pip_path(venv_path="venv"):
        """获取pip路径"""
        if os.name == "nt":  # Windows
            return os.path.join(venv_path, "Scripts", "pip.exe")
        else:  # macOS/Linux
            return os.path.join(venv_path, "bin", "pip")
    
    @staticmethod
    def clean_environment(venv_path="venv"):
        """清理虚拟环境"""
        if os.path.exists(venv_path):
            print(f"清理虚拟环境: {venv_path}")
            import shutil
            shutil.rmtree(venv_path)
            print(f"虚拟环境已清理: {venv_path}")
        else:
            print(f"虚拟环境不存在: {venv_path}")
```

**Pattern 5:** 多Python版本管理
```python
# 使用pyenv管理多Python版本（macOS/Linux）
pyenv install 3.8.10
pyenv install 3.9.10
pyenv install 3.10.0
pyenv global 3.10.0

# 示例代码：多Python版本管理工具类
import subprocess
import sys

class PythonVersionManager:
    """多Python版本管理工具类"""
    
    @staticmethod
    def get_current_version():
        """获取当前Python版本"""
        version = f"{sys.version_info.major}.{sys.version_info.minor}.{sys.version_info.micro}"
        print(f"当前Python版本: {version}")
        return version
    
    @staticmethod
    def list_available_versions():
        """列出可用的Python版本（使用pyenv）"""
        try:
            print("列出可用的Python版本:")
            result = subprocess.run(["pyenv", "versions"], capture_output=True, text=True)
            print(result.stdout)
        except FileNotFoundError:
            print("警告: pyenv未安装，无法列出版本")
    
    @staticmethod
    def install_version(version):
        """安装指定版本的Python（使用pyenv）"""
        try:
            print(f"安装Python版本: {version}")
            subprocess.run(["pyenv", "install", version], check=True)
            print(f"Python版本 {version} 安装成功")
        except FileNotFoundError:
            print("错误: pyenv未安装，无法安装版本")
        except subprocess.CalledProcessError:
            print(f"错误: 安装Python版本 {version} 失败")
    
    @staticmethod
    def set_global_version(version):
        """设置全局Python版本（使用pyenv）"""
        try:
            print(f"设置全局Python版本: {version}")
            subprocess.run(["pyenv", "global", version], check=True)
            print(f"全局Python版本已设置为: {version}")
        except FileNotFoundError:
            print("错误: pyenv未安装，无法设置版本")
        except subprocess.CalledProcessError:
            print(f"错误: 设置全局Python版本 {version} 失败")
    
    @staticmethod
    def set_local_version(version):
        """设置本地Python版本（使用pyenv）"""
        try:
            print(f"设置本地Python版本: {version}")
            subprocess.run(["pyenv", "local", version], check=True)
            print(f"本地Python版本已设置为: {version}")
        except FileNotFoundError:
            print("错误: pyenv未安装，无法设置版本")
        except subprocess.CalledProcessError:
            print(f"错误: 设置本地Python版本 {version} 失败")
```

## Examples

### Example 1: 基本虚拟环境管理

**Scenario:** 创建一个新的Python项目，需要使用虚拟环境隔离依赖。

**Steps:**
1. 创建项目目录
2. 创建虚拟环境
3. 激活虚拟环境
4. 安装依赖
5. 锁定依赖版本

**Code:**
```bash
# 创建项目目录
mkdir myproject
cd myproject

# 创建虚拟环境
python -m venv venv

# 激活虚拟环境
# Windows
venv\Scripts\activate
# macOS/Linux
source venv/bin/activate

# 安装依赖
pip install requests flask

# 锁定依赖版本
pip freeze > requirements.txt
```

**Expected Output:**
- 虚拟环境创建成功
- 依赖安装成功
- requirements.txt文件生成，包含所有依赖及其版本

### Example 2: 依赖项管理

**Scenario:** 项目需要更新依赖项版本。

**Steps:**
1. 激活虚拟环境
2. 更新依赖项
3. 重新锁定依赖版本
4. 安装更新后的依赖

**Code:**
```bash
# 激活虚拟环境
# Windows
venv\Scripts\activate
# macOS/Linux
source venv/bin/activate

# 更新依赖项
pip install --upgrade requests

# 重新锁定依赖版本
pip freeze > requirements.txt

# 安装更新后的依赖（在其他环境中）
pip install -r requirements.txt
```

**Expected Output:**
- 依赖项更新成功
- requirements.txt文件更新，包含最新版本的依赖
- 其他环境中依赖安装成功

### Example 3: conda环境管理

**Scenario:** 项目需要使用conda管理环境，并且需要特定版本的Python。

**Steps:**
1. 创建conda环境，指定Python版本
2. 激活conda环境
3. 安装依赖
4. 列出环境

**Code:**
```bash
# 创建conda环境，指定Python版本
conda create -n myenv python=3.10

# 激活conda环境
conda activate myenv

# 安装依赖
conda install numpy pandas

# 列出环境
conda env list
```

**Expected Output:**
- conda环境创建成功
- 依赖安装成功
- 环境列表显示新创建的环境

### Example 4: 多Python版本管理

**Scenario:** 项目需要在不同Python版本下测试。

**Steps:**
1. 安装pyenv
2. 安装多个Python版本
3. 为项目设置不同的Python版本
4. 测试项目

**Code:**
```bash
# 安装pyenv（macOS/Linux）
# 使用homebrew安装
brew install pyenv

# 或使用curl安装
curl https://pyenv.run | bash

# 安装多个Python版本
pyenv install 3.8.10
pyenv install 3.9.10
pyenv install 3.10.0

# 为项目设置Python版本
cd myproject
pyenv local 3.10.0

# 验证Python版本
python --version
```

**Expected Output:**
- Python版本安装成功
- 项目目录下的Python版本设置成功
- 验证显示正确的Python版本

### Example 5: 复杂依赖管理

**Scenario:** 项目需要管理复杂的依赖关系，使用pip-tools进行依赖管理。

**Steps:**
1. 创建requirements.in文件
2. 使用pip-compile生成requirements.txt
3. 使用pip-sync安装依赖
4. 更新依赖

**Code:**
```bash
# 安装pip-tools
pip install pip-tools

# 创建requirements.in文件
# 内容示例：
# requests
# flask

# 生成requirements.txt
pip-compile requirements.in > requirements.txt

# 安装依赖
pip-sync requirements.txt

# 更新依赖
# 修改requirements.in后重新生成
pip-compile requirements.in > requirements.txt
pip-sync requirements.txt
```

**Expected Output:**
- pip-tools安装成功
- requirements.txt生成成功，包含所有依赖及其版本
- 依赖安装成功
- 依赖更新成功

## References

- `references/index.md`: 虚拟环境管理最佳实践导航
- `references/venv-management.md`: venv虚拟环境管理指南
- `references/conda-management.md`: conda环境管理指南
- `references/requirements-management.md`: requirements.txt管理指南
- `references/dependency-locking.md`: 依赖锁定指南
- `references/python-version-management.md`: 多Python版本管理指南

## Maintenance

- Sources: Python官方文档和虚拟环境管理最佳实践
- Last updated: 2026-01-21
- Known limits: 不负责依赖项的安全审计