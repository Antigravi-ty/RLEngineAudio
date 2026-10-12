# Rocket League Engine Audio Simulator (高保真引擎声浪仿真套件)

基于 **Web Audio API**，全面还原 Rocket League 引擎声浪的三大独立底层声学仿真架构：
1. **Crankcase REV 颗粒循环引擎**（`crankcase_rev/index.html`）
2. **Batmobile 蝙蝠车 Wwise 双层混合容器引擎**（`wwise_motor/batmobile.html`）
3. **Iron Horse 蒸汽火车 Wwise 冲程节拍引擎**（`wwise_motor/train_voice.html`）

三个控制台完全物理级隔离，互不干扰，各自拥有独立的 HTML 页面与解包原声资产。

---

## 📂 仓库架构概览

```
RLEngineAudio/
├── index.html                       # 根目录全局导航门户
├── README.md
├── REVERSE_ENGINEERING_GUIDE.md    # 核心技术文档：逆向全景解析与声学建模实战指南
├── .gitignore
│
├── crankcase_rev/                   # 【体系 1】Crankcase REV 颗粒循环仿真
│   ├── index.html                   # 独立 REV 控制台 (常驻全切片零爆音矩阵架构)
│   ├── audio_human_voice/           # Human Voice (19切片 + 10s扫频 + 怠速循环 + 4放炮)
│   └── audio_hyundai_ioniq_6/       # Hyundai Ioniq 6 / Turtle Modem (40切片 + 7.4s扫频 + 怠速循环 + 5放炮)
│
└── wwise_motor/                     # 【体系 2】传统 Wwise 引擎体系
    ├── index.html                   # Wwise 模块独立入口
    ├── batmobile.html               # 蝙蝠车独立控制台 (CAkLayerCntr 连续交叉淡化轨)
    ├── train_voice.html             # 蒸汽火车独立控制台 (活塞冲程节奏 + 怠速气阀 + 上升沿放炮)
    ├── audio_batmobile/             # 蝙蝠车原声音频 (低速轨 + 高速喷气轨 + 减速排气轨 + 起步点火)
    └── audio_train_voice/           # 火车原声音频 (33冲程 + 8怠速气阀 + 5放炮 + 巡航Loop01)
```

---

## 🎮 三大独立控制台运行机制

### 1. Crankcase REV 控制台 (`crankcase_rev/index.html`)
- **零爆音颗粒循环矩阵**：所有切片常驻播放，运行中零节点创建与销毁。
- **声学同频对齐**：发声切片基频严格锁定物理转速，消除低频拍频。
- **双车型预设**：顶部一键切换 `Human Voice` 与 `Hyundai Ioniq 6`。

### 2. Batmobile 蝙蝠车控制台 (`wwise_motor/batmobile.html`)
- **Wwise 原生 `CAkLayerCntr` (ID: 36820665)**：
  - **Layer 0 (给油态)**：低速轨 `loop_low_mid.wav` 与高速喷气轨 `loop_cruise_high.wav` 平滑淡化。
  - **Layer 1 (滑行态)**：低速轨 `loop_low_mid.wav` 与减速排气呼啸 `decel_exhaust.wav` 交叉淡化。
- **起步爆燃**：静止踩油门瞬间触发 `ignition_launch.wav`。

### 3. Iron Horse 蒸汽火车控制台 (`wwise_motor/train_voice.html`)
- **怠速工况 (零油门/零车速)**：活塞冲程与背景巡航轨静音，定期自动触发 `steam_vents` (8 变体) 周期性蒸汽吐息。
- **起步上升沿放炮**：油门从 0 踩下的上升沿瞬间触发单次 `backfire_pops` (5 变体)。
- **行驶巡航背景**：有车速时启动 `speed_loop_01.wav` 背景巡航，音量与变调随速度提升。
- **活塞主冲程**：冲程 A → B → C 顺次交替触发，节拍节奏随车速由怠速 1.8 Hz 持续加速至高速 8.5 Hz。

---

## 🚀 启动与测试指南

1. **本地 HTTP 服务启动（推荐）**：
   ```bash
   python3 -m http.server 8080
   ```
   - 访问 `http://localhost:8080/` 打开主门户，直达三个控制台。
   - 访问 `http://localhost:8080/wwise_motor/train_voice.html` 体验蒸汽火车。
   - 访问 `http://localhost:8080/wwise_motor/batmobile.html` 体验蝙蝠车。
   - 访问 `http://localhost:8080/crankcase_rev/` 体验 Crankcase REV。

2. **本地直接双击打开**：
   所有 HTML 均内置本地文件夹上传兼容层，点击各页面的 **“📁 选入文件夹”** 按钮即可在浏览器内存中解码试听。
