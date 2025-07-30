# DoF6/Chero Support Implementation - 完整开发日志

## 📋 项目概览

本文档记录了 OpenCat 项目中 DoF6 机器人支持的完整实现过程，包括从最初的 DoF6 实现到最终重命名为 Chero 的全过程。

## 🚀 第一阶段：DoF6 初始实现

### 概述
成功为 SkillComposer 添加了 DoF6 产品支持，具备以下关键功能：

### 主要更改

#### 1. 配置文件更新

**pyUI/commonVar.py**
- 在 `modelOptions` 列表中添加了 'DoF6'
- 在 `NaJoints` 配置中添加了 'DoF6': []
- 在 `scaleNames` 配置中添加了 'DoF6': RegularScaleNames

**serialMaster/ardSerial.py**
- 在 `postureDict` 配置中添加了 'DoF6': postureTableDoF16

**OpenCatPythonAPI/PetoiRobot/ardSerial.py**
- 在 `postureDict` 配置中添加了 'DoF6': postureTableDoF16

#### 2. SkillComposer.py 核心更改

**关节控制器 (createController 方法)**
- 修改为仅显示 DoF6 模型的 6 个关节
- 关节映射：[0, 1, 8, 9, 10, 11] → 显示索引 [0, 1, 2, 3, 4, 5]
- 只显示指定的 6 个关节的滑块
- 内部维护原始关节索引，显示简化的显示索引

**产品图片**
- 修改 `placeProductImage` 方法为 DoF6 模型使用 'DoF6.jpeg.jpg'
- 使用资源目录中的现有 DoF6.jpeg.jpg 文件

**数据导出 (export 方法)**
- 修改为仅导出 DoF6 模型的 6 个关节
- DoF6 的帧大小设置为 6（而不是 8, 12, 16, 或 20）
- 从帧数据中仅提取指定的 6 个关节 (0, 1, 8, 9, 10, 11)
- 在导出数据中映射为顺序索引 (0, 1, 2, 3, 4, 5)

**数据导入 (loadSkill 和 loadSkillDataText 方法)**
- 修改为处理 DoF6 模型的 6 关节数据
- 将导入的 6 关节数据映射回原始关节位置 (0, 1, 8, 9, 10, 11)
- 支持 DoF6 的行为模式和步态模式

### 技术细节

**关节映射**
- **原始关节索引**: [0, 1, 8, 9, 10, 11]
- **显示索引**: [0, 1, 2, 3, 4, 5]
- **内部存储**: 维护完整的 20 关节帧数据结构
- **导出/导入**: 仅处理指定的 6 个关节

**帧数据结构**
- **内部**: 标准 24 元素帧数据数组
- **导出**: DoF6 关节的 6 元素数组
- **导入**: 将 6 元素数组映射回 24 元素结构

**支持模式**
- **姿势**: 6 关节，copyFrom = 4
- **步态**: 6 关节，copyFrom = 8
- **行为**: 6 关节，copyFrom = 4

---

## 🔄 第二阶段：DoF6 到 Chero 重命名

### 概述
成功将整个 OpenCat 代码库中的所有 DoF6 引用重命名为 Chero，包括模型名称、文件名和注释。

### 修改的文件

#### 1. pyUI/commonVar.py
- 更改 `modelOptions` 列表：'DoF6' → 'Chero'
- 更改 `NaJoints` 字典：'DoF6' → 'Chero'
- 更改 `scaleNames` 字典：'DoF6' → 'Chero'

#### 2. pyUI/SkillComposer.py
- 更改模型引用：`self.model == 'DoF6'` → `self.model == 'Chero'`
- 更新布局配置：
  - `DoF6WinSet` → `CheroWinSet`
  - `DoF6MacSet` → `CheroMacSet`
- 更新图片文件引用：`'DoF6.jpeg.jpg'` → `'Chero.jpeg'`
- 更新文件中的注释

#### 3. OpenCatPythonAPI/PetoiRobot/ardSerial.py
- 更新模型检测：`'DoF6' in parse[l]` → `'Chero' in parse[l]`
- 更新姿势表选择：`postureDict['DoF6']` → `postureDict['Chero']`
- 更新帧大小逻辑：`'DoF6' in config.model_` → `'Chero' in config.model_`
- 更新大角度分割的网格大小逻辑
- 更新姿势字典：`'DoF6': postureTableDoF6` → `'Chero': postureTableDoF6`

