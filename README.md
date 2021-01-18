# z-paging

> 【uni-app自动分页器】超简单！仅需三步轻松完成完整分页逻辑(下拉刷新、上拉加载更多)，分页全自动处理。支持自定义加载更多的文字或整个view，自定义下拉刷新样式，自动管理空数据view等。

## 在DCloud插件市场中访问：https://ext.dcloud.net.cn/plugin?id=3935

# 基本使用

* ①在`<template>` 中使用@query绑定js中分页请求的方法(`z-paging`会将计算好的pageNo和pageSize两个参数传递到此方法中)，然后通过 :list.sync绑定列表for循环的list。
* ②在请求结果回调中，通过调用`z-paging`的`addData()`方法，将请求返回的数组传递给`z-paging`处理
* ③在`onLoad()`中调用`z-paging`的`reload()`方法。

#### 【注意】z-paging必须有确定的高度！否则上拉加载更多将无法触发，请确保已设置z-paging的高度，并且z-paging的父节点也有确定的高度！！

```html
<template>
    <view class="content">
        <z-paging ref="paging" @query="queryList" :list.sync="dataList" style="height: 100%">
            <!-- list数据，建议像下方这样在item外层套一个view，而非直接for循环item，因为slot插入有数量限制 -->
            <view>
                <view class="item" v-for="(item,index) in dataList">
                    <view class="item-title">{{item.title}}</view>
                </view>
            </view>
        </z-paging>
    </view>
</template>

<script>
    export default {
        data() {
            return {
                dataList: [],
            };
        },
        onLoad() {
            //在Page的onLoad()方法中无法同步获取this.$refs，需要延时1毫秒再调用
            setTimeout(() => {
                this.$refs.paging.reload();
            }, 1);
        },
        methods: {
            queryList(pageNo, pageSize) {
                this.$request.queryList(pageNo, pageSize, (data) => {
                    this.$refs.paging.addData(data);
                });
            },
        },
    };
</script>

<style scoped>
    /* 注意，父元素需要固定高度，z-paging的height:100%才会生效 */
    page {
        height: 100%;
    }

    .content {
        height: 100%;
    }
</style>
```

