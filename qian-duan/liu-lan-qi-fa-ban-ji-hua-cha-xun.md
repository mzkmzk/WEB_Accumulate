# 浏览器发版计划查询

# 前言

作为一名前端开发者, 需要了解浏览器的发版动向

# chromium核

- 未来几个版本的发版时间规划: https://chromiumdash.appspot.com/schedule
- 已经发版的时间说明: https://chromiumdash.appspot.com/releases?platform=Mac, 这里可以选择不同的平台, 但是发版时间基本是一致的
- 每个版本的Features内容查询地址: https://www.chromestatus.com/features

### 灰度规则

假设现在81的版本规划在2020年1月17号完成了代码编写

- Feature Freeze: 2020年1月17日 代码基本完成
- Branch Point: 2020年1月30日 基本测试完成, 拥有了稳定的分支 (基本每6周会有一个这样的版本)
- First Beta: 2020年2月13日 在稳定分支后两周, 会推送第一个Beta版本, 之后每周都会推送Beta版本, 直到发布到稳定版
- Stable Cut: 2020年3月31日 First Beta 之后的4周, 会发一个几乎稳定的版本
- Stable Release: 2020年4月7日 在Stable Cut之后的一周发布正式版本
- Stable Refresh: Stable Release之后两周内 给用户全量发送新版本更新

具体参考 https://chromium.googlesource.com/chromium/src.git/+/master/docs/process/release_cycle.md#Stable-Release

# 尝试浏览器的测试属性

浏览器在做API改动的时, 一般会慢慢灰度, 然后再特定的页面 留一个开关

- chrome: chrome://flags/#cookies-without-same-site-must-be-secure
- firefox: about:config

# safari

- 发版本记录: https://developer.apple.com/documentation/safari_release_notes
- 下载最新的safari: https://developer.apple.com/download/more/?=Safari