#### 4. serialMaster/ardSerial.py
- 更新模型检测：`'DoF6' in parse[l]` → `'Chero' in parse[l]`
- 更新姿势表选择：`postureDict['DoF6']` → `postureDict['Chero']`
- 更新帧大小逻辑：`'DoF6' in config.model_` → `'Chero' in config.model_`
- 更新大角度分割的网格大小逻辑
- 更新姿势字典：`'DoF6': postureTableDoF6` → `'Chero': postureTableDoF6`

### 重命名的文件

**pyUI/resources/**
- `DoF6.jpeg.jpg` → `Chero.jpeg`
- `DoF6_Rest.jpeg.jpg` → `Chero_Rest.jpeg`

### 兼容性
- 所有更改保持向后兼容性
- 现有的 DoF6 机器人现在将被检测为 Chero
- 没有功能更改，仅名称更改

---

## 🛠️ 第三阶段：错误修复和功能完善

### 语法和缩进错误修复

#### 1. 缩进错误修复
- 修复了多个 Python 缩进错误
- 解决了 if/elif/else 语句结构问题
- 修复了函数定义的缩进问题

#### 2. 数组越界错误修复
- 修复了 `updateSliders` 函数中的 `IndexError`
- 添加了数组长度检查，防止访问超出范围的索引
- 对不存在的关节设置默认值 0

#### 3. 变量作用域错误修复
- 修复了 `setAngle` 函数中的 `UnboundLocalError`
- 纠正了 `indexedList` 变量的作用域问题
- 修复了 elif 语句的缩进问题

---

## 🎨 第四阶段：UI 布局优化

### 概述
根据用户要求，完成了 Chero 模型的界面布局优化：

### 主要修改

#### 1. 移除Debug信息
**修复的函数**:
- `updateSliders()` - 移除详细的角度调试输出
- `setPose()` - 移除preset posture的调试信息

#### 2. 滑块列布局调整
**修复前** (占据中间列):
```python
if leftQ:
    COL = 1  # Left side
else:
    COL = 5  # Right side
```

**修复后** (占据最左最右列):
```python
if leftQ:
    COL = 0  # Leftmost column
else:
    COL = 6  # Rightmost column
```

#### 3. IMU区域扩展
**修复前** (固定布局):
```python
self.frameImu.grid(row=rowFrameImu, column=3, rowspan=6, columnspan=2)
```

**修复后** (根据模型自适应):
```python
if self.model == 'Chero':
    # For Chero, use the same columnspan as the image (columns 2,3,4)
    self.frameImu.grid(row=rowFrameImu, column=2, rowspan=6, columnspan=3)
else:
    # For other models, keep original layout
    self.frameImu.grid(row=rowFrameImu, column=3, rowspan=6, columnspan=2)
```

#### 4. 关节标签映射
**修复前** (错误映射):
```python
sideLabel = txt(sideNames[i % 4]) + '\n'  # 简单取模，不正确
```

**修复后** (正确映射到DoF16):
```python
# Map Chero joints 2,3,4,5 to DoF16 joints 8,9,10,11 labels
dof16_index = i + 6  # 2->8, 3->9, 4->10, 5->11
sideLabel = txt(sideNames[dof16_index % 8]) + '\n'
```

### 最终布局效果

#### Chero模型布局 (修复后)
```
Column: 0    1    2    3    4    5    6
       [滑块]     [图][图][图]     [滑块]
       [2,5]     [片][片][片]     [3,4]
                 [IMU 6-axis区域]
```

---

## 🪞 第五阶段：镜像功能修复

### 问题描述
点击 mirror (>|<) 按钮时，Chero 模型出现以下错误：
- `IndexError: list index out of range` - 尝试访问不存在的数组索引
- 左右腿和头的角度没有正确镜像

### 根本原因
原始镜像代码是为 16 关节模型设计的，但 Chero 只有 6 个关节：
1. **数组越界**: 代码尝试访问 `self.values[16 + 2]`，但 Chero 只有 12 个值
2. **错误的关节映射**: 使用了 16 关节的镜像逻辑
3. **错误的数据范围**: 发送 `[4:20]` 而不是 `[4:10]` 的数据

### 修复方案

#### 1. 修复 `generateMirrorFrame` 函数
```python
def generateMirrorFrame(self):
    if self.model == 'Chero':
        # 为 Chero 镜像 6 轴值（索引 6 和 9，对应 Y 轴和 Roll）
        if len(self.values) > 6 + 2:
            self.values[6 + 2].set(-1 * self.values[6 + 2].get())  # Y 轴
        if len(self.values) > 6 + 5:
            self.values[6 + 5].set(-1 * self.values[6 + 5].get())  # Roll 轴
    else:
        # 其他模型的原始逻辑
        self.values[16 + 2].set(-1 * self.values[16 + 2].get())
        self.values[16 + 5].set(-1 * self.values[16 + 5].get())
        
    # 发送正确的数据范围
    if self.model == 'Chero':
        send(ports, ['L', self.frameData[4:10], 0.05])  # 6 关节
    else:
        send(ports, ['L', self.frameData[4:20], 0.05])  # 16 关节
```

#### 2. 修复 `mirrorAngles` 函数
```python
def mirrorAngles(self, singleFrame):
    if self.model == 'Chero':
        # Chero (6 关节) 专用镜像逻辑
        if len(singleFrame) > 4 + 5:
            # 镜像头部平移 (关节 0)
            singleFrame[4 + 0] = -singleFrame[4 + 0]
            # 头部倾斜 (关节 1) 保持不变
            # 交换左右肩膀关节 (2,3)
            singleFrame[4 + 2], singleFrame[4 + 3] = singleFrame[4 + 3], singleFrame[4 + 2]
            # 交换左右手臂关节 (4,5)
            singleFrame[4 + 4], singleFrame[4 + 5] = singleFrame[4 + 5], singleFrame[4 + 4]
    else:
        # 16 关节模型的原始逻辑
        singleFrame[1] = -singleFrame[1]
        singleFrame[4] = -singleFrame[4]
        singleFrame[4 + 2] = -singleFrame[4 + 2]
        for i in range(4, 16, 2):
            singleFrame[4 + i], singleFrame[4 + i + 1] = singleFrame[4 + i + 1], singleFrame[4 + i]
```

### 测试结果

#### 镜像前后对比
```
镜像前: [30, 0, -47, 50, 22, 19]
镜像后: [-30, 0, 50, -47, 19, 22]
```

#### 关节变化说明
- **关节 0 (Head Pan)**: 30 → -30 ✅ (头部左右镜像)
- **关节 1 (Head Tilt)**: 0 → 0 ✅ (头部上下保持不变)  
- **关节 2,3 (肩膀)**: -47,50 → 50,-47 ✅ (左右肩膀互换)
- **关节 4,5 (手臂)**: 22,19 → 19,22 ✅ (左右手臂互换)

---

## 📤 第六阶段：导出功能修复

### 问题描述
在导出技能时，Chero 模型漏掉了每帧后四位的描述信息：
- **应该**: 6个关节 + 4个描述信息 = 10个数
- **实际**: 只导出了6个关节角度，漏掉了4个描述信息

### 问题根源
在 `pyUI/SkillComposer.py` 的导出逻辑中：
```python
# 修复前 - 只提取6个关节
dof6Data = self.frameData[4:10]  # 只有6个值
```

### 修复方案
修复后的代码正确包含了4个描述信息：
```python
# 修复后 - 提取6个关节 + 4个描述信息
dof6Data = self.frameData[4:10] + self.frameData[20:24]  # 6 joints + 4 description values
```

### 数据结构说明

#### frameData 结构
- **索引 0-3**: 头部信息 `[1, 0, 0, 1]`
- **索引 4-9**: 6个关节角度 `[30, 0, -47, 50, 22, 19]`
- **索引 10-19**: 预留/填充数据
- **索引 20-23**: 4个描述信息 `[8, 2, 0, 0]`

#### 导出数据格式
- **修复前**: `[30, 0, -47, 50, 22, 19]` (6个值) ❌
- **修复后**: `[30, 0, -47, 50, 22, 19, 8, 2, 0, 0]` (10个值) ✅

#### 4个描述信息的含义
每帧的后4个描述信息通常包含：
1. **索引 20**: 步长信息 (通常是8)
2. **索引 21**: 时间信息 (延迟/速度)
3. **索引 22**: 触发轴信息
4. **索引 23**: 角度信息

---

## 🎮 第七阶段：UI 界面进一步优化

### 布局紧凑化

根据用户需求，将原来的7列布局优化为更紧凑的5列布局：

#### 新的5列布局设计：
- **列 0-1**: 关节0的水平滑条（占2列）
- **列 1-3**: 中间的图片和6轴IMU控制区域（占3列，与关节0重叠1列）
- **列 3-4**: 关节1的水平滑条（占2列，与中间区域重叠1列）
- **列 0**: 关节2（左前）和关节5（左后）
- **列 4**: 关节3（右前）和关节4（右后）

### 按钮位置优化

#### Head Pan和Head Tilt的+/-按钮优化

**最终方案：**
- **关节0 (Head Pan)**:
  - `-`按钮：位于ROW+1行，第0列的左边 (`sticky='w'`)
  - `+`按钮：位于ROW+1行，第1列的右边 (`sticky='e'`)

- **关节1 (Head Tilt)**:
  - `-`按钮：位于ROW+1行，第3列的左边 (`sticky='w'`)
  - `+`按钮：位于ROW+1行，第4列的右边 (`sticky='e'`)

**关键改进：**
1. **不遮挡滑条**: 按钮在标签行（ROW+1）而不是滑条行（ROW+2）
2. **精确定位**: 使用具体的列位置和sticky参数来精确控制按钮位置
3. **视觉美观**: `-`按钮在左边，`+`按钮在右边，符合用户直觉
4. **布局清晰**: 按钮与滑条不重叠，界面更加整洁

---

## 🎯 项目成果总结

### ✅ 完成的功能

1. **完整的Chero模型支持**
   - 6关节机器人完全支持
   - 专用的布局和控制逻辑
   - 正确的姿势和步态处理

2. **全面的重命名**
   - 所有DoF6引用已成功改为Chero
   - 文件重命名和资源更新
   - 向后兼容性保持

3. **错误修复**
   - 语法和缩进错误修复
   - 数组越界保护
   - 变量作用域问题解决

4. **UI优化**
   - 紧凑的5列布局
   - 精确的按钮定位
   - 优化的视觉效果

5. **功能完善**
   - 镜像功能支持
   - 导出功能修复
   - 调试信息清理

### 📊 技术规格

#### 16自由度模型 (Bittle/Nybble/DoF16)
- **每帧**: 16个关节 + 4个描述信息 = 20个数 ✅

#### 6自由度模型 (Chero)
- **每帧**: 6个关节 + 4个描述信息 = 10个数 ✅
- **关节映射**: [0,1,2,3,4,5] → [0,1,8,9,10,11]
- **布局**: 5列紧凑布局
- **功能**: 完整的镜像、导出、导入支持

### 🚀 用户体验

- **界面简洁**: 只显示相关的6个关节控制
- **操作直观**: 按钮位置合理，不遮挡控制元素
- **功能完整**: 支持所有必要的机器人控制功能
- **性能优化**: 移除冗余的调试信息，运行更流畅

### 📝 维护性

- **代码整洁**: 错误修复和重构提升了代码质量
- **文档完善**: 详细的变更记录便于后续维护
- **兼容性好**: 不影响现有其他模型的功能
- **扩展性强**: 为未来的新模型支持奠定了基础

**Chero支持现已完全实现并优化！** 🎉

---

## 📁 相关文件

本项目涉及的主要文件和修改记录：

### 代码文件
- `pyUI/SkillComposer.py` - 主要实现
- `pyUI/commonVar.py` - 配置
- `serialMaster/ardSerial.py` - 串口通信
- `OpenCatPythonAPI/PetoiRobot/ardSerial.py` - API接口

### 资源文件  
- `pyUI/resources/Chero.jpeg` - 产品图片
- `pyUI/resources/Chero_Rest.jpeg` - 休息姿势图片

### 文档文件
- `DoF6_Implementation_Summary.md` - 初始实现总结
- `DoF6_to_Chero_Rename_Summary.md` - 重命名记录
- `Chero_Rename_Complete.md` - 重命名完成总结
- `Chero_Layout_Complete_Fix.md` - 布局修复记录
- `Mirror_Function_Fix.md` - 镜像功能修复
- `Export_Fix_Summary.md` - 导出功能修复
- `DoF6_support.md` - 本综合文档
