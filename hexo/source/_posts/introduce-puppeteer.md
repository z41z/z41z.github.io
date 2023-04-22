---
title: Puppeteer应用场景总结
tags: [Puppeteer,Google,Chrome,pptr]
---

#### 0x00: 介绍
Puppeteer是一个通过DevTools协议控制Chromium或Chrome的Node库。

#### 0x01：技术基础
  - [DOM](https://developer.mozilla.org/zh-CN/docs/Web/API/Document_Object_Model/Introduction)
  - [JavaScript(ECMAScript 2017/ES8)](https://262.ecma-international.org/8.0/)

#### 0x02: 下载及安装
  ``` node
  // 默认下载浏览器,如果使用本地浏览器则安装puppeteer-core包
  npm i puppeteer -S
  ```

#### 0x03: 初始化
``` js
  const pptr = require("puppeteer-core")
  const PUPPETEER_CONFIG = {
    BROWSER_PATH: "C:\\Program Files\\Google\\Chrome\\Application\\chrome.exe",
    WIDTH: 1920,
    HEIGHT: 1080
  }
  /**
  * @description 以浏览器模式打开特定网址
  * @param {String} url 网址
  */
  const run = async (url = "https://pptr.dev") => {
    let { BROWSER_PATH, WIDTH, HEIGHT } = PUPPETEER_CONFIG
    const browser = await pptr.launch({
      headless: false,
      args: [
        // 浏览器窗口大小
        `--window-size=${WIDTH},${HEIGHT}`
      ],
      executablePath: BROWSER_PATH,
      // 视图大小
      defaultViewport: {
        width: WIDTH,
        height: HEIGHT
      }
    })
    const page = await browser.newPage()
    await page.goto(url)
  }

  run("https://www.baidu.com")
```

#### 0x04: 截图
``` js
  await page.screenshot({ path: 'screenshot.png' })
```

#### 0x05: 导出PDF
需将`headless`设置为`true`
``` js
  await page.pdf({ path: 'page.pdf' })
```

#### 0x06: 模拟操作网页
打开百度搜索`Puppeteer`后，点击第一个搜索结果
``` js
  // 输入关键字并回车
  const inputEl = await page.$("#kw")
  await inputEl.type('puppeteer')
  await page.keyboard.press('Enter')
  // 等待结果
  await page.waitForSelector(".result.new-pmd a")
  // 点击第一个搜索结果
  await (await page.$(".result.new-pmd a")).click()
```

#### 0x07: 注入JavaScript/CSS
``` js
  await page.addStyleTag({
    content: `.c-abstract{color:#666!important;}a{color:#00aeff!important;}#content_right{display:none;}`
  })
  await page.addScriptTag({
    content: `alert("已注入CSS样式，此弹窗通过注入JavaScript弹出");`
  })
```

#### 0x08: 请求拦截
``` js
  await page.setRequestInterception(true);
  page.on('request', (request) => {
    let url = request.url()
    if (/sugrec/.test(url)) {
      request.continue({
        url: url.replace(/(puppeteer)/ig, 'java')
      })
    } else {
      request.continue()
    }
  });
  await inputEl.type("请求拦截")
  console.log(chalk.green(`请求拦截${chalk.yellow(`已将搜索关键字puppeteer替换成java`)}`))
```

#### 0x09: 响应捕获
``` js
  page.on('response', async (response) => {
    let url = response.url()
    let text = await response.text()
    if (/sugrec/.test(url)) {
      console.log(chalk.green(`响应捕获到数据：${chalk.yellow(`${text}`)}`))
    }
  });
```

#### 0x10: 响应拦截
``` js
  await page.setRequestInterception(true);
  page.on('request', (request) => {
    let url = request.url()
    if (/sugrec/.test(url)) {
      // 响应拦截
      request.respond({
        status: 404,
        body: '拦截404'
      })
    } else {
      request.continue()
    }
  });
```

#### 0x11: 控制台代码执行
``` js
  await page.evaluate(() => {
    document.querySelectorAll("div").forEach(item => {
      item.style.fontSize = '16px'
    })
  })
```

#### 0x12: 案例
  - ##### 自动化测试
  - ##### 富文本导出
  - ##### 爬取土地拍卖数据
  - ##### 爬取股票数据

#### 0x13：参考
  - [Puppeteer文档](https://pptr.dev/)
  - [async/await说明](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/async_function)
  - [DOM文档](https://developer.mozilla.org/zh-CN/docs/Web/API/Document_Object_Model/Introduction)
