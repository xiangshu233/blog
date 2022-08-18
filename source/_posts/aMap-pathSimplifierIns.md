---
date: 2020-12-04
title: 高德地图JsApi轨迹巡航
categories: [Vue, 高德地图JsApi]
tags: [Vue, 高德Api, 轨迹巡航]
cover: https://tva2.sinaimg.cn/large/006wuklaly8h5apvxpq68j31c00u0wll.jpg
---

>本次场景需求是查询历史数据返回一组经纬度和相关数据在地图中使用轨迹巡航API巡航，期间的文本框每经过一个坐标其数值就会改变，文章末尾有demo文件，需要自取。

## 效果图
![GIF](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@a55cdd251aeefe761465684e8a34fbcf2117752e/2020/11/23/ac1d2c5e8d35574b3a917d900a05cf76.png)


## Data

{% folding child:codeblock open:false 展开详情 %}

```js
data() {
  return {
    tableData: [],  // 表格数据
    map: null, // 地图实例
    pathSimplifierIns: null, // 巡航路线实例
    navgtr: null, // 巡航器实例
    navgtrEnd: false, // 本轮巡航结束 结束为 true
    infoWindow: null, // 信息窗体实例
    // 巡航器、线路默认样式配置
    defaultRenderOpts: {
      renderAllPointsIfNumberBelow: 1,
      pathTolerance: 0,
      keyPointTolerance: 1, // 绘制中间节点的简化参数，小于0, 不绘制任何中间节点；默认设置。大于等于0，对简化过的绘制轨迹线做二次简化
      pathNavigatorStyle: {
        pathLinePassedStyle: {
          lineWidth: 5,  // 巡航器经过路线宽度
          strokeStyle: '#66AA00',
          borderWidth: 1,
          borderStyle: '#eeeeee',
          dirArrowStyle: true
        },
      },
      pathLineStyle: {
        lineWidth: 10, // 轨迹宽度
        strokeStyle: '#FA8C16',
        dirArrowStyle: true
      },
      keyPointStyle: {
        radius: 7, // 路径点大小
        fillStyle: '#576d93',
        lineWidth: 1,
        strokeStyle: '#eeeeee'
      }
    },
    // json
    res: {
      dataList: [
          {
              line_id: "ddcbe9b9857d4dcfb6ef9219a865ccc01",
              equip_id: "ddcbe9b9857d4dcfb6ef9219a865ccc01",
              line_name: "线路A",
              equip_name: "设备A",
              alarm_max: "10",
              equip_model_name: "LH007-TDL80",
              point: "117.221354,31.849226",
              data_name: "CH4",
              data_value: "7.15",
              data_unit: "%",
              status: 1
          },
          {
              line_id: "ddcbe9b9857d4dcfb6ef9219a865ccc01",
              equip_id: "ddcbe9b9857d4dcfb6ef9219a865ccc01",
              line_name: "线路A",
              equip_name: "设备A",
              alarm_max: "10",
              equip_model_name: "LH007-TDL80",
              point: "117.221432,31.847613",
              data_name: "CH4",
              data_value: "5.73",
              data_unit: "%",
              status: 1
          },
          {
              line_id: "ddcbe9b9857d4dcfb6ef9219a865ccc01",
              equip_id: "ddcbe9b9857d4dcfb6ef9219a865ccc01",
              line_name: "线路A",
              equip_name: "设备A",
              alarm_max: "10",
              equip_model_name: "LH007-TDL80",
              point: "117.221625,31.845863",
              data_name: "CH4",
              data_value: "12.43",
              data_unit: "%",
              status: 0
          },
          {
              line_id: "ddcbe9b9857d4dcfb6ef9219a865ccc01",
              equip_id: "ddcbe9b9857d4dcfb6ef9219a865ccc01",
              line_name: "线路A",
              equip_name: "设备A",
              alarm_max: "10",
              equip_model_name: "LH007-TDL80",
              point: "117.224822,31.845096",
              data_name: "CH4",
              data_value: "4.58",
              data_unit: "%",
              status: 1
          },
          {
              line_id: "ddcbe9b9857d4dcfb6ef9219a865ccc01",
              equip_id: "ddcbe9b9857d4dcfb6ef9219a865ccc01",
              line_name: "线路A",
              equip_name: "设备A",
              alarm_max: "10",
              equip_model_name: "LH007-TDL80",
              point: "117.225358,31.843085",
              data_name: "CH4",
              data_value: "7.25",
              data_unit: "%",
              status: 1
          },
          {
              line_id: "ddcbe9b9857d4dcfb6ef9219a865ccc01",
              equip_id: "ddcbe9b9857d4dcfb6ef9219a865ccc01",
              line_name: "线路A",
              equip_name: "设备A",
              alarm_max: "10",
              equip_model_name: "LH007-TDL80",
              point: "117.225337,31.840885",
              data_name: "CH4",
              data_value: "25.23",
              data_unit: "%",
              status: 0
          },
          {
              line_id: "ddcbe9b9857d4dcfb6ef9219a865ccc01",
              equip_id: "ddcbe9b9857d4dcfb6ef9219a865ccc01",
              line_name: "线路A",
              equip_name: "设备A",
              alarm_max: "10",
              equip_model_name: "LH007-TDL80",
              point: "117.221517,31.840794",
              data_name: "CH4",
              data_value: "9.03",
              data_unit: "%",
              status: 1
          },
          {
              line_id: "ddcbe9b9857d4dcfb6ef9219a865ccc01",
              equip_id: "ddcbe9b9857d4dcfb6ef9219a865ccc01",
              line_name: "线路A",
              equip_name: "设备A",
              alarm_max: "10",
              equip_model_name: "LH007-TDL80",
              point: "117.218685,31.839864",
              data_name: "CH4",
              data_value: "4.25",
              data_unit: "%",
              status: 1
          }
      ]
    },
  }
},
```
{% endfolding %}

