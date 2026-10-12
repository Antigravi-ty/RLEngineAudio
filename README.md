# Rocket League Engine Audio Simulator (双架构高保真物理仿真套件)

基于 **Web Audio API**，全面还原 Rocket League 引擎声浪的两大底层音频架构体系：
1. **Crankcase REV 颗粒循环引擎**（`crankcase_rev/`）
2. **传统 Wwise 多轨与冲程引擎**（`wwise_motor/`）

两套模型与页面代码物理级隔离，互不干扰，各自拥有独立的 HTML 控制台与专属解包原声音频资产。

---

## 📂 仓库架构概览

```
.
├── index.html                           # 根目录双引擎导航门户
├── README.md
│
├── crankcase_rev/                       # 【模块 1】Crankcase REV 颗粒循环仿真体系
│   ├── index.html                       # 独立 REV 控制台 (常驻零爆音颗粒矩阵架构)
│   ├── audio_human_voice/               # Human Voice 人声声浪包 (19 切片 + 4 放炮 + 怠速循环)
│   └── audio_hyundai_ioniq_6/           # Hyundai Ioniq 6 / Turtle Modem (40 切片 + 5 放炮 + 调制解调音)
│
└── wwise_motor/                         # 【模块 2】传统 Wwise 多轨与冲程仿真体系
    ├── index.html                       # 独立 Wwise 控制台 (多轨 Blend 容器 + 冲程节奏调度)
    ├── audio_batmobile/                 # 蝙蝠车 DarkCar (双层连续淡化轨 + 启动 Intro + 排气)
    └── audio_train_voice/               # 蒸汽火车 Iron Horse (33 冲程交替 + 8 泄气 + 5 鸣笛 + 5 放炮)
```

---

## 🏎️ 模块 1：Crankcase REV 颗粒循环引擎 (`crankcase_rev/`)

采用 **Crankcase Audio REV** 的微周期切片矩阵颗粒合成算法：
- **V4 常驻全切片循环矩阵 (Zero-Click Matrix)**：所有原声周期切片在引擎启动时一次性进入常驻循环，运行期间零节点创建、零销毁、零启动，彻底消灭 AudioBuffer 阶跃爆音。
- **声学绝对同频锁定 (Zero-Beat Frequency Alignment)**：所有发声切片基频严格锁定至实时目标物理频率 $F_{target}$，消除低频拍频干涉。
- **纯净怠速单播隔离**：怠速工况独占播放纯净原声循环，脱离怠速区间后平滑淡入切片矩阵。
- **包含车型**：
  - `audio_human_voice`：Rocket League 人声引擎 (SFX_REV_HumanVoice)
  - `audio_hyundai_ioniq_6`：Rocket League Turtle Modem 引擎 (SFX_REV_TurtleModem)

---

## 🦇 模块 2：传统 Wwise 多轨与冲程引擎 (`wwise_motor/`)

还原 **Audiokinetic Wwise** 原生 HIRC 容器行为：
- **Batmobile DarkCar (蝙蝠车喷气引擎)**：
  - 双层 `CAkLayerCntr` 连续交叉淡化轨（低中转速轨 + 巡航高转速喷气轨）
  - 瞬态起步点火音效（`ignition_launch.wav`）
  - 松油门尾段减速排气喷射（`decel_exhaust.wav`）
- **Iron Horse Vocal Engine (蒸汽火车声浪)**：
  - 基于真实转速/车速计算冲程频率（1.6 Hz ~ 8.5 Hz）
  - 3 组各 11 个原声冲程采样（`Group A -> B -> C`）轮替触发，带 Wwise 原生 `wAvoidRepeatCount: 1` 随机防重机制
  - 泄气声（Steam Vents）、鸣笛（Whistles）、回火放炮（Backfire Pops）与巡航背景音轨无缝混音。

---

## 🚀 启动与使用指南

1. **本地启动 HTTP 服务**（推荐，直接解决跨域资源加载）：
   ```bash
   python3 -m http.server 8080
   ```
2. 在浏览器中打开 `http://localhost:8080/`：
   - 点击 **进入 Crankcase REV 仿真控制台** 体验颗粒合成引擎。
   - 点击 **进入 Wwise Motor 仿真控制台** 体验传统多轨与冲程引擎。
3. **本地直接双击打开**：
   - 即使直接双击 `crankcase_rev/index.html` 或 `wwise_motor/index.html` 打开，页面均配备了专属的 **“📁 选入 audio 文件夹”** 按钮，选中对应车型的音频文件夹即可本地解码试听！

---

## 🎮 控制指南

- **W 键**：瞬时 100% 地板油加速
- **S 键**：瞬时 100% 刹车制动
- **线性滑块**：支持模拟手柄或脚踏板输入（持续维持任意油门/刹车深度）
- **Shift / Z 键**：锁挡保持，抑制升挡直冲红线
- **Space 键**：手刹制动
