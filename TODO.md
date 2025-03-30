# 思源笔记 Android 应用数据目录迁移 TODO

## 当前情况

- 目前思源笔记 Android 应用的数据存储在 `/storage/emulated/0/Android/data/org.b3log.siyuan` 目录下
- 这个路径在 Android 10+ 设备上对其他应用不可访问，属于应用私有目录

## 实现目标

- 允许用户将思源笔记的数据存储在更开放的位置，例如文档目录或下载目录
- 使其他应用能够读取和写入这些数据
- 提供简单的用户界面让用户选择数据存储位置

## 实现方案

采用 Storage Access Framework (SAF) 来实现，这样无需申请 `MANAGE_EXTERNAL_STORAGE` 权限（该权限在 Google Play 上受到严格限制）。

## 实施步骤

1. 修改 AndroidManifest.xml
   - 确保应用有必要的权限配置
   - 更新文件提供者路径配置

2. 创建工作区目录选择界面
   - 实现一个新的 Activity 用于引导用户选择工作区目录
   - 使用 ACTION_OPEN_DOCUMENT_TREE 意图让用户选择目录
   - 保存用户选择的目录 URI 到 SharedPreferences

3. 修改 workspaceBaseDir 设置逻辑
   - 更新 MainActivity.java 中 bootKernel() 方法，使用保存的目录 URI 而非默认的应用私有目录
   - 实现从 SAF URI 获取真实文件路径的工具方法

4. 添加数据迁移功能
   - 检测首次切换目录时的情况
   - 提供选项将现有数据迁移到新位置
   - 实现数据复制功能

5. 更新 FileProvider 配置
   - 修改 provider_paths.xml 确保能访问新的存储位置

6. 实现持久化权限管理
   - 确保应用保持对选定目录的读写权限
   - 处理权限失效的情况

7. 添加设置选项
   - 在应用设置中添加修改工作区目录的选项
   - 提供重置为默认目录的功能

8. 兼容性测试
   - 在不同 Android 版本上测试兼容性
   - 测试在应用更新后的数据访问是否正常

## 注意事项

- 使用 SAF 获取的 URI 格式与直接文件路径不同，需要适当转换
- 需要持久化储存 URI 权限，以便应用在重启后仍能访问选择的目录
- 在 Android 10 以上的设备上，应用私有目录对其他应用不可见，即使获得了读写外部存储的权限