## Template

`template` 包含一个地图实例容器，一个 `popover` 弹出框，弹出框里包含一个 `table`、五个按钮 `查询`、`开始巡航`、`暂停巡航`、`继续巡航`、和触发`popover` 弹出框的 `巡航历史` 按钮

{% folding child:codeblock open:false 展开详情 %}

```html
<template>
  <div class="map">
    <!-- popover 弹出框 -->
    <el-popover placement="bottom" width="550" trigger="click">
      <el-button
        style="margin-bottom: 20px"
        type="primary"
        @click="queryHisData"
        >查询</el-button
      >
      <div style="display: inline-block" v-if="tableData.length">
        <el-button
          style="margin-left: 10px"
          v-show="getNaviStatus == 'stop' || navgtrEnd"
          type="primary"
          icon="el-icon-video-play"
          @click="startNavgtr"
          >开始巡航</el-button
        >
        <el-button
          v-show="getNaviStatus == 'moving'"
          type="primary"
          icon="el-icon-video-pause"
          @click="pauseNavgtr"
          >暂停巡航</el-button
        >
        <el-button
          v-show="getNaviStatus == 'pause' && !navgtrEnd"
          type="primary"
          icon="el-icon-video-play"
          @click="resumeNavgtr"
          >继续巡航</el-button
        >
      </div>
      <el-table
        height="350"
        :data="tableData"
        :header-cell-style="{ 'text-align': 'center' }"
        :cell-style="{ 'text-align': 'center' }"
      >
        <el-table-column
          prop="line_name"
          label="线路名称"
        >
        </el-table-column>
        <el-table-column
          prop="equip_name"
          label="设备名称"
        >
        </el-table-column>
        <el-table-column
          prop="point"
          :show-overflow-tooltip="true"
          label="坐标"
        >
        </el-table-column>
        <el-table-column prop="data_value" label="浓度">
          <template v-slot="scope">
            <el-tag size="medium">{{ scope.row.data_value }}{{ scope.row.data_unit }}</el-tag>
          </template>
        </el-table-column>
        <el-table-column prop="status" label="状态">
          <template v-slot="scope">
            <el-tag v-if="scope.row.status === 0" type="warning">
              <i class="el-icon-warning"> 异常</i>
            </el-tag>
            <el-tag v-else type="success">
              <i class="el-icon-success"> 正常</i>
            </el-tag>
          </template>
        </el-table-column>
      </el-table>

      <el-button
        class="reference"
        slot="reference"
        type="primary"
        icon="el-icon-map-location"
        >巡航历史</el-button
      >
    </el-popover>
    <!-- 地图容器 -->
    <div id="container"></div>
  </div>
</template>
```
{% endfolding %}

## 初始化Map

安装高德

```Bash
yarn add @amap/amap-jsapi-loader --save
```

对高德地图加载器进行了简单封装。如其他页面需要引入高德API只需引入 `AMapLoader.js` 即可

新建 `AMapLoader.js`