| 效果演示                                                     |
| ------------------------------------------------------------ |
| ![](http://www.zxlee.cn/github/uni-z-paging/uni-z-paging.gif) |



## 设置自定义emptyView组件

* 设置自定义emptyView组件，非必须。空数据时会自动展示空数据组件，不需要自己处理

```html
<z-paging ref="paging" @query="queryList" :list.sync="dataList" style="height: 100%">
    <!-- 设置自己的emptyView组件，非必须。空数据时会自动展示空数据组件，不需要自己处理 -->
    <empty-view slot="empty"></empty-view>

    <view>
        <view class="item" v-for="(item,index) in dataList">
            <view class="item-title">{{item.title}}</view>
        </view>
    </view>
</z-paging>
```

## 自定义加载更多各个状态的描述文字

* 以修改【没有更多了】状态描述文字为例(将默认的"没有更多了"修改为"我也是有底线的！")

```html
<z-paging ref="paging" @query="queryList" :list.sync="dataList" style="height: 100%">
    <!-- 设置自己的emptyView组件，非必须。空数据时会自动展示空数据组件，不需要自己处理 -->
    <empty-view slot="empty"></empty-view>

    <view>
        <view class="item" v-for="(item,index) in dataList">
            <view class="item-title">{{item.title}}</view>
        </view>
    </view>
</z-paging>
```

## 自定义加载更多各个状态的描述view

* 以修改【没有更多了】状态描述view为例

```html
<z-paging ref="paging" @query="queryList" :list.sync="dataList" style="height: 100%">
    <view>
        <view class="item" v-for="(item,index) in dataList">
            <view class="item-title">{{item.title}}</view>
        </view>
    </view>
    <view style="background-color: red" slot="loadingMoreNoMore">这是完全自定义的没有更多数据view</view>
</z-paging
```



# API

## Props 

|              参数              |                             说明                             |       类型       |         默认值         |   可选值    |
| :----------------------------: | :----------------------------------------------------------: | :--------------: | :--------------------: | :---------: |
|        default-page-no         |                         自定义pageNo                         | String \| Number |           1            |      -      |
|       default-page-size        |                        自定义pageSize                        | String \| Number |           15           |      -      |
|      loading-more-enabled      | 是否启用加载更多数据(含滑动到底部加载更多数据和点击加载更多数据)，默认为是 |     Boolean      |          true          |    false    |
| to-bottom-loading-more-enabled |                是否启用滑动到底部加载更多数据                |     Boolean      |          true          |    false    |
|       loading-more-text        |  自定义底部加载更多文字(当需要不同加载状态固定文字时才使用)  |      String      |           -            |      -      |
|   loading-more-default-text    |                     滑动到底部"默认"文字                     |      String      |      点击加载更多      |      -      |
|   loading-more-loading-text    |                    滑动到底部"加载中"文字                    |      String      |       加载中...        |      -      |
|   loading-more-no-more-text    |                   滑动到底部"没有更多"文字                   |      String      |       没有更多了       |      -      |
|     loading-more-fail-text     |                   滑动到底部"加载失败"文字                   |      String      | 加载失败，点击重新加载 |      -      |
| show-default-loading-moretext  |                  是否显示默认的加载更多text                  |     Boolean      |          true          |    false    |
|        hide-empty-view         |                     是否强制隐藏空数据图                     |     Boolean      |         false          |    true     |
|         show-scrollbar         |                      控制是否出现滚动条                      |     Boolean      |         false          |    true     |
|       enable-back-to-top       | iOS点击顶部状态栏、安卓双击标题栏时，滚动条返回顶部，只支持竖向 |     Boolean      |         false          |    true     |
|       refresher-enabled        |                    是否开启自定义下拉刷新                    |     Boolean      |          true          |    false    |
|      refresher-threshold       |                    设置自定义下拉刷新阈值                    | String \| Number |           45           |      -      |
|    refresher-default-style     | 设置自定义下拉刷新默认样式，支持设置 black，white，none，none 表示不使用默认样式 |      String      |         black          | white、none |

## Slot

| 名称               | 说明                                     |
| :----------------- | ---------------------------------------- |
| empty              | 自定义空数据占位view                     |
| loadingMoreDefault | 自定义滑动到底部"默认"状态的view         |
| loadingMoreLoading | 自定义滑动到底部"加载中"状态的view       |
| loadingMoreNoMore  | 自定义滑动到底部"没有更多数据"状态的view |
| loadingMoreFail    | 自定义滑动到底部"加载失败"状态的view     |

## Events

* 监听组件方法(自定义扩展，一般无需使用)

| 事件名              | 说明                 | 回调参数                                            |
| ------------------- | -------------------- | --------------------------------------------------- |
| loadingStatusChange | 分页加载状态改变     | value:0-默认状态 1.加载中 2.没有更多数据 3.加载失败 |
| onRefresh           | 自定义下拉刷新被触发 | -                                                   |
| onRestore           | 自定义下拉刷新被复位 | -                                                   |

* 主动调用组件方法 (假设给z-paging设置ref="paging"，则通过this.$refs.paging.xxx()方式调用)

  注意：在Page的onLoad()方法中无法同步获取this.$refs，请加一个setTimeOut延时1毫秒再调用

| 方法名  | 说明                                                         | 参数                                                     |
| ------- | ------------------------------------------------------------ | -------------------------------------------------------- |
| reload  | 重新加载分页数据，pageNo恢复为默认值，相当于下拉刷新的效果   | -                                                        |
| addData | 请求结束(成功或者失败)调用此方法，将请求的结果传递给z-paging处理 | Value1:请求结果数组；value2:是否请求成功，不填默认为true |
