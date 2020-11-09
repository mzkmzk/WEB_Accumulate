# Graphql入门

# Schema书写顺序

这里以`github-schema.graphql`为标准样本进行编写

- interface
- union
- type
- enum
- input 
- scalar

# 学习入门网站

- https://www.howtographql.com/
- 分页最佳实践说明: https://relay.dev/graphql/connections.htm

# 查找属性值顺序

`schema`

```
type PageTagRecordGroup {
    text: String!
}

type Query {
    pageTagRecordGroup(skip: Int): PageTagRecordGroup
}
```


`resolvers`

```javascript
module.exports = {
    Query: {
        pageTagRecordGroup: async function () {
            return {
                text: 222
            }
        }
    },
    PageTagRecordGroup: {
        text: async function () {
            return 333
        }
    }
}
```

`query`

```
{
    pageTagRecordGroup(skip: 10) {
        text
    }
}
```

返回结果为

```
{
    "data": {
        "pageTagRecordGroup" {
            text: "333"
        }
    }
}
```

