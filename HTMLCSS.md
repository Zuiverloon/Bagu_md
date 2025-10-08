# Links

codepen.io
devdocs.io

# HTML

h1 -> h6

# CSS

inline > internal > external  
dont use id selector
pesudo class(:hover :active ...)

## display

**common block elements**: p h1-h6 div ol/ul/li form  
**common inline elements**: span,img,a  
cannot change width of inline element, but block element can, inline-block can support both feature.  
**display: none 和 visibility hidden 的区别？**  
前者 remove element，后者保留位置但是隐藏

## positioning

children sit on parents
**position**:static, relative, absolute, fixed  
static: by default  
relative: can move element top/bottom/left/right from where it used to be, and it does not affect other elements  
absolute: it affects the layout of other elements. right:20px means 20 px away from the right border of the parent element  
fixed: pinned on the screen even when we scroll down

## text-align

textalign needs to be set in parent element, only affect element without a width attr  
如果指定了宽度导致 center 失效怎么办？margin:0 auto 0 auto

# 面试题

## 浏览器内核

渲染引擎：获取网页内容，计算页面显示方式，渲染到屏幕
js 引擎：解析执行 js 代码，实现逻辑控制与 DOM 交互

## DOM（document object model） / 虚拟 DOM

**DOM**
html 树状结构，表示页面内容和结构，每个元素都是一个节点，js 可以通过 dom api 去操作，但是操作 dom 是昂贵的，频繁更新会导致性能下降，每次修改会触发浏览器重排重绘

**虚拟 dom**
一个 js 对象，模拟页面结构，状态更新/修改时先和虚拟做 diff 再更新

**react diff 算法**
不同类型的节点，销毁旧的创建新的；同一层的节点按顺序比较；通过 key 精准找到子节点

## cookie / session storage / local storage

**cookie**
存键值对，约 4KB，每次请求会自动携带到服务器

**session storage**
窗口内存，当前窗口有效，关闭就没了，刷新了还在

**local storage**
浏览器本地存储，持久化存储，永久保存除非手动清除

## JWT（json web token）

由 header payload signature 组成，header 和 payload 都是 base64 编码的，可解码，最好不要放敏感信息

## DOCTYPE

文档类型声明，告诉浏览器当前使用的是哪种 HTML 标准

## src 和 href 的区别

src：定义资源的来源，浏览器会立即加载并渲染
href：定义链接，用于跳转

## script 标签中 defer 和 async 的区别

defer 会等 dom 解析完毕后执行，按照顺序执行，适合依赖 DOM 的脚本
async 是加载完立刻执行，可能比 DOM 更早完成，适合不依赖 DOM 的脚本

## iframe

用来嵌入另一个网页，如果和当前网页不是同一个域名，js 不能访问其内容

# React 面试题

## React.js 是什么

开源 js 库，维护视图层，组件化开发+虚拟 DOM+响应式渲染

## jsx

语法糖，底层是 Babel 编译后，调用 createElement

## react hook

钩子函数，允许在函数组件中使用状态和生命周期

## 如何做 SSO

SAML
SAML 登录成功 → SP 接收 Assertion → Passport 提取用户信息
→ 存入 Session（服务器端） → 浏览器保存 Session ID（Cookie）
→ 后续请求带上 Cookie → Passport 注入 req.user

## JWT

json 格式的身份认证令牌，包含 header，payload 和签名
用户登录成功后，服务器生成 JWT 并返回给客户端

客户端将 JWT 存储在本地（如 localStorage 或 cookie）

后续请求中，客户端将 JWT 放入请求头（如 Authorization: Bearer <token>）

服务器验证签名和有效期，确认用户身份

```json
Header:
{
  "alg": "HS256",
  "typ": "JWT"
}

Payload:
{
  "sub": "1234567890",
  "name": "Alice",
  "iat": 1695800000,
  "exp": 1695803600
}

```
