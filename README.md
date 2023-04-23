# WPeChatGPT
- 基于与 ChatGPT 相同模型的**IDA 插件**，使用 OpenAI 发布的 gpt-3.5-turbo 模型，可以帮助分析师们分析二进制文件.

- 当前 *WPeChatGPT* 支持的**功能**包括：
   - 分析函数的使用环境、预期目的、函数功能。
   - 重命名函数的变量。
   - 尝试用 python3 对函数进行还原，此功能主要是针对较小块的函数（如一个异或解密函数）。
   - 在当前函数中查找是否存在漏洞。
   - 尝试用 python 对漏洞函数生成对应的 EXP。
   - 利用 GPT **全自动分析二进制文件**，具体参考节 ***Auto-WPeGPT***。
- *WPeChatGPT* 插件使用的是 OpenAI 基于GPT训练的 **text-davinci-003** 模型。  
  *v2.0* 版本后使用 OpenAI 最新的 **gpt-3.5-turbo** 模型（The same as **ChatGPT**）。  

ChatGPT 的分析结果**仅供参考**，不然我们这些分析师就当场失业了。XD  
## 更新历史
|Version|Date|Comment|
|----|----|----|
|1.0|2023-02-28|Based on Gepetto.|
|1.1|2023-03-02|1. 删除分析加解密的功能。<br>2. 增加 python 还原函数的功能。<br>3. 修改了一些细节。|
|1.2|2023-03-03|1. 增加查找函数中二进制漏洞的功能。<br>2. 增加尝试自动生成对应 EXP 的功能。<br>3. 修改了一些细节。<br>（由于OpenAI服务器卡顿原因未测试上传）|
|2.0|2023-03-06|1. 完成测试 *v1.2* 版本漏洞相关功能。<br>2. 改用 OpenAI 最新发布的 **gpt-3.5-turbo** 模型。|
|2.1|2023-03-07|修复 OpenAI-API 的 timed out 问题。（详见节***关于 OpenAI-API 报错***）|
|2.3|2023-04-23|添加 Auto-WPeGPT 自动分析功能。（详见节***Auto-WPeGPT***）|
## 安装
1. 运行如下命令安装所需包。
```
pip install -r ./requirements.txt
```
2. 修改脚本 `WPeChatGPT.py` 添加 API key 到变量 ***openai.api_key***。
3. 复制脚本文件 `WPeChatGPT.py` 到 IDA 的 plugins 文件夹, 最后重启 IDA 后即可使用。  

**`! NOTE`**：需要把 **IDA 的环境**设置为 **python3**，WPeChatGPT *2.0* 版本后需要使用**最新的 OpenAI Python 包**。
## 使用方法
支持在 IDA 中使用**右键、菜单栏或快捷键**任一。
- 快捷键：  
  `函数分析 = "Ctrl-Alt-G"`  
  `重命名函数变量 = "Ctrl-Alt-R"`  
  `二进制漏洞查找 = "Ctrl-Alt-E"`  

- 伪代码窗口右键：

&emsp;&emsp;<img src="https://github.com/WPeace-HcH/WPeChatGPT/blob/main/IMG/menuInPseudocode.png" width="788"/>

- 菜单栏：Edit $\Rightarrow$ WPeChatGPT

&emsp;&emsp;<img src="https://github.com/WPeace-HcH/WPeChatGPT/blob/main/IMG/menuInEdit.png" width="360"/>
## Auto-WPeGPT
**更新历史：**
|Version|Date|Comment|
|----|----|----|
|0.1|2023-04-23|初始版本。|

**使用方法：** 在菜单栏找到 Auto-WPeGPT 后点击即可，输出完成提示后可在对应文件夹（*"WPe_+IDB名称"*）中找到分析结果。  
- 菜单栏：Edit $\Rightarrow$ WPeChatGPT $\Rightarrow$ Auto-WPeGPT

&emsp;&emsp;<img src="https://github.com/WPeace-HcH/WPeChatGPT/blob/main/IMG/auto-wpegpt_menu.png" width="788"/>

输出文件夹中的每个文件含义：
```
GPT-Result.txt -> Auto-WPeGPT 分析结果
funcTree.txt -> 函数调用树形结构
mainFuncTree.txt -> 主函数树结构
effectiveStrings.txt -> 二进制文件中的可疑字符串
```

**效果展示：** 

&emsp;&emsp;<img src="https://github.com/WPeace-HcH/WPeChatGPT/blob/main/IMG/autogptExample.gif" width="788"/>

## 示例
使用方式：

&emsp;&emsp;<img src="https://github.com/WPeace-HcH/WPeChatGPT/blob/main/IMG/useExample.gif" width="790"/>

函数分析效果展示：

&emsp;&emsp;<img src="https://github.com/WPeace-HcH/WPeChatGPT/blob/main/IMG/resultExample.gif" width="790"/>

二进制漏洞查找效果展示：

&emsp;&emsp;<img src="https://github.com/WPeace-HcH/WPeChatGPT/blob/main/IMG/vulnExample.gif" width="790"/>
## 关于 OpenAI-API 报错
&emsp;&emsp;从 2023.3.2 开始我经常遇到 API 报错，开始以为是服务器不稳定的问题（因为在我这里时好时坏），但是由于有太多反馈说都遇到了相关错误，所以我先去了 OpenAI 查看 API Status 之后发现其运行情况良好，因此发现可能并不是我所想的服务器问题，于是进行了相关问题的搜索及调试，以下是我对 OpenAI API 连接问题的处理方法：  

&emsp;&emsp;首先前提，插件已经在**科学上网**的条件下运行。
- 在科学上网的条件下，如果发现插件多次尝试都无法正常连接 API，那么需要查询一下 python 的 urllib3 版本（1.26 版本存在代理问题）。
   - 可以使用如下命令对 urllib3 进行回退修复：
   ```
   pip uninstall urllib3
   pip install urllib3==1.25.11
   ```
- 如果 urllib3 版本没错或重装 1.25 版本还是存在 API 访问问题的话，那么请下载最新版本，对插件指定代理：
   - 将下面三行代码取消注释，然后把代理地址及端口信息填入 ***proxies*** 变量即可：  
   ```
   #print("WPeChatGPT has appointed the proxy.")
   #proxies = {'http': "http://127.0.0.1:7890", 'https': "http://127.0.0.1:7890"}
   #openai.proxy = proxies
   ```
## 联系我
如果使用插件时遇到问题或有任何疑问，欢迎留言或发送邮件联系我。
## 致谢
受到 *Gepetto* 的启发，该项目地址为：https://github.com/JusticeRage/Gepetto 。
