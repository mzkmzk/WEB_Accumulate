# no script URL provided react native

# 错误信息

No script URL provided. Make sure the packager is running or you have embedded a JS bundle in your application

# 解决方案

1. 重新生成 main.jsbundle
2. hosts没配置好 看能否curl到你的js 例如

    ```shell
     curl 'http://localhost:8081/Examples/UIExplorer/UIExplorerApp.ios.bundle?platform=ios' -o main.jsbundle
    ```
3. 将product->scheme->edit scheme->replece   
# 参考链接

https://github.com/facebook/react-native/issues/6458
