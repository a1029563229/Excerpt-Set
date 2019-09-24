# 阅读源码笔记合集

## Antd

```tsx
// 返回一个字符串数组的方法，用于定义枚举类型
export const tuple = <T extends string[]>(...args: T) => args;

class Button extends React.Component<ButtonProps, ButtonState> {
  //...
  // 定义了 Props 类型
  static propTypes = {
    type: PropTypes.string,
    // 指定 prop 只能是特定的值，指定它为枚举类型
    shape: PropTypes.oneOf(ButtonShapes),
    size: PropTypes.oneOf(ButtonSizes),
    htmlType: PropTypes.oneOf(ButtonHTMLTypes),
    onClick: PropTypes.func,
    // 一个对象可以是几种几种类型中的任意一个类型
    loading: PropTypes.oneOfType([PropTypes.bool, PropTypes.object]),
    className: PropTypes.string,
    icon: PropTypes.string,
    block: PropTypes.bool
  };

  // getDerivedStateFromProps 会在调用 render 方法之前调用，并且在初始挂载及后续更新时都会被调用。
  // 它应该返回一个对象来更新 state，如果返回 null 则不更新任何内容
  // 此方法适用于罕见的用例，即 state 的值在任何时候都取决于 props
  static getDerivedStateFromProps(
    nextProps: ButtonProps,
    prevState: ButtonState
  ) {
    // 如果 loading 发生改变并且类型为 Boolean 值，则更新 state
    if (nextProps.loading instanceof Boolean) {
      return {
        ...prevState,
        loading: nextProps.loading
      };
    }
    return null;
  }

  isNeedInserted() {
    const { icon, children } = this.props;
    // React.Children.count 返回 children 中的组件总数量，等同于通过 map 或 forEach 调用回调函数的次数
    return React.Children.count(children) === 1 && !icon;
  }
  //...
}

// Where el is the DOM element you'd like to test for visibility
function isHidden(element: HTMLElement) {
  if (process.env.NODE_ENV === "test") {
    return false;
  }

  // 元素是否存在
  // offsetParent：在 Webkit 中，如果元素为隐藏的（该元素或其祖先元素的 style.display 为 "none"），或者该元素的 style.position 被设为 "fixed"，则该属性返回 null。
  return !element || element.offsetParent === null;
}

// Window.getComputedStyle()方法返回一个对象，该对象在应用活动样式表并解析这些值可能包含的任何基本计算后报告元素的所有CSS属性的值。
// 私有的CSS属性值可以通过对象提供的API或通过简单地使用CSS属性名称进行索引来访问。
// 这里是按照优先级获取 node 的几个主色调
const waveColor =
  getComputedStyle(node).getPropertyValue("border-top-color") || // Firefox Compatible
  getComputedStyle(node).getPropertyValue("border-color") ||
  getComputedStyle(node).getPropertyValue("background-color");
this.clickWaveTimeoutId = window.setTimeout(
  () => this.onClick(node, waveColor),
  0
);

export default class Row extends React.Component<RowProps, RowState> {
  // ...
  renderRow = ({ getPrefixCls }: ConfigConsumerProps) => {
    // ...
    // 取 gutter 赋值给 style 中的 marginLeft 和 marginRight
    // 这里是赋值负数，暂时不太清楚用途是什么，效果是可以让父级的宽度更宽
    // 感叹号是非null和非undefined的类型断言
    const rowStyle =
      gutter! > 0
        ? {
            marginLeft: gutter! / -2,
            marginRight: gutter! / -2,
            ...style
          }
        : style;
    const otherProps = { ...others };
    delete otherProps.gutter;
    return (
      // 通过 RowContext.Provider（通过 React.createContext 创建） 组件的 value 属性将 gutter 传递给子组件（col）
      // React.createContext：创建一个 Context 对象。当 React 渲染一个订阅了这个 Context 对象的组件，
      // 这个组件会从组件树中离自身最近的那个匹配的 Provider 中读取到当前的 context 值
      // Context.Provider：每个 Context 对象都会返回一个 Provider React 组件，它允许消费组件订阅 context 的变化。
      // Provider 接收一个 value 属性，传递给消费组件。一个 Provider 可以和多个消费组件有对应关系。多个 Provider 也可以嵌套使用，里层的会覆盖外层的数据。
      // 当 Provider 的 value 值发生变化时，它内部的所有消费组件都会重新渲染。
      // Provider 及其内部 consumer（消费者） 组件都不受制于 shouldComponentUpdate 函数，因此当 consumer 组件在其祖先组件退出更新的情况下也能更新。
      // 通过新旧值检测来确定变化，使用了与 Object.is 相同的算法。
      // Context.Consumer：这里，React 组件也可以订阅到 context 变更。这能让你在函数式组件中完成订阅 context。
      // 这需要函数作为子元素（function as a child）这种做法。这个函数接收当前的 context 值返回一个 React 节点。
      // 传递给函数的 value 值等同于往上组件树这个 context 最近的 Provider 提供的 value 值。
      // 如果没有对应的 Provider，value 参数等同于传递给 createContext() 的 defaultValue。
      <RowContext.Provider value={{ gutter }}>
        <div {...otherProps} className={classes} style={rowStyle}>
          {children}
        </div>
      </RowContext.Provider>
    );
  };

  render() {
    return <ConfigConsumer>{this.renderRow}</ConfigConsumer>;
  }
}

// 利用 generator 进行参数控制
// 在返回的函数中传入主体
// 属于函数柯里化和装饰器模式
function generator({ suffixCls, tagName }: GeneratorProps) {
  return (BasicComponent: React.ComponentClass<BasicPropsWithTagName>): any => {
    return class Adapter extends React.Component<BasicProps, any> {
      static Header: any;
      static Footer: any;
      static Content: any;
      static Sider: any;

      renderComponent = ({ getPrefixCls }: ConfigConsumerProps) => {
        const { prefixCls: customizePrefixCls } = this.props;
        const prefixCls = getPrefixCls(suffixCls, customizePrefixCls);

        return (
          <BasicComponent
            prefixCls={prefixCls}
            tagName={tagName}
            {...this.props}
          />
        );
      };

      render() {
        return <ConfigConsumer>{this.renderComponent}</ConfigConsumer>;
      }
    };
  };
}

// 通过使用 onFieldsChange 与 mapPropsToFields，可以把表单的数据存储到上层组件或者 Redux、dva 中，更多可参考 rc-form 示例。
// 注意：mapPropsToFields 里面返回的表单域数据必须使用 Form.createFormField 包装。
const CustomizedForm = Form.create({
  name: "global_state",
  onFieldsChange(props, changedFields) {
    props.onChange(changedFields);
  },
  mapPropsToFields(props) {
    return {
      username: Form.createFormField({
        ...props.username,
        value: props.username.value
      })
    };
  },
  onValuesChange(_, values) {
    console.log(values);
  }
})(props => {
  const { getFieldDecorator } = props.form;
  return (
    <Form layout="inline">
      <Form.Item label="Username">
        {getFieldDecorator("username", {
          rules: [{ required: true, message: "Username is required!" }]
        })(<Input />)}
      </Form.Item>
    </Form>
  );
});

class Demo extends React.Component {
  state = {
    fields: {
      username: {
        value: "benjycui"
      }
    }
  };

  handleFormChange = changedFields => {
    this.setState(({ fields }) => ({
      fields: { ...fields, ...changedFields }
    }));
  };

  render() {
    const { fields } = this.state;
    return (
      <div>
        <CustomizedForm {...fields} onChange={this.handleFormChange} />
        <pre className="language-bash">{JSON.stringify(fields, null, 2)}</pre>
      </div>
    );
  }
}

class Field {
  constructor(fields) {
    // 将 fields 属性赋值给当前类
    // 批量赋值
    Object.assign(this, fields);
  }
}

// 依次降序判断入参，智能判断
// 猜测是用于 form.validateFields([fieldNames: string[]], [options: object], callback(errors, values))
export function getParams(ns, opt, cb) {
  let names = ns;
  let options = opt;
  let callback = cb;
  if (cb === undefined) {
    if (typeof names === "function") {
      callback = names;
      options = {};
      names = undefined;
    } else if (Array.isArray(names)) {
      if (typeof options === "function") {
        callback = options;
        options = {};
      } else {
        options = options || {};
      }
    } else {
      callback = options;
      options = names || {};
      names = undefined;
    }
  }
  return {
    names,
    options,
    callback
  };
}

// 利用 lastIndexOf 来判断是否是以 prefix 为前缀的字符串
export function startsWith(str, prefix) {
  return str.lastIndexOf(prefix, 0) === 0;
}

// 利用 reduce 重置组件（对象）的值，并返回改变后的对象
return names.reduce((acc, name) => {
  const field = fields[name];
  if (field && "value" in field) {
    acc[name] = {};
  }
  return acc;
}, {});

getNestedFields(names, getter) {
    const fields = names || this.getValidFieldsName();
    // 利用 reduce + lodash 的 set 方法，由数组返回了一个新的对象
    return fields.reduce((acc, f) => set(acc, f, getter(f)), {});
}

// 装饰器模式的另一个作用是创建一个属于自己的作用域，在作用域范围内可以访问一些变量
componentWillReceiveProps(nextProps) {
  if (mapPropsToFields) {
    this.fieldsStore.updateFields(mapPropsToFields(nextProps));
  }
}

// 支持 callback 和 Promise 的两种调用方法
const oldCb = callback;
callback = (errors, values) => {
  if (oldCb) {
    oldCb(errors, values);
  } else if (errors) {
    reject({ errors, values });
  } else {
    resolve(values);
  }
};

// PropTypes.shape 可以指定一个对象由特定的类型值组成
const formShape = PropTypes.shape({
  getFieldsValue: PropTypes.func,
  getFieldValue: PropTypes.func,
  getFieldInstance: PropTypes.func,
  setFieldsValue: PropTypes.func,
  setFields: PropTypes.func,
  setFieldsInitialValue: PropTypes.func,
  getFieldDecorator: PropTypes.func,
  getFieldProps: PropTypes.func,
  getFieldsError: PropTypes.func,
  getFieldError: PropTypes.func,
  isFieldValidating: PropTypes.func,
  isFieldsValidating: PropTypes.func,
  isFieldsTouched: PropTypes.func,
  isFieldTouched: PropTypes.func,
  isSubmitting: PropTypes.func,
  submit: PropTypes.func,
  validateFields: PropTypes.func,
  resetFields: PropTypes.func,
});

// 获取指定样式，这个样式是渲染之后的样式
function computedStyle(el, prop) {
  const getComputedStyle = window.getComputedStyle;
  const style =
    // If we have getComputedStyle
    getComputedStyle ?
      // Query it
      // TODO: From CSS-Query notes, we might need (node, null) for FF
      getComputedStyle(el) :

      // Otherwise, we are in IE and use currentStyle
      el.currentStyle;
  if (style) {
    return style
      [
      // Switch to camelCase for CSSOM
      // DEV: Grabbed from jQuery
      // https://github.com/jquery/jquery/blob/1.9-stable/src/css.js#L191-L194
      // https://github.com/jquery/jquery/blob/1.9-stable/src/core.js#L593-L597
        prop.replace(/-(\w)/gi, (word, letter) => {
          return letter.toUpperCase();
        })
      ];
  }
  return undefined;
}

// 父子关系可以使用 while 实现递归查询
while ((nodeName = node.nodeName.toLowerCase()) !== 'body') {
  const overflowY = computedStyle(node, 'overflowY');
  // https://stackoverflow.com/a/36900407/3040605
  if (
    node !== n &&
      (overflowY === 'auto' || overflowY === 'scroll') &&
      node.scrollHeight > node.clientHeight
  ) {
    return node;
  }
  node = node.parentNode;
}

const node = ReactDOM.findDOMNode(instance);
// Element.getBoundingClientRect()方法返回元素的大小及其相对于视口的位置。
// rect 是一个具有四个属性left、top、right、bottom 的 DOMRect 对象
// rect 的值是相对视口的，而不是绝对的，所以可能会因为随着滚动位置的变化而出现负数
const top = node.getBoundingClientRect().top;
// 获取节点 DOM，通过比较 top 的大小来判断距离顶部最近的一个节点
if (node.type !== 'hidden' && (firstTop === undefined || firstTop > top)) {
  firstTop = top;
  firstNode = node;
}

// React.isValidElement(object) 验证对象是否为 React 元素，返回值为 true 或 false
if (React.isValidElement(e)) {
  node = e;
} else if (React.isValidElement(e.message)) {
  node = e.message;
}

// 这里没有 children ，是如何把子节点显示出来的？
// 原生 DOM 就有 children 属性，children 属性就是子节点
return <form {...formProps} className={formClassName} />;

// keyCode 13 是回车键
if (e.keyCode === 13 && onPressEnter) {
  onPressEnter(e);
}

// 当组件非完全受控组件时，取传入的 value 值
if (!('value' in this.props)) {
  this.setState({ value }, callback);
}
// 组件内部处理了值以后，再通知给外部
// 通过 getDerivedStateFromProps 又可以让组件变成完全受控组件
const { onChange } = this.props;
if (onChange) {
  // ...
}

// 很多地方都调用一个显式方法来获取 props 的值
export function getPropValue(child: Option, prop?: any) {
  if (prop === 'value') {
    return getValuePropValue(child);
  }
  return child.props[prop];
}

// 使用方法来做判断，与 vue 同出一辙，这应该是一种比较流行的编程思想
export function isMultiple(props: Partial<ISelectProps>) {
  return props.multiple;
}

// 生成 uuid 的方法
export function generateUUID(): string {
  if (process.env.NODE_ENV === 'test') {
    return 'test-uuid';
  }
  let d = new Date().getTime();
  const uuid = 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, c => {
    // tslint:disable-next-line:no-bitwise
    const r = (d + Math.random() * 16) % 16 | 0;
    d = Math.floor(d / 16);
    // tslint:disable-next-line:no-bitwise
    return (c === 'x' ? r : (r & 0x7) | 0x8).toString(16);
  });
  return uuid;
}

// 使用一个 noop 函数作为函数类 props 的默认值，可以在其他地方减少一些判断的逻辑
const noop = () => null;

class Select extends React.Component<Partial<ISelectProps>, ISelectState> {
  // ...
  
  // 在类中可以挂载一些静态方法，用于在类的内部使用（示意与类关联性较高）
  public static getOptionsFromChildren = (
    children: Array<React.ReactElement<any>>,
    options: any[] = [],
  ) => {
    React.Children.forEach(children, child => {
      if (!child) {
        return;
      }
      const type = (child as React.ReactElement<any>).type as any;
      if (type.isSelectOptGroup) {
        Select.getOptionsFromChildren((child as React.ReactElement<any>).props.children, options);
      } else {
        options.push(child);
      }
    });
    return options;
  };

  // ...

  {/* React.cloneElement 被大量使用，应该显式使用该函数，而不是 <Component {...oldProps} {...newProps} /> */}
  {React.cloneElement(inputElement, {
    ref: this.saveInputRef,
    onChange: this.onInputChange,
    onKeyDown: chaining(
      this.onInputKeyDown,
      inputElement.props.onKeyDown,
      this.props.onInputKeyDown,
    ),
    value: this.state.inputValue,
    disabled: props.disabled,
    className: inputCls,
  })}
}

// 获取浏览器支持的 style 属性
window.document.documentElement.style

// createEvent 创建一个指定类型的事件。其返回的对象必须先初始化并可以被传递给 element.dispatchEvent
// 创建事件
var event = document.createEvent('Event');

// 定义事件名为'build'.
event.initEvent('build', true, true);

// 监听事件
elem.addEventListener('build', function (e) {
  // e.target matches elem
}, false);

// 触发对象可以是任何元素或其他事件目标
elem.dispatchEvent(event);

// 获取元素的上滚距离
getCurrentScrollTop = () => {
  const getTarget = this.props.target || getDefaultTarget;
  const targetNode = getTarget();
  if (targetNode === window) {
    return window.pageYOffset || document.body.scrollTop || document.documentElement!.scrollTop;
  }
  return (targetNode as HTMLElement).scrollTop;
};
```

