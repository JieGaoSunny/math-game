# 数学乐园 — 开发文档

## 1. 技术栈

| 类别 | 技术 |
|------|------|
| 结构 | HTML5 |
| 样式 | CSS3 (Flexbox, Grid, Animations, CSS Variables) |
| 逻辑 | Vanilla JavaScript (ES6+) |
| 音频 | Web Audio API + Web Speech Synthesis API |
| 图形 | 图片素材（小奶龙）+ SVG (进度环) |
| 部署 | 单 HTML 文件，浏览器直接打开 |

**无外部依赖**。所有代码内联在一个 HTML 文件中。

---

## 2. 文件结构

```
math_game/
├── index.html          # 游戏主文件（唯一的代码文件）
├── images/             # 小奶龙图片素材
│   ├── nailong-sayhi.png      # 打招呼（首页）
│   ├── nailong-happy.jpg      # 开心（关卡完成）
│   ├── nailong-curious.png    # 好奇（盲盒/答对）
│   ├── nailong-unhappy.png    # 不开心（答错/错题）
│   └── nailong-twinkle.png    # 闪亮得意（全部通关）
└── docs/
    ├── PRD.md          # 产品需求文档
    ├── DEV.md          # 开发文档（本文件）
    └── TEST.md         # 测试文档
```

---

## 3. HTML 页面结构

页面包含 7 个顶层 Screen，通过 `.active` 类控制显示：

```html
<body>
  <div id="screen-start" class="screen active">   <!-- 开始/难度选择 -->
  <div id="screen-game" class="screen">            <!-- 答题主界面 -->
  <div id="screen-reward" class="screen">          <!-- 关卡盲盒 -->
  <div id="screen-level-complete" class="screen">  <!-- 关卡结算 -->
  <div id="screen-wrong-review" class="screen">    <!-- 错题回顾（独立全屏） -->
  <div id="screen-game-complete" class="screen">   <!-- 全部完成 -->
  <div id="screen-rest" class="screen">            <!-- 休息页 -->
</body>
```

### 3.1 Screen: 开始页 (`#screen-start`)
```
┌──────────────────────────┐
│        数学乐园            │
│  每天进步一点点，数学变得好简单  │
│                          │
│    [小奶龙打招呼图片]       │
│    (nailong-sayhi.png)    │
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
│      🐰 (小奶龙角色)      │  ← 角色区（nailong-curious.png）
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
│       🎉 第1关通过        │
│                          │
│   [小奶龙开心图片]        │
│   (nailong-happy.jpg)    │
│                          │
│   正确率: 85%            │
│   平均用时: 4.2秒        │
│                          │
│   ┌──────────────────┐   │
│   │   📘 查看错题     │   │  ← 与下方按钮同级
│   └──────────────────┘   │
│   ┌──────────────────┐   │
│   │   继续下一关       │   │
│   └──────────────────┘   │
│   ┌──────────────────┐   │
│   │   休息一下         │   │
│   └──────────────────┘   │
└──────────────────────────┘
```

### 3.3A Screen: 错题回顾 (`#screen-wrong-review`)
```
┌──────────────────────────┐
│       📘 错题回顾         │
│                          │
│   1. 10 + 6 = ?          │
│      你的答案: 15         │
│      正确答案: 16         │
│   ────────────────────   │
│   2. 19 - 8 = ?          │
│      你的答案: 12         │
│      正确答案: 11         │
│   ────────────────────   │
│   ...（可上下滚动）       │
│                          │
│   ┌──────────────────┐   │
│   │      返回         │   │
│   └──────────────────┘   │
└──────────────────────────┘
```
注：错题包含所有第一次答错的题目（即使重试后答对）。

