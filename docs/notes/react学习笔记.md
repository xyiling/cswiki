## 基础语法
```tsx
// 定义组件，并支持定义props类型
function Component(props: { prop1: string; prop2: string }) {
    return (
        <div>
            <h1>{props.prop1}</h1>
            <p>{props.prop2}</p>
        </div>
    );
}

// 或者也支持使用const定义组件
const component: React.FC<{ prop1: string; prop2: string }> = (props) => {
    return (
        <div>
            <h1>{props.prop1}</h1>
            <p>{props.prop2}</p>
        </div>
    );
};
// 渲染组件
<Component
    prop1="value1"
    prop2="value2"
/>
// 条件渲染, 当cond为true时渲染Component
{cond && <Component />}
// 三元运算符, 当cond为true时渲染Component，否则渲染null
{cond ? <Component /> : null}
// 列表渲染
{
    list.map((item, index) => (
        <Component key={index} item={item} />
    ))
}
```

