# FFuzzApi
协助安全研究人员发现应用程序中可能存在的风险<br>
[![Author](https://img.shields.io/badge/author-DarkFi5-blue.svg)](https://github.com/DarkFi5)
[![GitHub release](https://img.shields.io/github/release/DarkFi5/FFuzzApi.svg)](https://github.com/DarkFi5/FFuzzApi/releases)
[![GitHub stars](https://img.shields.io/github/stars/DarkFi5/FFuzzApi.svg)](https://github.com/DarkFi5/FFuzzApi/stargazers)

## 简介

FFuzzApi 是一个强大的命令行工具，旨在帮助安全研究人员和渗透测试人员发现 Web 应用程序中的敏感信息泄漏和未授权访问点。它可以自动从 HTML 和 JavaScript 文件中提取 URL，识别敏感关键词和 URL，并进行主动探测以发现隐藏漏洞，用户可以指定敏感信息指纹规则文件，也可以使用内置指纹规则文件。

## 功能

- **URL 提取：** 自动从 HTML 和 JavaScript 文件中提取 URL。
- **敏感关键词检测：** 用户可配置的关键词检测，识别诸如身份证、手机号、邮箱等敏感信息。
- **主动探测：** 可配置的 URL 探测以识别敏感端点。
- **可定制路径扫描：** 支持用户定义的父路径以进行定向扫描。
- **指纹集成：** 集成流行的指纹库用于敏感信息检测。
- **未授权扫描更精准: ** 指定获取敏感API一级目录，排除无用API，提高扫描效率。

## 使用示例

```bash
# 示例用法
FFuzzApi -j http://example.com/script.js -w whitelist.txt -i api,web -u www.baidu.com,www,ichuqiu.com
FFuzzApi -J scripts.txt -t 10 --timeout 5
FFuzzApi -J jsUrl.txt -w whitelist.txt -i api,web -u www.baidu.com,www,ichuqiu.com
FFuzzApi --header 'Authorization: Bearer token'
FFuzzApi --data '{\"key\":\"value\"}' --method POST
FFuzzApi -J jsUrl.txt -i minispread -w whitelist.txt -u https://etdigital.qa.17u.cn/  -i ai,web -o result.csv --status 200,404
FFuzzApi -J jsUrl.txt -i minispread -w whitelist.txt -u https://etdigital.qa.17u.cn/ --patterns regex_patterns.json -i ai -p http://127.0.0.1:8080 --status 200,404 --data {\"id\":\"1\"} --header \"Cookie: xxx\" 
FFuzzApi -J jsUrl.txt -i minispread -w whitelist.txt -u https://etdigital.qa.17u.cn/ --patterns regex_patterns.json -i ai,web -o result.csv
FFuzzApi --help
```

# 未指定敏感API一级目录效果如下
![image](https://github.com/user-attachments/assets/41e82154-14d0-4b6f-b461-cb53378fcf59)

![image](https://github.com/user-attachments/assets/cf2df8ce-042e-432d-829f-7101f3570c81)

# 指定敏感API一级目录效果如下
![image](https://github.com/user-attachments/assets/624f17bc-9a6e-4496-bf1e-00de43006913)

![image](https://github.com/user-attachments/assets/e68b788f-1adc-4b2f-8bca-2529c1289d86)


## 安装

1. 从[发布页面](https://github.com/DarkFi5/FFuzzApi/releases)下载最新版本。
2. 确保敏感规则匹配文件和白名单主域名文件与 `FFuzzApi.exe` 位于同一目录下。
3. 使用命令行运行工具，并选择所需选项。

## 检测思路
在日常漏洞挖掘过程中，相信有很多小伙伴发现过未授权漏洞，一个未授权少则百元，多则上千上万，基于想自动化挖掘并发现这些未授权的思路下，编写了这款工具。例如本次漏洞挖掘、渗透测试的某个应用地址为：http://www.baidu.com/web/info/list</br>
1、基于日常漏洞挖掘经验，可以确定该应用后续访问的地址一级目录可能都为web，也会出现一个站点挂了多个一级目录，通过前期收集把这些一级目录存下来。接下来就需要把我们访问该应用收集到的js文件，这里可以通过jsfinder等等工具，看大家习惯，把收集到的js文件全部放到jsUrl.txt文件中，紧接着，就会启用api发现模块，去对包含了全部js地址的文件，一个一个的请求，去发现命中了与我们前期收集到的一级目录匹配的路由/接口，同时请求的js地址也会基于白名单主域名列表，例如：</br>
http://www.baidu.com/webUi/info/adasda.js</br>而我们js白名单主域名列表whitelist.txt中存在baidu.com这个主域名，后面才会去请求这个js，寻找敏感api。
这一步是为了排除掉与本次漏洞挖掘无关的js文件，降低误报，</br>
2、使用指定域名+命中规则Api进行fuzz，如果存在后台ApiFuzz，建议添加header头认证信息，默认使用POST请求，请求体默认为json格式的空字符，用户可以自定义，注意请求体参数若需要双引号，需要添加\\进行转移。如 --data {\\\"id\\\":\\\"1\\\"}</br>
3、前期有一些步骤还可以集成并自动化，同时有一些特殊挖掘场景也会在使用中不断完善、更新。</br>

<b>推荐使用FFuzzApi+Enscan+JsFinder效果更佳</b><br>
<b>同时作者也在二开Urlfinder，旨在于将前期发现js->获取敏感api->定制化扫描api，让挖掘漏洞更简单、更方便！</b>


## 配置

### 标志

- `-j, --javascript`: 输入单个 JavaScript URL。
- `-J, --javascript-file`: 输入包含 JavaScript URL 的文件。
- `-i, --include`: 逗号分隔的一级目录列表。
- `-t, --threads`: 并发线程数（默认：1）。
- `-w, --white-list`: 包含允许的主域名的文件。
- `-u, --urls`: 逗号分隔的域名列表。
- `--header`: 请求头信息，格式为 key:value，用逗号分隔。
- `--data`: POST 请求的请求体数据。
- `-m, --method`: HTTP 方法（GET 或 POST）。
- `--patterns`: 输入匹配敏感信息指纹规则文件。
- `--status`: 需要展示的状态码列表，用,分割，如200，302，404。
- `--timeout`: API 请求超时时间（默认：3 秒）。
- `-p, --proxy`: HTTP 代理（例如，`http://127.0.0.1:8080`）。
- `-o, --output`: 输出格式：`json`、`txt`、`csv`、`xls`。
- `-h, --help`: 显示帮助。

### 工具目录列表
![image](https://github.com/user-attachments/assets/e555ebd2-0a0b-42c6-b7ef-9315be76160d)
- jsUrl.txt [包含了前期收集到的所有js文件]
- whitelist [包含了目标主体的所有主域名]
- regex_patterns.json [包含了敏感信息匹配的所有正则规则，可自定义添加]
- result/api [存储了所有命中规则的api结果文件]
- result/FuzzApi [存储了命中敏感信息规则的结果文件]

### 运行

- ![image](https://github.com/user-attachments/assets/008d9ead-6152-4688-82c1-8d5e60a50c0b)

- ![image](https://github.com/user-attachments/assets/05a16b8a-1c40-45c7-978a-2f215cfda691)

## 贡献

欢迎贡献！如果你发现任何漏洞或者有功能需求，请提交一个拉取请求（pull request）或者创建一个问题（issue）。

## 免责声明
本工具仅作为安全研究交流，请勿用于非法用途。如您在使用本工具的过程中存在任何非法行为，您需自行承担相应后果，本人将不承担任何法律及连带责任。
