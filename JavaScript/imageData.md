`ImageData` 是 JavaScript 中用于操作图像像素数据的对象，通常在 HTML5 的 `<canvas>` 元素上下文中使用。通过`ImageData`我们可以直接读取和修改图像的像素值。
参考 [MDN-ImageData]()

### 创建 `ImageData`

`ImageData` 可以通过以下方式创建：

1. **使用 Canvas 的 `createImageData` 方法**：
   ```javascript
   const canvas = document.createElement('canvas');
   const ctx = canvas.getContext('2d');
   const imageData = ctx.createImageData(width, height);
   ```
   这会创建一个全透明（RGBA 值为 0）的空白图像数据对象。

2. **使用 Canvas 的 `getImageData` 方法**：
   ```javascript
   const imageData = ctx.getImageData(x, y, width, height);
   ```
   这会从指定的 Canvas 区域获取图像数据。

3. **直接使用 `ImageData` 构造函数**：
   ```javascript
   const imageData = new ImageData(width, height);
   ```
   或者通过提供一个 `Uint8ClampedArray` 的像素数组：
   ```javascript
   const data = new Uint8ClampedArray(width * height * 4); // 每个像素需要 4 个值 R 、G、B、A
   const imageData = new ImageData(data, width, height);
   ```

---

### `ImageData` 的结构

`ImageData` 对象主要包含以下两个属性：

1. **`data`**: 
   - 是一个 `Uint8ClampedArray`，长度为 `width * height * 4`。
   - 每个像素由 4 个连续的值表示，分别对应 **R**（红色）、**G**（绿色）、**B**（蓝色）、**A**（透明度），范围为 `0` 到 `255`。
   - 数据按行存储，从左到右，从上到下排列。
   - 每个通道的值范围是 0-255：
   - 颜色分量：R, G, B。
   - 透明度分量：A，0 表示完全透明，255 表示完全不透明。

   ```javascript
   const red = imageData.data[0]; // 第一个像素的红色分量
   const green = imageData.data[1]; // 第一个像素的绿色分量
   const blue = imageData.data[2]; // 第一个像素的蓝色分量
   const alpha = imageData.data[3]; // 第一个像素的透明度
   ```

2. **`width` 和 `height`**:
   - 图像数据的宽度和高度（以像素为单位）。


假设我们有一个 2x2 的图像，像素值如下：

```
左上角：红色 (255, 0, 0, 255)
右上角：绿色 (0, 255, 0, 255)
左下角：蓝色 (0, 0, 255, 255)
右下角：白色 (255, 255, 255, 255)
```

ImageData.data 数组的内容会是：

```javascript
Uint8ClampedArray([
  // 第一行像素
  255, 0, 0, 255,    // 左上角 - 红色
  0, 255, 0, 255,    // 右上角 - 绿色

  // 第二行像素
  0, 0, 255, 255,    // 左下角 - 蓝色
  255, 255, 255, 255 // 右下角 - 白色
]);
```
数据以像素为单位，每个像素由 4 个值组成：R, G, B, A（红、绿、蓝、透明度）。


---

### 使用示例

#### 修改像素数据
以下示例将图像中的所有像素设为红色：
```javascript
const canvas = document.createElement('canvas');
const ctx = canvas.getContext('2d');
const imageData = ctx.createImageData(100, 100); // 创建 100x100 的图像数据

for (let i = 0; i < imageData.data.length; i += 4) {
  imageData.data[i] = 255; // Red
  imageData.data[i + 1] = 0; // Green
  imageData.data[i + 2] = 0; // Blue
  imageData.data[i + 3] = 255; // Alpha
}

ctx.putImageData(imageData, 0, 0); // 将图像数据绘制到 Canvas 上
```

---

### 常用操作

1. **绘制到 Canvas**
   使用 `putImageData` 方法可以将修改后的图像数据绘制到指定位置：
   ```javascript
   ctx.putImageData(imageData, x, y);
   ```

2. **读取像素数据**
   使用 `getImageData` 方法可以获取特定区域的图像数据：
   ```javascript
   const imageData = ctx.getImageData(x, y, width, height);
   ```

3. **图像特效**
   修改 `data` 数组中的 RGBA 值，可以实现多种效果，例如：
   - 灰度处理：
     ```javascript
     for (let i = 0; i < imageData.data.length; i += 4) {
       const avg = (imageData.data[i] + imageData.data[i + 1] + imageData.data[i + 2]) / 3;
       imageData.data[i] = avg; // Red
       imageData.data[i + 1] = avg; // Green
       imageData.data[i + 2] = avg; // Blue
     }
     ```

   - 反色处理：
     ```javascript
     for (let i = 0; i < imageData.data.length; i += 4) {
       imageData.data[i] = 255 - imageData.data[i]; // Red
       imageData.data[i + 1] = 255 - imageData.data[i + 1]; // Green
       imageData.data[i + 2] = 255 - imageData.data[i + 2]; // Blue
     }
     ```