### 3.3B Screen: 盲盒页 (`#screen-reward`)
```
┌──────────────────────────┐
│      🎁 关卡盲盒奖励      │
│                          │
│    [ ? 盲盒 ]            │  ← 微微周期性抽动，点击可触发抽奖
│                          │
│      点击开始抽奖         │
│   (点击后：剧烈摇晃       │
│    → emoji轮转(10+个)     │
│    → 0.8~1.2s 揭晓)      │
│                          │
│   💪 数学小能手！         │  ← 结果区直接显示鼓励语（不显示"抽中：XXX"）
│                          │
│   ┌──────────────────┐   │
│   │      继续           │   │
│   └──────────────────┘   │
└──────────────────────────┘
```

### 3.4 Screen: 全部完成 (`#screen-game-complete`)
```
┌──────────────────────────┐
│     🎊 全部通关！         │
│                          │
│   [小奶龙闪亮得意图片]    │
│   (nailong-twinkle.png)  │
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
│   [小奶龙打招呼图片]      │
│   (nailong-sayhi.png)    │
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
  --bg-primary: #FFFDE7;
  --bg-secondary: #FFF8E1;
  --bg-game: linear-gradient(180deg, #FFFDE7 0%, #FFF8E1 50%, #FFF3E0 100%);
  --color-accent: #FFB300;       /* 主强调色（金黄） */
  --color-accent-light: #FFD54F; /* 浅强调色 */
  --color-accent-dark: #FF8F00;  /* 深强调色 */
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
| `bounce` | 答对跳跃效果 | 0.5s |
| `shake` | 答错时题目抖动 | 0.4s |
| `fadeInUp` | 新题目出现 | 0.3s |
| `confetti-fall` | 彩纸飘落 | 1.5s |
| `pulse` | 倒计时最后3秒闪烁 | 0.5s infinite |
| `float` | 图片空闲浮动 | 3s infinite |
| `celebrate` | 关卡完成庆祝 | 1s infinite |
| `blink` | 输入区光标闪烁 | 1s infinite |
| `nudge` | 盲盒方块微微抽动（等待点击） | 1.5s infinite |
| `violent-shake` | 盲盒方块剧烈摇晃（触发后） | 0.6-0.8s |

### 4.4 小奶龙角色实现
角色从 CSS 绘制改为使用图片素材。使用 `<img>` 标签替代原有的 `.bunny` CSS 结构。

```html
<!-- 角色图片容器 -->
<div class="character-area">
  <img class="character-img" id="character-img" src="images/nailong-sayhi.png" alt="小奶龙">
