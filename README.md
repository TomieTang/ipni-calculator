# LPNI Calculator - 使用说明

## 📋 项目简介

这是一个轻量级网页应用，用于计算营养不良状态（Malnutrition）和死亡风险概率。应用采用纯前端技术实现，无需后端服务器，可直接在浏览器中运行。

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
   - 白蛋白 (Albumin) - 单位：g/L
   - 淋巴细胞 (Lymphocyte) - 单位：×10⁹/L
   - 乳酸 (Lactate) - 单位：mmol/L

2. **计算**：点击"Calculate"按钮，系统会自动计算并显示：
   - **Malnutrition（营养不良状态）**：根据 PNI 值判断，显示 "Yes" 或 "No"
   - **Risk of mortality（死亡风险）**：显示死亡概率百分比

3. **重置**：点击"Reset"按钮可清空所有输入和结果

4. **输入验证**：
   - 仅允许输入数字（包括小数）
   - 支持小数点后最多2位
   - 数值必须 ≥ 0
   - 输入无效时会显示错误提示

## 🔧 如何修改公式参数

如果需要调整计算公式中的系数，可以编辑 `mortality_calculator.html` 文件中的 JavaScript 代码部分。

### 修改 PNI 计算公式

找到以下代码（约第 220 行）：
```javascript
// Calculate PNI: PNI = albumin + 5*Lymphocyte
const pni = albumin + (5 * lymphocyte);
```

**参数说明：**
- `5`：淋巴细胞的系数

**修改示例：**
如果要将淋巴细胞系数改为 4.5：
```javascript
const pni = albumin + (4.5 * lymphocyte);
```

### 修改营养不良判断阈值

找到以下代码（约第 225 行）：
```javascript
// If PNI <= 36.65, then Malnutrition is Yes, otherwise No
const malnutrition = pni <= 36.65 ? 'Yes' : 'No';
```

**参数说明：**
- `36.65`：营养不良判断的阈值

**修改示例：**
如果要将阈值改为 40：
```javascript
const malnutrition = pni <= 40 ? 'Yes' : 'No';
```

### 修改死亡概率公式

找到以下代码（约第 230 行）：
```javascript
// Formula: P(mortality = 1) = 1 / (1 + exp(-(1.8999 - 0.1166 × PNI + 0.3269 × Lactate)))
const exponent = -(1.8999 - (0.1166 * pni) + (0.3269 * lactate));
const mortalityProb = 1 / (1 + Math.pow(Math.E, exponent));
```

**参数说明：**
- `1.8999`：常数项
- `0.1166`：PNI 的系数（负号表示减去）
- `0.3269`：乳酸的系数（正号表示加上）

**修改示例：**
如果要将常数项改为 2.0，PNI 系数改为 0.12，乳酸系数改为 0.35：
```javascript
const exponent = -(2.0 - (0.12 * pni) + (0.35 * lactate));
const mortalityProb = 1 / (1 + Math.pow(Math.E, exponent));
```

### 修改小数位数

找到显示结果的代码（约第 235 行）：
```javascript
document.getElementById('malnutrition').textContent = malnutrition;
document.getElementById('mortality-prob').textContent = (mortalityProb * 100).toFixed(2) + '%';
```

将 `.toFixed(2)` 中的 `2` 改为其他数字即可改变死亡概率的小数位数：
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

### 公式①：PNI（预后营养指数）计算
```
PNI = Albumin + 5 × Lymphocyte
```

这是一个简单的线性组合公式，用于评估患者的营养状态。

### 公式②：营养不良判断
```
Malnutrition = {
    "Yes"  if PNI ≤ 36.65
    "No"   if PNI > 36.65
}
```

根据 PNI 值判断患者是否存在营养不良：
- 当 PNI ≤ 36.65 时，判定为营养不良（Malnutrition = Yes）
- 当 PNI > 36.65 时，判定为无营养不良（Malnutrition = No）

### 公式③：死亡概率计算
```
P(mortality = 1) = 1 / (1 + exp(-(1.8999 - 0.1166 × PNI + 0.3269 × Lactate)))
```

这是一个逻辑回归（Logistic Regression）公式，使用 Sigmoid 函数将 PNI 和乳酸值转换为 0-1 之间的死亡概率值。

**公式特点：**
- PNI 值越高，死亡风险越低（系数为负）
- 乳酸值越高，死亡风险越高（系数为正）
- 结果以百分比形式显示（0-100%）

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
