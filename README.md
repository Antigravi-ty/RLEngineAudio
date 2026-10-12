# Rocket League Engine Audio Simulator (Web Audio API)

基于 **Web Audio API** 与 **Crankcase REV / Wwise** 逆向工程实现的 Rocket League 发动机声浪高保真物理仿真器。

## 🌟 核心特性

- **V4 常驻全切片循环矩阵 (Zero-Click Matrix)**：所有原声周期切片在引擎启动时一次性进入常驻循环，运行期间零节点创建、零销毁、零启动，彻底消灭 AudioBuffer 阶跃爆音。
- **声学绝对同频锁定 (Zero-Beat Frequency Alignment)**：所有发声切片基频严格锁定至实时目标物理频率 $F_{target}$，消除低频拍频（Beat Frequency）干涉。
- **纯净怠速单播隔离**：怠速工况独占播放纯净原声循环，脱离怠速区间后平滑淡入切片矩阵。
- **多车型通用文件夹音效包载入**：支持点击“选择音效文件夹”直接导入包含 `metadata.json` 的任意车辆音效包，动态构建转速表与切片混音矩阵。
- **真实车辆动力学与变速箱高级调校**：
  - 红线断油触发转速（4500 ~ 6500 RPM 可调）
  - 断油回弹深度（Bounce Drop Range）与跳动周期（Hard Cut 哒哒哒 vs Soft Cut 绵密感）
  - 升挡转速回落目标（Upshift Landing RPM）
  - 挡位数无上限支持（支持 5 挡、6 挡、10 挡、20 挡及任意自定义挡位）
  - 高挡位指数级空气阻力时间衰减（满足长久巡航升挡挑战）
  - 飞轮转动惯量 / 转速跟手敏捷度调节
  - 丢油门回火放炮转速门槛检测（随机去重多选一放炮）

## 📂 车辆资产目录结构

每个车型独立为一个标准文件夹，均内嵌对应的 `metadata.json`：

```
├── index.html                           # 仿真器前端页面与 Web Audio 合成核心
│
├── audio_human_voice/                   # 【Human Voice】人声声浪包 (19 切片)
│   ├── metadata.json                    # 元数据 (48.0Hz ~ 132.6Hz, 900~6000 RPM)
│   ├── full/                            # 10s 加速扫频、减速扫频、4.8s 纯净怠速循环
│   ├── sfx/                             # 4 种去重回火放炮音效 (pop_01 ~ pop_04)
│   └── slices/                          # 19 个加速微周期切片 + 19 个减速反向切片
│
└── audio_hyundai_ioniq_6/               # 【Hyundai Ioniq 6 / Turtle Modem】电子声浪包 (40 切片)
    ├── metadata.json                    # 元数据 (37.65Hz ~ 74.65Hz, 900~6000 RPM)
    ├── full/                            # 7.4s 扫频、2.5s 怠速循环
    ├── sfx/                             # 5 种回火放炮音效 + 超音速突破 Modem 循环
    └── slices/                          # 40 个微周期切片 + 40 个减速反向切片
```

## 🎮 控制指南

- **选择车辆音效**：点击页面右上角“选择车辆音效文件夹”，选择 `audio_human_voice` 或 `audio_hyundai_ioniq_6` 即可一键载入！
- **W 键**：瞬时 100% 地板油加速
- **S 键**：瞬时 100% 刹车制动
- **单踏板滑块**：支持线性持久定速巡航（如维持 +20% 油门巡航或 -20% 制动）
- **Shift / Z 键**：锁挡保持（抑制自动升挡，直撞红线触发断油保护）