## React

```tsx
// 显式获取类名
const constructor = publicInstance.constructor;
const componentName =
  (constructor && (constructor.displayName || constructor.name)) ||
  "ReactClass";

class User {
  constructor() {}
}
const user = new User();
console.log(user.constructor.name); // User

// component.forceUpdate(callback);
// 默认情况下，当组件的 state 或 props 发生变化时，组件将重新渲染。
// 如果 render() 方法依赖于其他数据，则可以调用 forceUpdate() 强制让组件重新渲染。
// 调用 forceUpdate() 以致使组件调用 render() 方法，此操作会跳过该组件的 shouldComponentUpdate()。
// 但其子组件会触发正常的生命周期方法，包括 shouldComponentUpdate() 方法。如果标记发生变化，React 仍将只更新 DOM。
// 通常你应该避免使用 forceUpdate()，尽量在 render() 中使用 this.props 和 this.state。
Component.prototype.forceUpdate = function(callback) {
  this.updater.enqueueForceUpdate(this, callback, 'forceUpdate');
};

// 在 DEV 环境利用 getter 弹出警告信息
const defineDeprecationWarning = function(methodName, info) {
  Object.defineProperty(Component.prototype, methodName, {
    get: function() {
      lowPriorityWarning(
        false,
        '%s(...) is deprecated in plain JavaScript React classes. %s',
        info[0],
        info[1],
      );
      return undefined;
    },
  });
};

// 重命名导出 
// originalModuleName as newModuleName
export {
  forEachChildren as forEach,
  mapChildren as map,
  countChildren as count,
  onlyChild as only,
  toArray,
};

// React.Fragment
// React.Fragment 组件能够在不额外创建 DOM 元素的情况下，让 render() 方法中返回多个元素。
// 你也可以使用其简写语法 <></>
<React.Fragment></React.Fragment>

// 相当于 <type(div || span || ...) />
// 创建并返回指定类型的新 React 元素。
// 其中的类型参数既可以是标签名字符串（如 'div' 或 'span'），也可以是 React 组件类型（class 组件或函数组件），或是 React fragment 类型。
// 使用 JSX 编写的编码将会被转换为使用 React.createElement() 的形式。如果使用了 JSX 方式，那么一般来说就不需要直接调用 React.createElement().
// 相当于执行 render() 函数，最后执行的就是 React.createElement()
React.createElement(
  type, // component.type 字符串（如果是 function 则自动被转为了 type.displayName || type.name || 'Unknown'）
  [props],
  [...children]
)

// React.createElement 接受的 type 可以是字符串，也可以是一个类（函数）
// React.cloneElement 接受的 element 一定是一个实例，这样才能获取到实例的属性

// React.cloneElement()
// 以 element 元素为样板克隆并返回新的 React 元素。返回元素的 props 是将新的 props 与原始元素的 props 浅层合并后的结果。
// 新的子元素将取代现有的子元素，而来自原始元素的 key 和 ref 将被保留。
// React.cloneElement() 几乎等同于：
// <element.type {...element.props} {...props}>{children}</element.type>
// 但是，这也保留了组件的 ref。这意味着当通过 ref 获取子节点时，你将不会意外地从你祖先节点上窃取它。相同的 ref 将添加到克隆后的新元素中。
React.cloneElement(
  element, // 实例，ReactElement，由 React.createElement() 返回的对象
  [props],
  [...children]
)

// React.createContext
// 创建一个 Context 对象。当 React 渲染一个订阅了这个 Context 对象的组件，这个组件会从组件树中离自身最近的那个匹配的 Provider 中读取到当前的 context 值。
// 只有当组件所处的树中没有匹配到 Provider 时，其 defaultValue 参数才会生效。这有助于在不使用 Provider 包装组件的情况下对组件进行测试。
// 其内部实现就是创建了一个 context 树，树上有 Provider 和 Consumer，Consumer 又指向 context，从而形成一个闭环，可以达到访问同一个作用域中的 context 的效果。
// 在渲染节点时应该还有一些处理，比如将 Provider 中的 value 值赋值给 context，而 Consumer 接收到值后进行更新
const MyContext = React.createContext(defaultValue);

// Context.Provider
// 每个 Context 对象都会返回一个 Provider React 组件，它允许消费组件订阅 context 的变化。
// Provider 接收一个 value 属性，传递给消费组件。一个 Provider 可以和多个消费组件有对应关系。多个 Provider 也可以嵌套使用，里层的会覆盖外层的数据。
// 当 Provider 的 value 值发生变化时，它内部的所有消费组件都会重新渲染。Provider 及其内部 consumer 组件都不受制于 shouldComponentUpdate 函数，因此当 consumer 组件在其祖先组件退出更新的情况下也能更新。
// 通过新旧值检测来确定变化，使用了与 Object.is 相同的算法
// <MyContext.Provider value={/* 某个值 */}>

// class.contextType
// 挂载在 class 上的 contextType 属性会复制为一个由 React.createContext() 创建的 Context 对象。
// 这能让你使用 this.context 来消费最近 Context 的那个值。你可以在任何生命周期中访问到它，包括 render 函数中。
class MyClass extends React.Component {
  componentDidMount() {
    let value = this.context;
    /* 在组件挂载完成后，使用 MyContext 组件的值来执行一些有副作用的操作 */
  }
  componentDidUpdate() {
    let value = this.context;
    /* ... */
  }
  componentWillUnmount() {
    let value = this.context;
    /* ... */
  }
  render() {
    let value = this.context;
    /* 基于 MyContext 组件的值进行渲染 */
  }
}
MyClass.contextType = MyContext;

// Context.Consumer
// 这里，React 组件也可以订阅到 context 变更，这能让你在函数式组件中完成订阅 context。
<MyContext.Consumer>
  {value => /* 基于 context 值进行渲染*/}
</MyContext.Consumer>

// React.Suspense
// Suspense 使得组件可以等待“等待”某些操作结束后，再进行渲染。目前，Suspense 仅支持的使用场景是：通过 React.lazy 动态加载组件。
// 它将在未来支持其他使用场景，如数据获取等。
// React.Suspense 可以指定加载指示器（loading indicator），以防其组件树中的某些子组件尚未具备渲染条件。
// 目前，懒加载组件是 <React.Suspense> 支持的唯一用例：
// 该组件是动态加载的
const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    // 显示 <Spinner> 组件直至 OtherComponent 加载完成
    <React.Suspense fallback={<Spinner />}>
      <div>
        <OtherComponent />
      </div>
    </React.Suspense>
  );
}

// React.lazy
// React.lazy 和 Suspense 技术还不支持服务端渲染。如果你想要在使用服务端渲染的应用中使用，我们推荐 Loadable Components 这个库。
// React.lazy 函数能让你像渲染常规组件一样处理动态引入（的组件）。
const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    <div>
      <OtherComponent />
    </div>
  );
}

// React.forwardRef
// React.forwardRef 会创建一个 React 组件，这个组件能够将其接受的 ref 属性转发到其组件树下的另一个组件中。在下面两种场景中特别有用：
// 转发 refs 到 DOM 组件 || 在高阶组件中转发 refs
const FancyButton = React.forwardRef((props, ref) => (
  <button ref={ref} className="FancyButton">
    {props.children}
  </button>
));

// You can now get a ref directly to the DOM button:
const ref = React.createRef();
<FancyButton ref={ref}>Click me!</FancyButton>;

// React.memo 为高阶组件。它与 React.PureComponent 非常相似，但它适用于函数组件，但不适用于 class 组件。
const MyComponent = React.memo(function MyComponent(props) {
  /* 使用 props 渲染 */
});

// React Hook
// Hook 是一些可以让你在函数组件里“钩入” React state 及生命周期等特性的函数。
// Hook 使你在非 class 的情况下可以使用更多的 React 特性。 从概念上讲，React 组件一直更像是函数。而 Hook 则拥抱了函数，同时也没有牺牲 React 的精神原则。Hook 提供了问题的解决方案，无需学习复杂的函数式或响应式编程技术。
// 我们准备让 Hook 覆盖所有 class 组件的使用场景，但是我们将继续为 class 组件提供支持。在 Facebook，我们有成千上万的组件用 class 书写，我们完全没有重写它们的计划。相反，我们开始在新的代码中同时使用 Hook 和 class。
// 通过使用 Hook，你可以把组件内相关的副作用组织在一起（例如创建订阅及取消订阅），而不要把它们拆分到不同的生命周期函数里。
function FriendStatusWithCounter(props) {
  const [count, setCount] = useState(0);
  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });

  const [isOnline, setIsOnline] = useState(null);
  useEffect(() => {
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });

  function handleStatusChange(status) {
    setIsOnline(status.isOnline);
  }
  // ...

// Hook 使用规则
// Hook 就是 Javascript 函数，但是使用它们会有两个额外的规则：
// 只能在函数最外层调用 Hook。不要在循环、条件判断或子函数中调用。
// 只能在 React 的函数组件中调用 Hook。不要在其他 Javascript 函数中调用。

// Effect Hook 可以让你在函数组件中执行副作用操作
// 如果你熟悉 React class 的生命周期函数，你可以把 useEffect Hook 看做 componentDidMount，componentDidUpdate 和 componentWillUnmount 这三个函数的组合。
// useEffect 会在每次渲染后都执行吗？ 是的，默认情况下，它在第一次渲染之后和每次更新之后都会执行。
// 与 componentDidMount 或 componentDidUpdate 不同，使用 useEffect 调度的 effect 不会阻塞浏览器更新屏幕，这让你的应用看起来响应更快。
// 如果你已经习惯了使用 class，那么你可能会想知道为什么 effect 在每次重渲染时都会执行，而不是只在卸载组件的时候执行一次。让我们看一个实际的例子，看看为什么这个设计可以帮助我们创建 bug 更少的组件。
// 忘记正确地处理 componentDidUpdate 是 React 应用中常见的 bug 来源。
// useEffect 默认就会处理。它会在调用一个新的 effect 之前对前一个 effect 进行清理。
import React, { useState, useEffect } from 'react';

function Example() {
  const [count, setCount] = useState(0);

  // Similar to componentDidMount and componentDidUpdate:
  useEffect(() => {
    // Update the document title using the browser API
    document.title = `You clicked ${count} times`;
  });

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}

// 通过跳过 Effect 进行性能优化
// 这是很常见的需求，所以它被内置到了 useEffect 的 Hook API 中。如果某些特定值在两次重渲染之间没有发生变化，你可以通知 React 跳过对 effect 的调用，只要传递数组作为 useEffect 的第二个可选参数即可：
// 如果想执行只运行一次的 effect（仅在组件挂载和卸载时执行），可以传递一个空数组（[]）作为第二个参数。这就告诉 React 你的 effect 不依赖于 props 或 state 中的任何值，所以它永远都不需要重复执行。这并不属于特殊情况 —— 它依然遵循依赖数组的工作方式。
useEffect(() => {
  document.title = `You clicked ${count} times`;
}, [count]); // 仅在 count 更改时更新

// useReducer
// useState 的替代方案。它接收一个形如 (state, action) => newState 的 reducer，并返回当前的 state 以及与其配套的 dispatch 方法。（如果你熟悉 Redux 的话，就已经知道它如何工作了。）
const [state, dispatch] = useReducer(reducer, initialArg, init);

// useCallback
// 把内联回调函数及依赖项数组作为参数传入 useCallback，它将返回该回调函数的 memoized 版本，该回调函数仅在某个依赖项改变时才会更新。当你把回调函数传递给经过优化的并使用引用相等性去避免非必要渲染（例如 shouldComponentUpdate）的子组件时，它将非常有用。
const memoizedCallback = useCallback(
  () => {
    doSomething(a, b);
  },
  [a, b],
);

// useMemo
// 把“创建”函数和依赖项数组作为参数传入 useMemo，它仅会在某个依赖项改变时才重新计算 memoized 值。这种优化有助于避免在每次渲染时都进行高开销的计算。
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);

// useRef
// useRef 返回一个可变的 ref 对象，其 .current 属性被初始化为传入的参数（initialValue）。返回的 ref 对象在组件的整个生命周期内保持不变。
const refContainer = useRef(initialValue);
function TextInputWithFocusButton() {
  const inputEl = useRef(null);
  const onButtonClick = () => {
    // `current` 指向已挂载到 DOM 上的文本输入元素
    inputEl.current.focus();
  };
  return (
    <>
      <input ref={inputEl} type="text" />
      <button onClick={onButtonClick}>Focus the input</button>
    </>
  );
}

// useImperativeHandle
// useImperativeHandle 可以让你在使用 ref 时自定义暴露给父组件的实例值。在大多数情况下，应当避免使用 ref 这样的命令式代码。
useImperativeHandle(ref, createHandle, [deps])

// useLayoutEffect
// 其函数签名与 useEffect 相同，但它会在所有的 DOM 变更之后同步调用 effect。可以使用它来读取 DOM 布局并同步触发重渲染。在浏览器执行绘制之前，useLayoutEffect 内部的更新计划将被同步刷新。
// 尽可能使用标准的 useEffect 以避免阻塞视觉更新。

// 我们已经发现大部分人并不喜欢在组件树的每一层手动传递回调。尽管这种写法更明确，但这给人感觉像错综复杂的管道工程一样麻烦。
// 在大型的组件树中，我们推荐的替代方案是通过 context 用 useReducer 往下传一个 dispatch 函数
const TodosDispatch = React.createContext(null);

function TodosApp() {
  // 提示：`dispatch` 不会在重新渲染之间变化
  const [todos, dispatch] = useReducer(todosReducer);

  return (
    <TodosDispatch.Provider value={dispatch}>
      <DeepTree todos={todos} />
    </TodosDispatch.Provider>
  );
}

// TodosApp 内部组件树里的任何子节点都可以使用 dispatch 函数来向上传递 actions 到 TodosApp：
function DeepChild(props) {
  // 如果我们想要执行一个 action，我们可以从 context 中获取 dispatch。
  const dispatch = useContext(TodosDispatch);

  function handleClick() {
    dispatch({ type: 'add', text: 'hello' });
  }

  return (
    <button onClick={handleClick}>Add todo</button>
  );
}

// 每个组件内部都有一个「记忆单元格」列表。它们只不过是我们用来存储一些数据的 JavaScript 对象。当你用 useState() 调用一个 Hook 的时候，它会读取当前的单元格（或在首次渲染时将其初始化），然后把指针移动到下一个。这就是多个 useState() 调用会得到各自独立的本地 state 的原因。


// ReactDOM.render
// 在提供的 container 里渲染一个 React 元素，并返回对该组件的引用。
// 如果 React 元素之前已经在 container 里渲染过，这将会对其执行更新操作，并仅会在必要时改变 DOM 以映射最新的 React 元素。
// ReactDOM.render() 会控制你传入容器节点里的内容。当首次调用时，容器节点里的所有 DOM 元素都会被替换，后续的调用则会使用 React 的 DOM diff 算法（DOM diffing algorithm）进行高效的更新。
ReactDOM.render(element, container[, callback]);

// ReactDOM.hydrate
// 与 render() 相同，但它用于在 ReactDOMServer 渲染的容器中对 HTML 的内容进行 hydrate 操作。React 会尝试在已有标记上绑定事件监听器。
ReactDOM.hydrate(element, container[, callback]);

// unmountComponentAtNode()
// 从 DOM 中卸载组件，会将其事件处理器（event handlers）和 state 一并清除。如果指定容器上没有对应已挂载的组件，这个函数什么也不会做。如果组件被移除将会返回 true，如果没有组件可被移除将会返回 false。
ReactDOM.unmountComponentAtNode(container);

// findDOMNode()
// 如果组件已经被挂载到 DOM 上，此方法会返回浏览器中相应的原生 DOM 元素。此方法对于从 DOM 中读取值很有用，例如获取表单字段的值或者执行 DOM 检测（performing DOM measurements）。大多数情况下，你可以绑定一个 ref 到 DOM 节点上，可以完全避免使用 findDOMNode。
ReactDOM.findDOMNode(component);

// createPortal()
// 创建 portal。Portal 将提供一种将子节点渲染到 DOM 节点中的方式，该节点存在于 DOM 组件的层次结构之外。
// 一个 portal 的典型用例是当父组件有 overflow: hidden 或 z-index 样式时，但你需要子组件能够在视觉上“跳出”其容器。例如，对话框、悬浮卡以及提示框
ReactDOM.createPortal(child, container);
```

