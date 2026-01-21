---
name: debug-config-expert
description: "C#/Python调试配置专家技能：负责launch.json/tasks.json配置、断点策略建议、日志级别管理、环境变量配置、多目标调试。使用时需要配置调试文件、建议断点策略、管理日志级别、配置环境变量、设置多目标调试。"
---

# debug-config-expert Skill

为C#/Python项目提供专业的调试配置和策略建议能力，确保开发人员能够高效地调试和解决代码问题。

## When to Use This Skill

Trigger when any of these applies:
- 需要配置VS Code的launch.json和tasks.json文件
- 需要建议断点设置策略
- 需要管理日志级别
- 需要配置调试环境变量
- 需要设置多目标调试
- 需要优化调试流程

## Not For / Boundaries

- 不负责具体代码的调试执行
- 不替代开发人员分析和解决代码问题
- 不处理特定调试器的高级配置（仅通用配置）
- 不负责调试器的安装和基础设置

## Quick Reference

### Common Patterns

**Pattern 1:** VS Code C# launch.json配置
```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": ".NET Core Launch (web)",
            "type": "coreclr",
            "request": "launch",
            "preLaunchTask": "build",
            "program": "${workspaceFolder}/bin/Debug/net6.0/YourProject.dll",
            "args": [],
            "cwd": "${workspaceFolder}",
            "stopAtEntry": false,
            "serverReadyAction": {
                "action": "openExternally",
                "pattern": "^\s*Now listening on:\s+(https?://\S+)",
                "uriFormat": "%s/swagger"
            },
            "env": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            },
            "sourceFileMap": {
                "/Views": "${workspaceFolder}/Views"
            }
        },
        {
            "name": ".NET Core Attach",
            "type": "coreclr",
            "request": "attach",
            "processId": "${command:pickProcess}"
        }
    ]
}
```

**Pattern 2:** VS Code Python launch.json配置
```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Python: Current File",
            "type": "python",
            "request": "launch",
            "program": "${file}",
            "console": "integratedTerminal",
            "justMyCode": true,
            "env": {
                "PYTHONPATH": "${workspaceFolder}",
                "DEBUG": "true"
            },
            "args": ["--arg1", "value1", "--arg2", "value2"]
        },
        {
            "name": "Python: Flask",
            "type": "python",
            "request": "launch",
            "module": "flask",
            "env": {
                "FLASK_APP": "app.py",
                "FLASK_ENV": "development",
                "FLASK_DEBUG": "1"
            },
            "args": [
                "run",
                "--no-debugger",
                "--no-reload"
            ],
            "jinja": true
        },
        {
            "name": "Python: Remote Attach",
            "type": "python",
            "request": "attach",
            "connect": {
                "host": "localhost",
                "port": 5678
            },
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "/app"
                }
            ]
        }
    ]
}
```

**Pattern 3:** VS Code tasks.json配置
```json
{
    "version": "2.0.0",
    "tasks": [
        {
            "label": "build",
            "command": "dotnet",
            "type": "process",
            "args": [
                "build",
                "${workspaceFolder}/YourProject.csproj",
                "/property:GenerateFullPaths=true",
                "/consoleloggerparameters:NoSummary"
            ],
            "problemMatcher": "$msCompile"
        },
        {
            "label": "test",
            "command": "dotnet",
            "type": "process",
            "args": [
                "test",
                "${workspaceFolder}/YourProject.Tests.csproj",
                "/property:GenerateFullPaths=true",
                "/consoleloggerparameters:NoSummary"
            ],
            "problemMatcher": "$msCompile"
        }
    ]
}
```

**Pattern 4:** 日志级别配置
```csharp
// C# - appsettings.Development.json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "Microsoft.AspNetCore": "Warning",
      "YourProject": "Trace"
    }
  }
}
```

```python
# Python - logging configuration
import logging

logging.basicConfig(
    level=logging.DEBUG,  # 可以设置为DEBUG, INFO, WARNING, ERROR, CRITICAL
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
    filename='app.log'
)

logger = logging.getLogger(__name__)
```

**Pattern 5:** 断点策略建议
1. **条件断点**：只在特定条件满足时触发
2. **日志断点**：记录信息但不中断执行
3. **命中计数断点**：在命中特定次数后触发
4. **异常断点**：在抛出异常时触发
5. **数据断点**：在数据值改变时触发
6. **函数断点**：在函数调用时触发

## Examples

### Example 1: 配置VS Code C#调试环境
- Input: 需要为一个C# ASP.NET Core项目配置VS Code调试环境
- Steps:
  1. 创建launch.json配置文件
  2. 添加.NET Core Launch (web)配置
  3. 设置program路径和工作目录
  4. 配置环境变量（如ASPNETCORE_ENVIRONMENT）
  5. 创建tasks.json配置文件，添加build任务
- Expected output / acceptance: 完整的launch.json和tasks.json配置，可以直接用于调试

### Example 2: 配置Python Flask应用调试
- Input: 需要为一个Python Flask应用配置VS Code调试环境
- Steps:
  1. 创建launch.json配置文件
  2. 添加Python: Flask配置
  3. 设置FLASK_APP、FLASK_ENV等环境变量
  4. 配置调试参数
  5. 确保可以在浏览器中访问应用
- Expected output / acceptance: 完整的launch.json配置，可以直接用于调试Flask应用

### Example 3: 建议断点策略
- Input: 需要为一个复杂的业务逻辑函数建议断点策略
- Steps:
  1. 分析函数的输入和输出
  2. 建议在函数入口添加日志断点
  3. 建议在关键条件分支添加条件断点
  4. 建议在循环和异常处理处添加断点
  5. 建议在函数返回前添加断点
- Expected output / acceptance: 详细的断点策略建议，包含断点类型和位置

## References

- `references/index.md`: 调试配置最佳实践导航
- `references/vs-code-launch-json.md`: VS Code launch.json配置指南
- `references/vs-code-tasks-json.md`: VS Code tasks.json配置指南
- `references/logging-config.md`: 日志级别配置指南
- `references/breakpoint-strategies.md`: 断点策略建议

## Maintenance

- Sources: VS Code官方文档和调试最佳实践
- Last updated: 2026-01-20
- Known limits: 不支持特定调试器的高级配置和特定IDE的专属配置