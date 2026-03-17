# 小兔子数学乐园 — 开发文档

## 1. 技术栈

| 类别 | 技术 |
|------|------|
| 结构 | HTML5 |
| 样式 | CSS3 (Flexbox, Grid, Animations, CSS Variables) |
| 逻辑 | Vanilla JavaScript (ES6+) |
| 音频 | Web Audio API + Web Speech Synthesis API |
| 图形 | CSS 绘制 + SVG (进度环) |
| 部署 | 单 HTML 文件，浏览器直接打开 |

**无外部依赖**。所有代码内联在一个 HTML 文件中。

---

## 2. 文件结构

```
math_game/
├── index.html          # 游戏主文件（唯一的代码文件）
└── docs/
    ├── PRD.md          # 产品需求文档
    ├── DEV.md          # 开发文档（本文件）
    └── TEST.md         # 测试文档
```

---

## 3. HTML 页面结构

页面包含 5 个顶层 Screen，通过 `.active` 类控制显示：

```html
<body>
  <div id="screen-start" class="screen active">   <!-- 开始/难度选择 -->
  <div id="screen-game" class="screen">            <!-- 答题主界面 -->
  <div id="screen-level-complete" class="screen">  <!-- 关卡结算 -->
  <div id="screen-game-complete" class="screen">   <!-- 全部完成 -->
  <div id="screen-rest" class="screen">            <!-- 休息页 -->
</body>
```

### 3.1 Screen: 开始页 (`#screen-start`)
```
┌──────────────────────────┐
│       🐰 小兔子数学乐园    │
│                          │
│    [可爱的小兔子插画]      │
│                          │
│   ┌──────────────────┐   │
│   │ 20以内加减法       │   │
│   └──────────────────┘   │
│   ┌──────────────────┐   │
│   │ 50以内加减法       │   │
│   └──────────────────┘   │
│   ┌──────────────────┐   │
│   │ 100以内加减法      │   │
│   └──────────────────┘   │
└──────────────────────────┘
```

### 3.2 Screen: 游戏页 (`#screen-game`)
```
┌──────────────────────────┐
│ 第1关  [🔊]   ⭕3/20  ⏰8s │  ← 顶栏
│                          │
│      10 + 5 - 3 = ?     │  ← 题目区
│                          │
│         [ 12 ]           │  ← 答案显示区
│                          │
│      🐰 (小兔子角色)      │  ← 角色区
│                          │
│   ┌───┬───┬───┐          │
│   │ 1 │ 2 │ 3 │          │
│   ├───┼───┼───┤          │  ← 数字键盘
│   │ 4 │ 5 │ 6 │          │
│   ├───┼───┼───┤          │
│   │ 7 │ 8 │ 9 │          │
│   ├───┼───┼───┤          │
│   │ ⌫ │ 0 │ ✓ │          │
│   └───┴───┴───┘          │
└──────────────────────────┘
```

### 3.3 Screen: 关卡结算 (`#screen-level-complete`)
```
┌──────────────────────────┐
│       🎉 第1关通       │
│                          │
│     🐰 (庆祝动画)       │
│                          │
│   正确率: 85%            │
│   平均用时: 4.2秒        │
│                          │
│   ┌──────────────────┐   │
│   │   继续下一关       │   │
│   └──────────────────┘   │
│   ┌──────────────────┐   │
│   │   休息一下         │   │
│   └──────────────────┘   │
└──────────────────────────┘
```

### 3.4 Screen: 全部完成 (`#screen-game-complete`)
```
┌──────────────────────────┐
│     🎊 全部通关！         │
│                          │
│     🐰 (超级庆祝)       │
│                          │
│   总正确率: 90%          │
│   总用时: 15分30秒       │
│   每关详情...            │
│                          │
│   ┌──────────────────┐   │
│   │    再玩一次        │   │
│   └──────────────────┘   │
└──────────────────────────┘
```

### 3.5 Screen: 休息页 (`#screen-rest`)
```
┌──────────────────────────┐
│                          │
│    🐰 (打盹的小兔子)     │
│                          │
│     休息一下吧～         │
│                          │
│   ┌──────────────────┐   │
│   │    准备好了！       │   │
│   └──────────────────┘   │
└──────────────────────────┘
```

---

## 4. CSS 架构

### 4.1 CSS Variables
```css
:root {
  --bg-primary: #FFF5F5;
  --bg-secondary: #FFF0E6;
  --color-pink: #FF8AAE;
  --color-mint: #7DDCBA;
  --color-yellow: #FFD93D;
  --color-purple: #C9A5F7;
  --color-correct: #4CAF50;
  --color-wrong: #FF6B6B;
  --color-text: #5D4037;
  --radius: 16px;
  --radius-lg: 24px;
}
```

### 4.2 响应式断点
| 断点 | 目标设备 | 调整 |
|------|---------|------|
| 默认 (≤599px) | 手机 | 全宽布局，键盘按钮 56px |
| ≥600px | 平板竖屏 | 居中 max-width 500px，键盘按钮 64px |
| ≥900px | 平板横屏 | 居中 max-width 600px |

