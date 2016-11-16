# prop的关键字问题

# 问题

在prop向子组件传递数据时

脑残的这样传递


```javascript
<Tasks data={tasks.data} />
```

然后子组件里this.props.data是结果是undefined

我知道标签是有个类似data-*属性...但是data怎么也莫名其妙了


# 结果

props传递时候不要用关键字就可以了

现在踩坑的有data