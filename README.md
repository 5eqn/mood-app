# Moodlight

原生 Android 心情记录应用，Android 8.0+。版本 1.1 / versionCode 9。

## 使用

安装根目录 `Moodlight-r8-release.apk`。它使用与上一版相同的本地 debug 证书签署、启用 R8 和资源裁剪，可覆盖安装。正式分发签名请替换 app/build.gradle 中的 signingConfig。

- 此刻：单击 0–10 分；可选理由；保存后显示随心情变化的全屏动效。
- 看板：七天图表按时间定位，同一小时的记录取平均；无轴刻度。历史每批 40 条，用 SQLite 时间/id 游标分页，ListView 复用行。
- 设置：JSON 导出/合并导入。兼容旧版数组和新版 version/records 格式。重复 time/score/note 跳过；无效导入事务回滚。

数据读写在单线程 executor 中执行；绘图动画有界，完成后不持续刷新。图表最多 169 个小时桶。已有旧版 SharedPreferences 数据自动迁移。

## 构建

需要 Java 17、Gradle 8.9、Android SDK 34，Android Gradle Plugin 8.7.2。

```sh
ANDROID_HOME=/home/seqn/Android/Sdk /home/seqn/gradle-8.9/bin/gradle assembleRelease --offline --no-daemon
```

输出：app/build/outputs/apk/release/app-release.apk。

## 验证

2026-09-24 在本地 Android 14 模拟器执行：

- 安装最终 R8 release，验证 v2 签名；完整 assembleRelease（含 lintVital）成功。
- 1080×2400 / 420 dpi：三页视觉检查，选分、输入理由、保存、持久化、详情和导航。
- 720×1280 / 360 dpi，字体 130%：页面滚动、分数按钮、键盘、备注完成按钮检查。最终小屏截图 small-note-final.png。
- 实际拍摄低分波带、中分涟漪、高分光点/彩纸及页面切换动画，见 verification/mood-ux.mp4 与 save-*.png。
- 系统文件选择器导入 5,000 条测试记录，包含长中文备注；列表滚动越过 40 条分页边界至第 70–78 条，展示正确。
- 系统文件选择器导出 JSON，在宿主机逐条核对 5,000 条导入记录的时间、分数和完整备注，全部一致（连同之前记录共 5,002 条）。
- AndroidRuntime 日志无崩溃。尚未进行实体设备 GPU 性能基准测试。

验证数据只存在模拟器，APK 不内置测试记录。verification 下保留截图与录屏供审阅。