```js
import AMapLoader from "@amap/amap-jsapi-loader";

export default function loadAMap () {
  return AMapLoader.load({
    key: "a0fe39f6c90ba997c0833b6b1cd00bfd",
    version: "2.0",
    plugins: ['AMap.InfoWindow', 'AMap.Polyline', 'AMap.CircleMarker'], // 需要使用的的插件列表
    AMapUI: {
      // 是否加载 AMapUI，缺省不加载
      version: "1.1",
      plugins: [], // 需要加载的 AMapUI ui插件
    }
  })
};
```

引入 `AMapLoader.js`

```js
import AMapLoader from "@/utils/AMapLoader";
```

在 `created()` 里初始化

```js
created() {
  AMapLoader().then(() => this.initAMap());
},
```

在 `methods()` 中书写 `initAMap()`

```js
initAMap() {
  this.map = new AMap.Map("container", {
    center: [117.224618, 31.8431],
    resizeEnable: true,
    zoom: 16,
  });
},
```

## 查询

查询触发轨迹巡航路线并展示响应的表格数据

```js
// 查询按钮
queryHisData() {
  this.tableData = this.res.dataList;		// 赋值给 table
  this.drawNavgtrPath(this.tableData);	// 将数据传给 drawNavgtrPath() 函数
},
```

## 绘制轨迹巡航

**drawNavgtrPath() 函数解释：**

- `new` 一个 `PathSimplifier` (巡航路线)，其中 `getPath` 回调用来处理 `PathSimplifier` 所需的路径坐标
- `new` 一个 `InfoWindow` (信息窗体)，打开信息窗体，设置窗体内容
- `that.pathSimplifierIns.setData([dataList])` 设置巡航路线源数据
- `that.pathSimplifierIns.createPathNavigator()` 创建一个(巡航器)，配置相应的参数
- `that.navgtr.on("move", function () {})` 监听该巡航器移动事件
- `that.infoWindow.open(that.map, that.navgtr.getPosition())`
- 在监听回调中打开信息窗体将巡航器坐标传给信息窗体就会产生信息窗体一直跟随巡航器的效果
- `this.getCursor().idx` 返回当前所处的索引位置，即要实现每经过一个坐标点就展示该点的信息数据，有了这个方法就可以用它来充当 `dataList` 的下标来达到我们的需求


{% image https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@0eaca61d91353093d6358c7fae7a555c398f30ae/2020/11/23/c495d21a7c6bbbe1a6649435bf1876cb.png %}

```js
drawNavgtrPath(dataList) {
  const that = this; // vue实例
  AMapUI.load(["ui/misc/PathSimplifier"], (PathSimplifier) => {
    if (!that.pathSimplifierIns) {
      that.pathSimplifierIns = new PathSimplifier({
        zIndex: 100,
        map: that.map, // 所属的地图实例
        getPath: (pathData, pathIndex) => {
          const lnglatList = [];
          for (const item of pathData) {
            lnglatList.push(item.point.split(","));
          }
          return lnglatList;
        },
        // hover 路径
        getHoverTitle: (pathData, pathIndex, pointIndex) => {
          if (pointIndex >= 0) {
            // point
            return `
              ${pathData[pointIndex].line_name}-${pointIndex + 1}测量点：
              ${pathData[pointIndex].data_value}${
              pathData[pointIndex].data_unit
            }
            `;
          }
          // path
          return `该线路共${pathData.length}个测量点`;
        },
        autoSetFitView: false,
        renderOptions: that.defaultRenderOpts, // 巡航器、线路默认样式配置
      });
      // 创建信息窗体
      that.infoWindow = new AMap.InfoWindow({
        offset: new AMap.Pixel(1, -30),
      });
    }

    // 设置数据
    that.pathSimplifierIns.setData([dataList]);

    // 对第一条线路（即索引 0）创建一个巡航器
    that.navgtr = that.pathSimplifierIns.createPathNavigator(0, {
      loop: false, // 循环播放
      speed: 500, // 巡航速度，单位千米/小时
      pathNavigatorStyle: {
        width: 45,
        height: 45,
        content: PathSimplifier.Render.Canvas.getImageContent(robot), // 自定义巡航器样式
        strokeStyle: null,
        fillStyle: null,
      },
    });

    // 打开信息窗体
    that.infoWindow.open(that.map, that.navgtr.getPosition());
    // 设置信息窗体内容
    that.infoWindow.setContent(
      `浓度：${dataList[0].data_value}${dataList[0].data_unit}`
    );
    // 监听移动中巡航器
    that.navgtr.on("move", function () {
      // 此处不能用箭头函数否则会改变this指向
      that.infoWindow.open(that.map, that.navgtr.getPosition());
      let idx = this.getCursor().idx; // 走到了第几个点
      let point = dataList[idx];
      point &&
        point.data_value &&
        that.infoWindow.setContent(
          `浓度：${point.data_value}${point.data_unit}`
        );
      // 通过 监听判断巡航器是否走到最后一个点来确定本轮结束
      if (idx + 1 === dataList.length) {
        that.navgtrEnd = true;
      }
    });
  });
},
```

