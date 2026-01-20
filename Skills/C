---
name: "avalonia-ui-optimizer"
version: "1.0.0"
author: "kongliuli"
language: "zh-CN"
summary: "面向 Avalonia 的界面优化与多平台调优技能：静态分析 XAML/ViewModel，提出跨平台兼容性、性能、DPI/缩放、触控和平台原生行为改进建议。"
tags:
  - "avalonia"
  - "ui"
  - "optimization"
  - "cross-platform"
  - "dpi"
description: >
  该技能对 Avalonia 项目的界面进行静态分析，重点提供跨平台（Windows / Linux / macOS）适配与性能调优建议。
  包括但不限于：高 DPI 支持、布局与重绘优化、资源与样式复用、输入与触控适配、平台特性（原生菜单、窗口装饰）以及打包/发布建议。
platforms:
  - "windows"
  - "linux"
  - "macos"
inputs:
  - name: "project_archive"
    type: "file"
    accept: [".zip", ".skill"]
    required: false
    description: "可选：包含 UI 相关文件的压缩包，若提供则对其中 XAML/C# 进行静态解析。压缩包根目录须含 SKILL.md（本文件）。"
file_requirements:
  - filename: "SKILL.md"
    location: "root"
    description: "技能元信息（本文件），必须位于压缩包根目录以便 Vibe 识别。"
  - optional:
      - "Views/**.xaml"
      - "ViewModels/**.cs"
      - "*.csproj"
      - "Assets/**"
behavior:
  - "读取并解析 SKILL.md（或仅以本技能说明运行）"
  - "若用户上传 project_archive：解析 XAML、ViewModel、csproj，识别跨平台风险点"
  - "生成针对各平台的改进建议（Windows / Linux / macOS）与优先级提示"
  - "可生成 XAML 片段、ViewModel 模板或补丁 ZIP（仅静态文件）以供下载/合并"
outputs:
  - "分析报告：项目结构、发现的跨平台问题、优先级与建议"
  - "改进片段：XAML、/styles（ResourceDictionary）、ViewModel 代码片段"
  - "平台差异清单与打包/发布建议（如 native menus、tray、签名、runtime identifers）"
limitations:
  - "仅做静态分析与建议，不会运行或自动编译项目"
  - "对高度自定义控件或第三方原生扩展的运行时问题，需要用户补充说明与测试"
examples:
  - "示例: 上传包含 MainWindow.xaml 与 MainWindowViewModel.cs 的 zip，技能返回高 DPI 调整建议与优化后的 XAML 片段。"
  - "示例: 请求 macOS 上窗口装饰替代方案，技能给出 WindowTransparency、TitleBarOverlay 等配置建议。"
---
