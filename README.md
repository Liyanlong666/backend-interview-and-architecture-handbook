# 后端八股与架构手册

> 📚 后端八股与架构手册 - Java 后端开发、面试八股文、技术深度学习笔记

## 📖 概览

这是一个基于 Obsidian 构建的技术知识库，主要聚焦于 **Java 后端开发**、**面试准备** 和 **数据库技术**。内容来源于高质量技术博客（如小林 coding、JavaGuide 等）的学习整理，以及个人实践总结。

## 📂 目录结构

### 🔥 核心内容：JAVA/

#### **八股文** (面试必备)
系统化的面试知识点整理，包含：

- **JUC (并发编程)** 
  - AQS、Java 内存模型 (JMM)
  - ThreadLocal、线程池、并发工具类
  - 各类锁的实现与原理
  
- **JVM**
  - JVM 调优实战
  - 垃圾收集算法与垃圾收集器
  - 类加载机制
  
- **MySQL**
  - 索引原理与优化
  - 事务、日志 (redo log、undo log、binlog)
  - SQL 优化技巧
  - MySQL 锁机制
  
- **Redis**
  - 底层数据结构 (SDS、跳表、ZipList 等)
  - 持久化机制 (RDB、AOF)
  - 缓存穿透/击穿/雪崩
  - Redis 分布式锁实现
  
- **计算机网络**
  - TCP 三次握手与四次挥手
  - HTTP/HTTPS 协议详解

#### **专题学习**

- **时序数据库 InfluxDB** (5 篇系列深度笔记)
  - 核心概念与数据模型
  - TSM 存储引擎深度解析
  - 时序压缩算法详解
  - 合并机制与性能测试
  - 选型对比与架构演进

- **Kafka** - 消息队列核心组件与机制

- **Algorithm-Go** - Go 语言刷算法笔记

- **非算法类手撕题** - 面试常见编码题整理

- **AI Coding Agent Prompt** - AI 辅助编程提示工程

### 🔖 其他内容

- **Clippings/** - 剪藏的优质技术文章
  - Gemini CLI 会话管理教程
  
- **gemini-scribe/** - Gemini 工具配置与 Prompt 模板
  - Agent Sessions 会话记录
  - Prompts 提示词库

## 🛠️ 使用方式

### 推荐工具
使用 [Obsidian](https://obsidian.md/) 打开本仓库以获得最佳体验：
- 双向链接、图谱视图
- Markdown 增强渲染
- Git 自动同步 (已配置 obsidian-git 插件)

### 命令行操作
配合 [obsidian-cli](https://github.com/Yakitrak/obsidian-cli) 进行快速操作：

```bash
# 设置默认 vault
obsidian-cli set-default "后端八股与架构手册"

# 搜索笔记
obsidian-cli search "Redis"
obsidian-cli search-content "分布式锁"

# 创建新笔记
obsidian-cli create "JAVA/新主题/笔记名" --content "..." --open
```

## 📌 笔记规范

- **Markdown 格式**：遵循 GitHub Flavored Markdown
- **图片存储**：每个笔记的图片资源存放在同名 `.assert` 文件夹中
- **版本控制**：使用 Git 管理，配合 obsidian-git 插件自动提交
- **引用来源**：笔记中标注参考来源（如小林 coding、JavaGuide 等）

## 🎯 适用场景

✅ Java 后端开发学习  
✅ 技术面试准备  
✅ 系统性知识梳理  
✅ 快速查阅技术要点  


---

> 💡 **提示**: 这是一个持续更新的知识库。学到新内容时，记得及时补充到相应主题下。