</div>
```

**场景对应图片**：
| 场景 | 图片文件 | 说明 |
|------|---------|------|
| 首页 | `nailong-sayhi.png` | 打招呼 |
| 答题空闲/答对 | `nailong-curious.png` | 好奇 |
| 答错 | `nailong-unhappy.png` | 不开心 |
| 盲盒页 | `nailong-curious.png` | 好奇 |
| 关卡完成 | `nailong-happy.jpg` | 开心 |
| 游戏全部完成 | `nailong-twinkle.png` | 闪亮得意 |
| 休息页 | `nailong-sayhi.png` | 打招呼 |

**CSS 样式**：
```css
.character-area {
  display: flex;
  justify-content: center;
  align-items: center;
}
.character-img {
  max-height: 120px;
  width: auto;
  object-fit: contain;
  animation: float 3s ease-in-out infinite;
}
```

---

## 5. JavaScript 模块设计

### 5.1 模块总览

```
GameApp (IIFE)
├── GameState          // 游戏状态数据
├── QuestionGenerator  // 题目生成
├── RewardEngine       // 盲盒奖励组合与概率
├── ScreenManager      // 页面切换
├── UIRenderer         // DOM 更新
├── TimerController    // 倒计时控制
├── InputHandler       // 键盘输入处理
├── AudioManager       // 音效和语音
│   ├── SFXManager     // 本地 mp3 音效优先
│   ├── SpeechManager  // TTS 兜底
│   └── BGMManager     // 本地 BGM
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
  wrongQuestions: [],
  currentInput: '',
  isRetry: false,
  lastWrongInput: null,
  timerInterval: null,
  questionStartTime: null,
  pendingLevelTransition: null,

  levelStats: { correct: 0, wrong: 0, totalTime: 0, retries: 0 },
  overallStats: { levels: [] },

  audioEnabled: true,
  bgmPlayer: null,
  rewardHistory: []
};
```

### 5.3 QuestionGenerator — 题目生成算法

#### 题目质量控制策略

| 策略 | 说明 |
|------|------|
| 操作数不为 0 | 所有题型的操作数均 > 0，加/减 0 无练习价值 |
| 答案为 0 限制 | 每关最多 1 道答案为 0 的题目 |
| 简单题比例控制 | 通过概率机制控制简单题占比不超过 15-20%，不硬性写死下限 |
| 答案分布均匀 | 将答案范围均分若干区间，检测并拒绝导致某区间过度集中的题目（单区间不超 40%） |

#### 简单加法
```javascript
function generateAddition() {
  const max = this.getMax();
  const answer = randomInt(6, max);   // 保证有足够空间使两个操作数都 ≥ 3
  const a = randomInt(3, answer - 3);
  const b = answer - a;
  // 额外检查：操作数不能为 0
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
  // 额外检查：操作数不能为 0，answer 为 0 时需检查配额
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

    // 额外检查：所有操作数 > 0
    if (a > 0 && b > 0 && c > 0) break;
  }
  return { operands: [a, b, c], operators: ops, answer };
}
```

#### 关卡题目生成
```javascript
function generateLevelQuestions(mode, count) {
  const questions = [];
  const seen = new Set();
  let zeroAnswerCount = 0; // 答案为 0 的题目计数

  while (questions.length < count) {
    const roll = Math.random();
    let q;
    if (roll < 0.20)      q = generateAddition();
    else if (roll < 0.40) q = generateSubtraction();
    else if (roll < 0.48) q = generateCarryAddition();
    else if (roll < 0.55) q = generateBorrowSubtraction();
    else if (roll < 0.82) q = generateChained();
    else                  q = generateLongChained();

    // 检查操作数不含 0
    if (q.operands.some(op => op === 0)) continue;

    // 检查答案为 0 配额
    if (q.answer === 0) {
      if (zeroAnswerCount >= 1) continue;
      zeroAnswerCount++;
    }

    // 去重
    const key = q.display;
    if (seen.has(key)) continue;
    seen.add(key);

    // 答案分布均匀性检查（在累积到一定数量后生效）
    // ...

    questions.push(q);
  }

  // 最终 shuffle
  return shuffle(questions);
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
- 自动判题：输入位数达到当前题正确答案位数后，自动调用 `submitAnswer()`
- CSS `touch-action: manipulation` 防止双击缩放
- 同时监听物理键盘（方便调试）

### 5.6 AudioManager

#### SFXManager (本地 mp3 优先)
- 按键：`click.bubble.mp3`、`click.common.button.mp3`
- 答对：`Eliminate1~8.mp3` 分池随机
- 答错：`board.mp3`（首次）/ `ice_break.mp3`（最终）
- 关卡完成：`money.mp3`
- 盲盒：开启 `Eliminate6/7.mp3`，揭晓普通/稀有 `money.mp3`，超稀有 `unbelievable.mp3`
- 若文件缺失或播放失败，自动回退到既有简易音频（或静默）

#### SpeechManager (Web Speech Synthesis 兜底)
- 仅在本地鼓励语音文件不可用时触发
- 语言设置：`lang = 'zh-CN'`
- **优先选择女声**：遍历可用语音列表，优先匹配名称含"female"、"女"的中文语音
- 音调 `pitch = 1.3`，语速 `rate = 0.85`
- 监听 `voiceschanged` 事件缓存中文女声

#### BGMManager
- 游戏页：`GameSceneBGM.mp3`
- 非游戏页：`WorldSceneBGM.mp3`
- **首页 BGM**：页面加载后立即尝试 `new Audio().play()` 自动播放 `WorldSceneBGM.mp3`；若浏览器策略阻止（play() 返回 rejected Promise），则监听 `document` 的首次 `click`/`touchstart` 事件恢复播放
- 场景切换时做短淡入淡出
- 音量建议 0.08 ~ 0.12，受全局静音开关控制

### 5.6A RewardEngine
- 组合式奖励：`类型 × 主题 × 品质`
- 品质概率：普通 65%、稀有 28%、超稀有 7%
- 同一局弱去重：尽量避免与上一关奖励完全相同
- 输出结构：`{ type, theme, rarity, label, rarityKey }`
- **盲盒方块动画**：等待时 CSS `@keyframes nudge` 微微抽动；触发后 `@keyframes violent-shake` 剧烈摇晃
- **轮转 emoji 池**：`['🎁','✨','🎊','🌟','💎','🌈','🧸','🎉','🌊','🎵','🦋','🍀']`，每次 `shuffle()` 后取前 6-8 个轮转
- **鼓励语显示**：抽中后结果区域直接显示鼓励语，不再显示"抽中：XXX"文字，也不在下方单独显示鼓励语
- **鼓励语池**：按品质分级，每级 10+ 句随机轮换
  - 普通：`["每天进步一点点！","数学小达人！","继续加油哦！","你做得很棒！","又完成了一关！","坚持就是胜利！","你很认真呢！","学习使你更强大！","小小数学家！","一步一个脚印！"]`
  - 稀有：`["数学小能手！","太厉害了！","你的努力有回报了！","越来越棒了！","思维敏捷，了不起！","你是学习的榜样！","进步神速！","聪明的小脑袋！","你让小奶龙骄傲！","运气和实力并存！"]`
  - 超稀有：`["你是最棒的！","不可思议的天才！","超级数学之星！","了不起了不起！","简直是数学精灵！","你拥有超能力！","传说中的学霸！","全世界最厉害的小朋友！","数学王国的勇士！","你闪闪发光！"]`

### 5.7 GameController 流程

```
页面加载
  → 显示 start 页面
  → 立即尝试自动播放 WorldSceneBGM
  → 若被阻止 → 监听首次 click/touchstart → 恢复播放

startGame(mode)
  → generateLevelQuestions()  // 含操作数≠0、答案0限额、分布均匀性检查
  → showScreen('game')
  → nextQuestion()

nextQuestion()
  → currentQuestion++
  → 如果 > questionsPerLevel → onLevelComplete()
  → 否则：renderQuestion(), resetInput(), startTimer(), setCharacter('curious')

submitAnswer(answer)
  → if correct:
      → stopTimer(), playCorrect(), speakEncouragement()
      → showConfetti(), setCharacter('curious')
      → if isRetry: 记录错题（首次错误答案）
      → 1.5s 后 nextQuestion()
  → if wrong, first attempt:
      → isRetry = true, 保存 lastWrongInput
      → 记录错题（第一次答错即记录）
      → playWrong(), speakRetry()
      → shake animation, setCharacter('unhappy')
      → resetTimer(), clearInput()
  → if wrong, second attempt:
      → stopTimer(), playWrong(), speakComfort()
      → showCorrectAnswer(), setCharacter('unhappy')
      → 更新错题记录（最终答案仍错）
      → 2s 后 nextQuestion()

onTimerExpired()
  → 等同于 submitAnswer(null) — 走答错逻辑

onLevelComplete()
  → 计算 levelStats
  → overallStats.levels.push(levelStats)
  → generateReward() + showScreen('reward')
  → reward 页：方块微微抽动 → 点击触发 → 剧烈摇晃 → emoji轮转 → 揭晓 → 直接显示鼓励语
  → 点击继续后 showScreen('level-complete')
  → level-complete 显示统计 + "查看错题"按钮（同级按钮）+ "继续下一关" + "休息一下"
  → 点击"查看错题" → showScreen('wrong-review')
  → wrong-review 页：全屏展示错题列表 + "返回"按钮
  → 点击"返回" → showScreen('level-complete')
  → if currentLevel >= 4 且继续 → showScreen('game-complete')
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