## Webpack

```es6
// child_process.spawn() 方法异步地衍生子进程，且不阻塞 Node.js 事件循环
// child_process.spawn(command[, args][, options])
// command 要运行的命令 args 字符串参数的列表
// options.stdio 选项用于配置在父进程和子进程之间建立的管道。
// options.shell 如果为 true，则在 shell 中运行 command。 在 Unix 上使用 '/bin/sh'，在 Windows 上使用 process.env.ComSpec。 可以将不同的 shell 指定为字符串。 参阅 shell 的要求与 Windows 默认的 shell。 默认值: false（没有 shell）。
const executedCommand = cp.spawn(command, args, {
  stdio: "inherit",
  shell: true
});

// The entries() method returns a new Iterator object that contains the [key, value] pairs for each element in the Map object in insertion order.
// 使用了 entries 后，可以使用 for of 方法了
// for (const [key, value] of this.groupMap.entries()) {
Map.prototype.entries()

// 使用 webpackOptionsSchema 配合 ajv 校验配置项格式是否正确
const webpackOptionsValidationErrors = validateSchema(
  webpackOptionsSchema,
  options
);

// require.resolve 增强版，NodeEnvironmentPlugin 给 compiler 提供了更强大的解析路径能力
// alias 功能就是用这个工具实现的
new NodeEnvironmentPlugin({
  infrastructureLogging: options.infrastructureLogging
}).apply(compiler);

// webpack 核心使用 Tapable 来实现插件(plugins)的 binding 和 applying.

```