## 巡航器开始、暂停、继续功能

效果图：

![GIF2](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@55ef268881e213eb7c6007ff716500df9b57c199/2020/11/23/61d1465f4a3b5b02042d7765ae143f6b.png)



要实现上图这种按钮效果我们只需要使用高德API提供的 `getNaviStatus()` 函数即可，在计算属性中添加 `getNaviStatus()` 函数，它会返回巡航器的 `状态`，之后将该计算属性绑定在按钮的 `v-show` 上

```js
computed: {
  getNaviStatus() {
    if (this.navgtr) {
      return this.navgtr.getNaviStatus();
    }
  },
},
```

{% image https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@b1adaf14bfd9270b3889f8171fbf18147fca14c0/2020/11/23/2ae87310dc5e9267bae3eaaa42ff73f8.png %}


使用如下方法控制巡航器 `状态` 并绑定在按钮上

```js
startNavgtr() {
  this.navgtr.start(); // 开始
  this.navgtrEnd = false;
},

pauseNavgtr() {
  this.navgtr.pause(); // 暂停
},

resumeNavgtr() {
  this.navgtr.resume(); // 继续
},
```

![image-20201123134326693](https://fastly.jsdelivr.net/gh/xiangshu233/blogAssets@f0ba56b3c99bf7fec7f73088fca0986a5a66e01e/2020/11/23/d837bfabe975e447a93fa44a2f41b15f.png)

开始之前巡航器的状态为 `stop`（停止），这里还有个变量是 `navgtrEnd` 该变量的作用是巡航 `一轮` 结束后让按钮重新显示 `开始巡航` 按钮，因为巡航器巡航完一轮后的状态为 `pause`（暂停）状态。如果 `getNaviStatus == 'stop'` 或者 `navgtrEnd==true` 显示开始按钮，那么具体怎么判断巡航器走完一轮呢？

在上述 `drawNavgtrPath()` 函数的 `move` 监听中书写如下代码：

`idx` 是巡航器经过第几个点，从 `0` 开始，`dataList` 的 `length` 从 `1` 开始计算所以要 `+1` ，如果 `===` `dataList` 的长度则表示本轮结束，设置 `that.navgtrEnd = true`，显示开始按钮。

```js
// 通过 监听判断巡航器是否走到最后一个点来确定本轮结束
if (idx + 1 === dataList.length) {
  that.navgtrEnd = true;
}
```

```html
<el-button
  style="margin-left: 10px"
  v-show="getNaviStatus == 'stop' || navgtrEnd"
  type="primary"
  icon="el-icon-video-play"
  @click="startNavgtr"
  >开始巡航</el-button
>
```

点击 `开始按钮` 后巡航器状态处于 `moving` 状态，则 `DOM` 上显示 `暂停按钮`

```html
<el-button
    v-show="getNaviStatus == 'moving'"
    type="primary"
    icon="el-icon-video-pause"
    @click="pauseNavgtr"
    >暂停巡航</el-button
>
```

点击 `暂停按钮` 后巡航器的状态是 `pause`（暂停）状态，则 `DOM` 上显示 `继续按钮`，因为巡航器走完一轮的状态是 `pause`（暂停）状态，为了防止走完显示的 `继续巡航` 按钮则需要附加 `!navgtrEnd` 条件，则 `navgtrEnd==true` 显示 `开始按钮`

```html
<el-button
    v-show="getNaviStatus == 'pause' && !navgtrEnd"
    type="primary"
    icon="el-icon-video-play"
    @click="resumeNavgtr"
    >继续巡航</el-button
>
```

## 完整 demo

>链接：https://pan.baidu.com/s/1kVRm8GcWhf3Vozn3uIxDww
>提取码：2333


## 结语

> 世上所谓的“交友”是指彼此轻蔑又相互来往，并使双方越发无趣