### 4.3 动画系统
| 动画名 | 用途 | 时长 |
|--------|------|------|
| `bounce` | 兔子答对跳跃 | 0.5s |
| `shake` | 答错时题目抖动 | 0.4s |
| `fadeInUp` | 新题目出现 | 0.3s |
| `confetti-fall` | 彩纸飘落 | 1.5s |
| `pulse` | 倒计时最后3秒闪烁 | 0.5s infinite |
| `wiggle` | 兔子答错时摇摆鼓励 | 0.6s |
| `float` | 兔子空闲浮动 | 3s infinite |
| `celebrate` | 兔子关卡完成跳舞 | 1s infinite |
| `blink` | 输入区光标闪烁 | 1s infinite |

### 4.4 小兔子 CSS 结构
```
.bunny
├── .bunny-ear.left / .bunny-ear.right
├── .bunny-head  (包裹脸部，与身体通过重叠实现一体化连接)
│   ├── .bunny-face
│   │   ├── .bunny-eye.left / .bunny-eye.right
│   │   ├── .bunny-nose
│   │   ├── .bunny-mouth
│   │   └── .bunny-cheek.left / .bunny-cheek.right
└── .bunny-body  (上部与头部重叠，消除间隙)
    └── .bunny-arm.left / .bunny-arm.right
```

**关键设计**：头部(.bunny-head)和身体(.bunny-body)通过 z-index 层叠 + 位置重叠实现无缝连接，避免视觉上的断裂感。

状态类：`.bunny--idle` / `.bunny--happy` / `.bunny--sad` / `.bunny--celebrate` / `.bunny--sleep`

---

## 5. JavaScript 模块设计

### 5.1 模块总览

```
GameApp (IIFE)
├── GameState          // 游戏状态数据
├── QuestionGenerator  // 题目生成
├── ScreenManager      // 页面切换
├── UIRenderer         // DOM 更新
├── TimerController    // 倒计时控制
├── InputHandler       // 键盘输入处理
├── AudioManager       // 音效和语音
│   ├── SFXManager     // Web Audio 音效
│   ├── SpeechManager  // TTS 语音
│   └── BGMManager     // 背景音乐
└── GameController     // 主控制器/流程编排
```

### 5.2 GameState

```javascript
const GameState = {
  mode: 'within20',
  totalLevels: 4,
  questionsPerLevel: 20,
  timePerQuestion: 15,

  currentScreen: 'start',
  currentLevel: 1,
  currentQuestion: 0,
  questions: [],
  currentInput: '',
  isRetry: false,
  timerRemaining: 10,
  timerInterval: null,
  questionStartTime: null,

  levelStats: { correct: 0, wrong: 0, totalTime: 0, retries: 0 },
  overallStats: { levels: [] },

  audioEnabled: true,
  bgMusicPlaying: false
};
```

### 5.3 QuestionGenerator — 题目生成算法

#### 简单加法
```javascript
function generateAddition() {
  const max = this.getMax();
  const answer = randomInt(6, max);   // 保证有足够空间使两个操作数都 ≥ 3
  const a = randomInt(3, answer - 3);
  const b = answer - a;
  return { operands: [a, b], operators: ['+'], answer };
}
```
- 保证 a ≥ 3, b ≥ 3（过滤掉 2+1、3+3 等过于简单的题目）

#### 简单减法
```javascript
function generateSubtraction() {
  const max = this.getMax();
  const a = randomInt(5, max);
  const b = randomInt(2, a - 1);   // b ≥ 2 且结果 ≥ 1
  const answer = a - b;
  return { operands: [a, b], operators: ['-'], answer };
}
```
- 保证被减数 ≥ 5，减数 ≥ 2（不出 2-1、5-1 这类极简题目）

#### 进位加法 / 退位减法
- 进位加法：保证 `a%10 + b%10 ≥ 10`，操作数均 ≥ 3
- 退位减法：保证 `a%10 < b%10`，被减数 ≥ 11，减数 ≥ 2

#### 连加连减
```javascript
function generateChained() {
  const ops = [random < 0.5 ? '+' : '-', random < 0.5 ? '+' : '-'];
  let a, b, c, mid, answer;

  // 循环生成直到满足约束
  while (true) {
    a = randomInt(1, 18);

    if (ops[0] === '+') {
      b = randomInt(1, 20 - a);
      mid = a + b;
    } else {
      b = randomInt(1, a);
      mid = a - b;
    }

    if (ops[1] === '+') {
      if (20 - mid < 1) continue;
      c = randomInt(1, 20 - mid);
      answer = mid + c;
    } else {
      if (mid < 1) continue;
      c = randomInt(1, mid);
      answer = mid - c;
    }

    break;
  }
  return { operands: [a, b, c], operators: ops, answer };
}
```

#### 关卡题目生成
```javascript
function generateLevelQuestions(mode, count) {
  const questions = [];
  const seen = new Set();

  while (questions.length < count) {
    const roll = Math.random();
    let q;
    if (roll < 0.35) q = generateAddition();
    else if (roll < 0.70) q = generateSubtraction();
    else q = generateChained();

    const key = q.operands.join(',') + '|' + q.operators.join(',');
    if (!seen.has(key)) {
      seen.add(key);
      questions.push(q);
    }
  }
  return questions;
}
```

