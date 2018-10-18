[![API](https://img.shields.io/badge/API-16%2B-blue.svg?style=flat)](https://android-arsenal.com/api?level=16)
[![License](http://img.shields.io/badge/License-Apache%202.0-brightgreen.svg?style=flat)](https://opensource.org/licenses/Apache-2.0)

 

- **自定义`SeekBar`，**

- **进度变化由可视化气泡样式呈现，定制化程度较高**  
- **实现带刻度的进度条实现颜色渐变效果**

---
## 整体效果如下：

eg：![demo1](https://github.com/KosmoSakura/BubbleSeekBar/blob/master/show/showinit.gif)
---

基于[woxingxiao的BubbleSeekBar](https://github.com/woxingxiao/BubbleSeekBar)修改

---

### 主要代码
#### 1.attr中新增属性：

```xml
<!--渐变色号，用_分割-->
<attr name="bsb_colors" format="string"/>
<!--是否显示刻度，默认：true-->
<attr name="bsb_marks" format="boolean"/>
```

#### 2.布局示例

```xml
<cos.mos.sb.widget.KBubbleSeekBar
        android:id="@+id/sb2"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="24dp"
        kosmos:bsb_bubble_text_color="#24d1b4"
        kosmos:bsb_colors="#ffffffff_#ff24d1b4_#ff000000"
        kosmos:bsb_marks="false"
        kosmos:bsb_max="100"
        kosmos:bsb_min="0"
        kosmos:bsb_progress="20"
        kosmos:bsb_second_track_color="#15398e"
        kosmos:bsb_section_count="5"
        kosmos:bsb_section_text_position="below_section_mark"
        kosmos:bsb_show_progress_in_float="true"
        kosmos:bsb_show_section_mark="false"
        kosmos:bsb_show_section_text="true"
        kosmos:bsb_show_thumb_text="true"
        kosmos:bsb_thumb_color="#ffffff"
        kosmos:bsb_thumb_text_color="#cabf18"
        kosmos:bsb_touch_to_seek="true"
        kosmos:bsb_track_color="#d1cccc"
        kosmos:bsb_track_size="14dp"/>
```

#### 3.KBubbleSeekBar 关键代码

```java
public void sweepGradientInit() {
        //渐变颜色.colors和pos的个数一定要相等
        float[] pos = {0f, 0.5f, 1.0f};
        linearGradient = new LinearGradient(0, 0, lySpace / 2, lySpace / 2, colors, pos, Shader.TileMode.REPEAT);
        Matrix matrix = new Matrix();
        linearGradient.setLocalMatrix(matrix);
}
```

##### 说明：

- 控件的绘制顺序为：`onMeasure`-->`onLayout`-->`onDraw`

- 这里用到`android` `api`提供的线性渐变工具：`LinearGradient`

- - 这里，LinearGradient的构造函数内传入的几个参数
  - `(x0,y0)`: 起始渐变点坐标
  - `(x1,y1)`: 结束渐变点坐标
  - `colors[]`: 用于指定渐变的颜色值数组 
  - `positions[]`: 与渐变的颜色相对应，取值是0-1的float类型
  - `TileMode tile`：指定控件区域大于指定的渐变区域时，空白区域颜色填充方法

##### 关于渐变色的注意事项：

![demo1](https://github.com/KosmoSakura/BubbleSeekBar/blob/master/show/notice.gif)

- 1.`colors[]`与`positions[]`的长度要一一对应
- 2.`bsb_colors="#ffffffff_#ff24d1b4_#ff000000"`内的色值必须带alpha通道

#### 4.关于Thumb的阴影效果

> 关键代码：

```java
mPaint.setColor(mThumbColor);
if (isThumbOnDragging) {
    //绘制拖动中的Thumb
    //绘制拖动中的阴影：阴影半径，阴影x坐标偏移，阴影y坐标偏移，阴影颜色
    mPaint.setShadowLayer(5f, 0, 3, Color.GRAY);
    canvas.drawCircle(mThumbCenterX, yTop, mThumbRadiusOnDragging, mPaint);
} else {
     //绘制静止状态Thumb
     //绘制静止状态阴影：阴影半径，阴影x坐标偏移，阴影y坐标偏移，阴影颜色
     mPaint.setShadowLayer(5f, 0, 1, Color.GRAY);
     canvas.drawCircle(mThumbCenterX, yTop, mThumbRadius, mPaint);
}
mPaint.setShadowLayer(0, 0, 0, Color.GRAY);//关闭阴影
```

> 我尝试过用，` paint.setShader(mShader);`的方式，但已经过时，谷狗不推荐

**setShadowLayer的注意事项**

> - 用完之后记得关掉阴影效果，因为这是作为一个参数，传入Paint里面，最简单的做法就是直接把`阴影半径`设为0，即可
> - 这里我踩了一个坑，记录下，以免再犯
> - - 开始我一切写好后，却一直没有阴影效果，折腾了好久，才发现是`硬件加速`的问题
>   - setShadowLayer`只有文字绘制才支持硬件加速，其他都不支持
>   - 因此，要为此控件单独关闭硬件加速：`setLayerType(LAYER_TYPE_SOFTWARE, null);`

如图：![shader](https://github.com/KosmoSakura/BubbleSeekBar/blob/master/show/shader.png)





---

## License

```
   Copyright 2017 woxingxiao

   Licensed under the Apache License, Version 2.0 (the "License");
   you may not use this file except in compliance with the License.
   You may obtain a copy of the License at

     http://www.apache.org/licenses/LICENSE-2.0

   Unless required by applicable law or agreed to in writing, software
   distributed under the License is distributed on an "AS IS" BASIS,
   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
   See the License for the specific language governing permissions and
   limitations under the License.
```
