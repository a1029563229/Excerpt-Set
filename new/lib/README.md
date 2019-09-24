# 实用库

## Antd
```es6
// Props 类型
import * as PropTypes from "prop-types";

// ReactDOM.findDOMNode(component) 如果组件已经被挂载到 DOM 上，此方法会返回浏览器中相应的原生 DOM 元素。
// 此方法对于从 DOM 中读取值很有用，例如获取表单字段的值或者执行 DOM 检测。
// 大多数情况下，你可以绑定一个 ref 到 DOM 节点上，可以完全避免使用 findDOMNode
// 注意：findDOMNode 是一个访问底层 DOM 节点的应急方案（escape hatch）。在大多数情况下，不推荐使用该方法，因为它会破坏组件的抽象结构。
import { findDOMNode } from "react-dom";

// anim(el,animationName,function(){}); 使用这个方法可以让 el 节点播放指定 animationName 动画
import TransitionEvents from "css-animation/lib/Event";

// React.cloneElement(element, [props], [...children])
// 以 element 元素为样板克隆并返回新的 React 元素。返回元素的 props 是将新的 props 和原始元素的 props 浅层合并后的结果。
// 新的子元素将取代现有的子元素，来自原始元素的 key 和 ref 将被保留
// React.cloneElement() 几乎等同于：<element.type {...element.props} {...props}>{children}</element.type>
import { cloneElement } from "react";

// 复制组件的静态方法到新的组件
import hoistStatics from "hoist-non-react-statics";

// 不通过 es6，通过 create-react-class 来创建 React 组件
import createReactClass from 'create-react-class';

// 一个异步验证插件，用于验证值
import AsyncValidator from 'async-validator';

// 在满足条件时弹出提示
import warning from 'warning';

// _.eq(value, other) 比较两者的值是否相等
import eq from 'lodash/eq';

// scrollIntoView：滚动到指定视图的指定位置
import scrollIntoView from 'dom-scroll-into-view';

// _.has(object, path) 检查 path 是否是object对象的直接属性。
import has from 'lodash/has';

// 响应css媒体查询的轻量级javascript库
const enquire = require("enquire.js");

// omit，轻量级
import omit from 'omit.js';

// react 的动画组件，通过一些设置可以更方便的执行动画
import Animate from 'rc-animate';

// 日历组件
import RcCalendar from 'rc-calendar';

// 选择框组件
import RcSelect, { Option, OptGroup } from 'rc-select';

// 给 DOM 元素动态添加/删除/切换 class
import classes from 'component-classes';

// Menu 菜单
import { Item as MenuItem, ItemGroup as MenuItemGroup } from 'rc-menu';

// 把 children 转换为数组
import childrenToArray from 'rc-util/lib/Children/toArray';

// 键盘/键位的枚举，例如 KeyCode.ENTER 代表回车键的 keyCode
import KeyCode from 'rc-util/lib/KeyCode';

// react 轮播组件
import { Settings } from 'react-slick';

// _.debounce(func, [wait=0], [options={}]) 防抖函数
import debounce from 'lodash/debounce';

// react tooltip 插件
import RcTooltip from 'rc-tooltip';
```

## Webpack

```es6
// child_process 模块提供了衍生子进程的能力（以一种与 popen(3) 类似但不相同的方式）。 此功能主要由 child_process.spawn() 函数提供：
// 在子进程中可以执行 shell 命令，也可以生成新的 Node 进程并且调用指定模块（构建父子进程通信）
const cp = require("child_process");

// 工具类应用使用此包会加速构建速度。
require('v8-compile-cache');

// 查询更新
const updateNotifier = require('update-notifier');

// 版本管理工具
const semver = require('semver');

// 读取命令行的参数
const cmdArgs = require('command-line-args');

// JSON 格式校验器
const Ajv = require("ajv");

// require.resolve 增强版
const {
  NodeJsInputFileSystem,
  CachedInputFileSystem,
  ResolverFactory 
} = require('enhanced-resolve');

// 创建一些功能强大的钩子，类似于 发布/订阅模式
const {
	Tapable,
	SyncHook,
	SyncBailHook,
	AsyncParallelHook,
	AsyncSeriesHook
} = require("tapable");
```