### 5.4 TimerController
```javascript
const TimerController = {
  start() {
    GameState.questionStartTime = Date.now();
    GameState.timerRemaining = GameState.timePerQuestion;
    this.interval = setInterval(() => {
      const elapsed = (Date.now() - GameState.questionStartTime) / 1000;
      const remaining = Math.max(0, GameState.timePerQuestion - elapsed);
      GameState.timerRemaining = remaining;
      UIRenderer.renderTimer(remaining, GameState.timePerQuestion);
      if (remaining <= 0) {
        this.stop();
        GameController.onTimerExpired();
      }
    }, 100); // 100ms 更新保证视觉平滑
  },
  stop()  { clearInterval(this.interval); },
  reset() { this.stop(); this.start(); },
  getElapsed() {
    return (Date.now() - GameState.questionStartTime) / 1000;
  }
};
```

### 5.5 InputHandler
- 使用 `pointerdown` 事件（避免移动端 300ms 延迟）
- 限制输入最多 2 位数字
- 输入为 "0" 时仍可提交（答案可能是 0）
- 空输入时确认键无效
- CSS `touch-action: manipulation` 防止双击缩放
- 同时监听物理键盘（方便调试）

### 5.6 AudioManager

#### SFXManager (Web Audio API)
- `AudioContext` 在首次用户交互时创建（mobile autoplay policy）
- 每个音效通过 `OscillatorNode` + `GainNode` 即时生成
- 答对音：C5(523Hz) → E5(659Hz)，正弦波，各 150ms
- 答错音：E4(330Hz) → C4(262Hz)，方波，200ms
- 按键音：800Hz 正弦波，20ms
- 关卡完成：C-E-G-C 琶音

#### SpeechManager (Web Speech Synthesis)
- 语言设置：`lang = 'zh-CN'`
- **优先选择女声**：遍历可用语音列表，优先匹配名称含"female"、"女"的中文语音
- 音调 `pitch = 1.3`（更活泼），语速 `rate = 0.85`（略慢，清晰温暖）
- 监听 `voiceschanged` 事件缓存中文女声
- 随机选取鼓励话术，避免连续重复

#### BGMManager
- 简单 C 大调五声音阶循环旋律
- 音量 0.05（极低背景音）
- 提供音频开关按钮

### 5.7 GameController 流程

```
startGame(mode)
  → generateLevelQuestions()
  → showScreen('game')
  → nextQuestion()

nextQuestion()
  → currentQuestion++
  → 如果 > questionsPerLevel → onLevelComplete()
  → 否则：renderQuestion(), resetInput(), startTimer(), setBunny('idle')

submitAnswer(answer)
  → if correct:
      → stopTimer(), playCorrect(), speakEncouragement()
      → showConfetti(), setBunny('happy')
      → 1.5s 后 nextQuestion()
  → if wrong, first attempt:
      → isRetry = true, playWrong(), speakRetry()
      → shake animation, setBunny('sad')
      → resetTimer(), clearInput()
  → if wrong, second attempt:
      → stopTimer(), playWrong(), speakComfort()
      → showCorrectAnswer(), setBunny('sad')
      → 2s 后 nextQuestion()

onTimerExpired()
  → 等同于 submitAnswer(null) — 走答错逻辑

onLevelComplete()
  → 计算 levelStats
  → overallStats.levels.push(levelStats)
  → if currentLevel < 4 → showScreen('level-complete')
  → else → showScreen('game-complete')
```

---

## 6. 关键技术细节

### 6.1 SVG 进度环
```html
<svg viewBox="0 0 36 36">
  <circle cx="18" cy="18" r="16" fill="none" stroke="#eee" stroke-width="3"/>
  <circle cx="18" cy="18" r="16" fill="none" stroke="var(--color-pink)"
          stroke-width="3" stroke-dasharray="100.53"
          stroke-dashoffset="CALCULATED" stroke-linecap="round"
          transform="rotate(-90 18 18)"/>
</svg>
```
- 周长 = 2π × 16 ≈ 100.53
- dashoffset = 100.53 × (1 - progress)

### 6.2 Viewport 配置
```html
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
```

### 6.3 Confetti 实现
- 生成 25 个 `<div>` 小方块
- 随机颜色、大小、旋转角、水平偏移
- 使用 CSS Animations 下落
- `animationend` 事件后移除 DOM 节点

### 6.4 防误操作
- 答案展示/过渡期间禁用键盘输入
- `user-select: none` 全局禁止文本选择
- `touch-action: manipulation` 禁止双击缩放

---

## 7. 性能考虑
- Confetti DOM 节点动画结束后立即清理
- 定时器使用 `Date.now()` 计算经过时间（不依赖 setInterval 精度）
- 音频 OscillatorNode 使用后自动断开回收
- 无内存泄漏点：所有 setInterval 都有对应 clearInterval
