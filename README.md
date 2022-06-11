![m](https://user-images.githubusercontent.com/29711228/173181783-c841f2e4-4a6d-44e6-8aba-c70ed7c13d41.png)
2022.5.19 左右，本项目已无意义。archived.

# Cloud

这是一个**检查更新**的服务器解决方案，采用中继加速的思想，引导更新程序访问最新推送版本的文件，同时又可以使用 `jsdelivr` 全球加速，避免了需要频繁刷新缓存造成的问题。

### 优势

相比自建更新检查，Cloud方案:

 - 不需要经济支持（有个域名就行），且一劳永逸
 - 稳定，`jsdelivr` 全球高速连接

另外，相对于单纯使用 `jsdelivr` 的更新检查，本项目：
 - 使用随机目录名绕过缓存。众所周知，JSD 在大陆的缓存很难使用 purge 刷新。
 - 可以自动对文件生成`.md5.verify`校验文件
 - 可以自定义自动运行外部脚本

------

### 部署本方案
#### 准备
 - 一个域名（部分后缀非常廉价，甚至可以白嫖，具体自行baidu/google）
 - 一个Cloudflare账号 [注册](https://dash.cloudflare.com/sign-up)
 - 对于域名解析、CDN、GithubPages一点点了解
#### 将域名添加到Cloudflare账号
![a](https://user-images.githubusercontent.com/29711228/144693542-9f7f37d9-db6b-43ce-8ed8-dd89a296ece4.png)
#### Fork本仓库
#### 开启 Github Page
![b](https://user-images.githubusercontent.com/29711228/144693897-2d708842-a3d3-4e84-bbff-ff16779d089d.png)
#### 完成
如果一切无误，就已经完成了！现在可以在仓库内启动`Actions`。

### 如何使用
#### 上传/下载文件
 - 修改**根目录**对应文件夹中的文件，推送到仓库即可。不要修改随机文件夹中的内容

> 例如您在 **Test** 文件夹新建了 **version.json** 文件。接下来，您可以使用以下方法访问到您的文件

```javascript
// 访问中继索引文件
let remote = HttpGet("https://lxl-cloud.amd.rocks/id.json")
// 单纯使用jsdelivr并不能绕过id.json的缓存，因此使用Cloudflare CDN下载中继文件，获取中继目录名后再使用速度较快的jsdelivr进行下载
let json = JSON.parse(remote.data)	//Json格式：{ "token": "中继目录名" }
let dir = json.token;	 //获取中继目录名

// *目标文件的下载地址（中间加上了"token"目录）
let downloadUrl = "https://cdn.jsdelivr.net/gh/LiteLDev-LXL/Cloud/" + dir + "/Test/version.json"

// *目标文件的MD5校验数据地址
let md5Url = "https://cdn.jsdelivr.net/gh/LiteLDev-LXL/Cloud/" + dir + "/Test/version.json.md5.verify"
```
 - 使用上面给出的两个拼接的下载地址，下载你需要的目标文件和MD5校验数据即可。

> **切记** 使用上述方法、使用随机文件夹名中继访问你的文件。否则，对jsdelivr文件的直接访问极有可能存在缓存，并且难以刷新。

#### 推送时运行自定义脚本
 - 此功能为方便开发者而提供。在每次Push时，Github Actions将运行目录下所有的`__AUTORUN__.sh`文件。

 默认的Actions使用Windows系统，但你需要用`bash`语法来编写脚本。<br>
 一个应用示例：

 ```bash
 # Path: ./iLand/__AUTORUN__.sh
 # iLand 自动从上游仓库同步语言文件到此仓库
 git clone https://github.com/LiteLDev-LXL/iLand-Core.git
 cp -R iLand-Core/3rd-languages/* i18n-data
 cp -R iLand-Core/iland/lang/* i18n-data
 rm -rf iLand-Core
 echo "iland's corn is completed."
 ```
  - 编写完成后，请务必关注脚本在Actions中的运行情况，错误的脚本可能导致Actions中断。

## Credits
 - JSD 加速仓库的想法和实现 [@RedbeanW](https://github.com/Redbeanw44602)
 - MD5 自动计算程序 [@yqs112358](https://github.com/yqs112358)
 - 一切的基础 [@jsdelivr](https://github.com/jsdelivr)
