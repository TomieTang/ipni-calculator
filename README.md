# 死亡概率计算器 - 使用说明

## 📋 项目简介

这是一个轻量级网页应用，用于计算医疗相关的总评分和死亡概率。应用采用纯前端技术实现，无需后端服务器，可直接在浏览器中运行。

## 🚀 如何运行

### 方法一：直接打开（推荐）
1. 找到项目目录中的 `mortality_calculator.html` 文件
2. 双击该文件，系统会自动使用默认浏览器打开
3. 或者右键点击文件，选择"打开方式" → 选择任意浏览器（Chrome、Edge、Firefox等）

### 方法二：通过本地服务器运行
如果需要通过 HTTP 协议访问（某些浏览器可能对本地文件有限制），可以使用以下方式：

**使用 Python（如果已安装）：**
```bash
# Python 3
python -m http.server 8000

# 然后在浏览器访问: http://localhost:8000/mortality_calculator.html
```

**使用 Node.js（如果已安装）：**
```bash
# 安装 http-server
npm install -g http-server

# 运行服务器
http-server

# 然后在浏览器访问显示的地址
```

## 📝 使用说明

1. **输入数值**：在三个输入框中分别输入：
   - 白蛋白 (Albumin)
   - 淋巴细胞 (Lymphocyte)
   - 乳酸 (Lactate)

2. **计算**：点击"计算"按钮，系统会自动计算并显示：
   - 总评分 (total_points)
   - 死亡概率 P(mortality=1)

3. **重置**：点击"重置"按钮可清空所有输入和结果

4. **输入验证**：
   - 仅允许输入数字（包括小数）
   - 数值必须 ≥ 0
   - 输入无效时会显示错误提示

## 🔧 如何修改公式参数

如果需要调整计算公式中的系数，可以编辑 `mortality_calculator.html` 文件中的 JavaScript 代码部分。

### 修改总评分公式

找到以下代码（约第 150 行）：
```javascript
// 公式①: total_points = 107.7 - 1.54*Albumin - 7.7*Lymphocyte + 2.9*Lactate
const totalPoints = 107.7 - (1.54 * albumin) - (7.7 * lymphocyte) + (2.9 * lactate);
```

**参数说明：**
- `107.7`：常数项
- `1.54`：白蛋白系数（负号表示减去）
- `7.7`：淋巴细胞系数（负号表示减去）
- `2.9`：乳酸系数（正号表示加上）

**修改示例：**
如果要将白蛋白系数改为 1.5，淋巴细胞系数改为 8.0，乳酸系数改为 3.0，常数项改为 100：
```javascript
const totalPoints = 100 - (1.5 * albumin) - (8.0 * lymphocyte) + (3.0 * lactate);
```

### 修改死亡概率公式

找到以下代码（约第 155 行）：
```javascript
// 公式②: P(mortality=1) = 1/(1+e^(-(total_points-65)/25))
const exponent = -(totalPoints - 65) / 25;
const mortalityProb = 1 / (1 + Math.pow(Math.E, exponent));
```

**参数说明：**
- `65`：偏移量（total_points 减去此值）
- `25`：缩放因子（用于调整曲线的陡峭程度）

**修改示例：**
如果要将偏移量改为 70，缩放因子改为 20：
```javascript
const exponent = -(totalPoints - 70) / 20;
const mortalityProb = 1 / (1 + Math.pow(Math.E, exponent));
```

### 修改小数位数

找到显示结果的代码（约第 160 行）：
```javascript
document.getElementById('total-points').textContent = totalPoints.toFixed(2);
document.getElementById('mortality-prob').textContent = (mortalityProb * 100).toFixed(2) + '%';
```

将 `.toFixed(2)` 中的 `2` 改为其他数字即可改变小数位数：
- `.toFixed(0)`：整数
- `.toFixed(1)`：1位小数
- `.toFixed(3)`：3位小数

## 🎨 自定义样式

如果需要修改页面外观，可以编辑 `<style>` 标签内的 CSS 代码（约第 15-80 行）。

**主要样式区域：**
- 背景渐变：`body` 的 `background` 属性
- 容器样式：`.calculator-container`
- 按钮颜色：`.btn-calculate` 和 `.btn-reset`
- 结果展示：`.result-item` 和 `.result-value`

## 📊 计算公式详解

### 公式①：总评分计算
```
total_points = 107.7 - 1.54 × Albumin - 7.7 × Lymphocyte + 2.9 × Lactate
```

这是一个线性组合公式，通过各项指标的加权求和得到总评分。

### 公式②：死亡概率计算
```
P(mortality=1) = 1 / (1 + e^(-(total_points - 65) / 25))
```

这是一个逻辑回归（Logistic Regression）公式，使用 Sigmoid 函数将总评分转换为 0-1 之间的概率值。

**公式特点：**
- 当 `total_points = 65` 时，死亡概率为 50%
- 当 `total_points > 65` 时，死亡概率 > 50%
- 当 `total_points < 65` 时，死亡概率 < 50%
- 缩放因子 25 控制曲线的陡峭程度（值越小，曲线越陡）

## 🔍 技术细节

- **前端框架**：纯 HTML + CSS + JavaScript（无框架依赖）
- **样式库**：Bootstrap 5.3.0（CDN）
- **数学计算**：使用 JavaScript 原生 `Math.E` 和 `Math.pow()` 函数
- **浏览器兼容性**：支持 Chrome、Edge、Firefox、Safari 等主流浏览器

## ⚠️ 注意事项

1. 本应用仅用于计算，不提供医疗建议
2. 计算结果仅供参考，实际医疗决策需咨询专业医生
3. 输入数值时请确保单位正确（与公式设计时的单位一致）
4. 建议使用最新版本的浏览器以获得最佳体验

## 📞 技术支持

如有问题或需要修改，请直接编辑 `mortality_calculator.html` 文件中的相应代码部分。

---

**版本信息：** v1.0  
**最后更新：** 2026

---

**Copyright © 2026 Guanjie Chen. All rights reserved.**