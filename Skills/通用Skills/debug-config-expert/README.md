# 调试配置专家 (debug-config-expert)

为C#/Python项目提供专业的调试配置和策略建议能力，确保开发人员能够高效地调试和解决代码问题。

## 主要功能

- **launch.json/tasks.json配置**：配置VS Code的调试和任务文件
- **断点策略建议**：提供断点设置策略建议
- **日志级别管理**：配置和管理日志级别
- **环境变量配置**：配置调试环境变量
- **多目标调试**：设置和管理多目标调试

## 使用场景

- 需要配置VS Code的launch.json和tasks.json文件
- 需要建议断点设置策略
- 需要管理日志级别
- 需要配置调试环境变量
- 需要设置多目标调试
- 需要优化调试流程

## 快速开始

### VS Code C# launch.json配置
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
            "env": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            }
        }
    ]
}
```

### VS Code Python launch.json配置
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
            "env": {
                "PYTHONPATH": "${workspaceFolder}",
                "DEBUG": "true"
            }
        }
    ]
}
```

### 断点策略建议
1. **函数入口**：添加日志断点记录函数调用
2. **条件分支**：添加条件断点验证关键条件
3. **循环**：添加断点检查循环变量变化
4. **异常处理**：添加断点捕获异常情况
5. **数据修改**：添加数据断点监控关键数据变化

## 相关资源

- 详细说明：查看 `SKILL.md` 文件
- 参考文档：`references/` 目录下的详细文档
- 辅助脚本：`scripts/` 目录下的脚本工具
- 模板资源：`assets/` 目录下的模板文件