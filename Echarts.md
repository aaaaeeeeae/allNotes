# echarts

## 数据集

数据集可以写在 `dataset` 中，也可以写在 `series` 中

### 映射相关

#### series

`series` 定义的是一个系列，默认每列为一个系列，可以通过设置 `seriesLayoutBy` 来指定为行

```
series:[{ type: 'bar', seriesLayoutBy: 'row' }]
```

- 什么是系列？

  - 我的理解是相同系列会在逻辑上划分为同种类型数据，因此不同系列会被分为同一个组被一起展示。

    如下图所展示的那样，以列为一个系列，一列都表示相同年份的数据

```
option = {
  legend: {},
  tooltip: {},
  dataset: {
    // 提供一份数据。
    source: [
      ['product', '2015', '2016', '2017'],
      ['Matcha Latte', 43.3, 85.8, 93.7],
      ['Milk Tea', 83.1, 73.4, 55.1],
      ['Cheese Cocoa', 86.4, 65.2, 82.5],
      ['Walnut Brownie', 72.4, 53.9, 39.1]
    ]
  },
  // 声明一个 X 轴，类目轴（category）。默认情况下，类目轴对应到 dataset 第一列。
  xAxis: { type: 'category' },
  // 声明一个 Y 轴，数值轴。
  yAxis: {},
  // 声明多个 bar 系列，默认情况下，每个系列会自动对应到 dataset 的每一列。
  series: [{ type: 'bar' }, { type: 'bar' }, { type: 'bar' }]
};
```

![截屏2023-07-18 11.31.12](/Users/ache/Desktop/photos/截屏2023-07-18 11.31.12.png)

#### dimension

`dimension` 的定义其实和 `series` 的定义是一样的，只是不同说法罢了

#### 数据到图形的映射

- 自动映射，默认为列，用`seriesLayoutBy` 修改默认映射是列还是行

- encode设置映射

  ```
  var option = {
    dataset: {
      source: [
        ['score', 'amount', 'product'],
        [89.3, 58212, 'Matcha Latte'],
        [57.1, 78254, 'Milk Tea'],
        [74.4, 41032, 'Cheese Cocoa'],
      ]
    },
    xAxis: {},
    yAxis: { type: 'category' },
    series: [
      {
        type: 'bar',
        encode: {
          // 将 "amount" 列映射到 X 轴。
          x: 'amount',
          // 将 "product" 列映射到 Y 轴。
          y: 'product'
        }
      }
    ]
  };
  ```


### 数据转换

使用 transform 可完成数据集的转换

- 可设置属性:  `fromDatasetIndex` 或 `fromDatasetId ` 去指定 `transform` 的输入，来自于哪个 `dataset` ，如 `fromDatasetIndex: 0`
- 使用 `config` 去指定

```
var option = {
  dataset: [
    {
      source: [
        ['Product', 'Sales', 'Price', 'Year'],
        ['Cake', 123, 32, 2011],
        ['Cereal', 231, 14, 2011],
        ['Tofu', 235, 5, 2011],
      ]
    },
    {
      transform: {
        type: 'filter',
        config: { dimension: 'Year', value: 2011 }
      }
    },
    {
      transform: {
        type: 'filter',
        config: { dimension: 'Year', value: 2012 }
      }
    },
    {
      transform: {
        type: 'filter',
        config: { dimension: 'Year', value: 2013 }
      }
    }
  ],
  series: [
    {
      type: 'pie',
      radius: 50,
      center: ['25%', '50%'],
      // 引用了上述 2011 年的结果。
      datasetIndex: 1
    },
    {
      type: 'pie',
      radius: 50,
      center: ['50%', '50%'],
      datasetIndex: 2
    },
    {
      type: 'pie',
      radius: 50,
      center: ['75%', '50%'],
      datasetIndex: 3
    }
  ]
};
```

- 当几个 transform 被声明成 array ，则形成了链式结构（前一个 transform 的输出是后一个 transform 的输入）

  ```
  option = {
    dataset: [{
        source: [
          // 原始数据
        ]},
      {
        transform: [
          {
            type: 'filter',
            config: { dimension: 'Product', value: 'Tofu' }
          },
          {
            type: 'sort',
            config: { dimension: 'Year', order: 'desc' }
          }
        ]
      }
    ],
    series: {
      type: 'pie',
      // 这个系列引用上述 transform 的结果。
      datasetIndex: 1
    }
  };
  ```

  