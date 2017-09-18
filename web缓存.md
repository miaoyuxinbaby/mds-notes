# Web缓存 
- study from by  www.alloyteam.com/2016/03/discussion-on-web-caching/
## 缓存分类
+ web缓存分为很多种，比如数据库缓存，代理服务器缓存，还有我们熟悉的CDN缓存，以及浏览器缓存

#### 通过代理服务器向源服务器发起请求
- 浏览器先向代理服务器发起Web请求，再将请求转发到源服务器。它属于共享缓存。

#### 浏览器缓存
- 页面的缓存状态是由header决定的，header的参数有四种:
```
 1. Cache-Control
 2. Expires
 3. Last-modified
 4. ETag
 5. LocalStorage和sessionStorage
```

###### Cache-Control
- max-age(单位为s)指定设置缓存最大的有效时间，定义的是时间长短。当浏览器向服务器发送请求后，在max-age这段时间里浏览器就不会再向服务器发送请求了
- s-maxage(单位为s)同max-age，只用于共享缓存（比如CDN缓存）。 
- max-age用于普通缓存，而s-maxage用于代理缓存。如果存在s-maxage，则会覆盖掉max-age和Expires header。
- public指定响应会被缓存，并且在多用户间共享，如果没有指定public还是private，则默认为public。
- private响应只作为私有的缓存，不能在用户间共享。如果要求HTTP认证，响应会自动设置为private。
- no-cache指定不缓存响应，表明资源不进行缓存。 <br> 但是设置了no-cache之后并不代表浏览器不缓存，而是在缓存前要向服务器确认资源是否被更改。因此有的时候只设置no-cache防止缓存还不够保险，还可以加上private指令，将过期时间设为过去的时间。
- no-store绝对禁止缓存，每次请求资源都要从服务器重新获取。
- must-revalidate指定如果页面是过期的，则去服务器进行获取（不常用）


###### Expires
- 缓存过期时间，用来指定资源到期的时间，是服务器端的具体的时间点。<br>也就是说，Expires = max-age + 请求时间，需要和Last-modified结合使用。（cache-control的优先级更高）
- Expires是Web服务器响应消息头字段，在响应http请求时告诉浏览器在过期时间前浏览器可以直接从浏览器存取数据，而无需再次请求

###### Last-modified
- 服务器端文件的最后修改时间,需要和cache-control共同使用，是检查服务器端资源是否更新的一种方式。当浏览器再次请求时，会向服务器传送if-Modified-Since报头，询问Last-modified时间点之后资源是否被修改过。如果没有修改，则返回码为304，使用缓存；如果修改过，则再次去服务器请求资源，返回码和首次请求相同为200。

###### ETag
- 根据实体内容生成一段hash字符串，标识资源的状态，由服务器端产生。浏览器会将这串字符串传回服务器，验证资源是否已经修改。
- 使用ETag可以解决Last-modified存在的一些问题<br> a、某些服务器不能精确的得到资源的最后修改时间，这样就无法通过最后修改时间判断资源是否更新。<br>b、如果资源修改非常频繁，在秒以下的时间内进行修改，而Last-modified只能精确到秒。<br>c、一些资源的最后修改时间改变了，但是内容没改变，使用ETag就认为资源还是没有修改的。

###### LocalStorage和SessionStorage
- LocalStorage是一种本地存储的公共资源，域名下很多应用共享这份资源会有风险；LocalStorage是以页面域名划分的，如果有多个等价域名之间的LocalStorage不互通，则会造成缓存多份浪费。
- LocalStorage在PC上的兼容性不太好，而且当网络速度快、协商缓存响应快时使用LocalStorage的速度比不上304。并且不能缓存css文件。而移动端由于网速慢，使用LocalStorage要快于304
- SessionStorage的数据只存储到特定的会话中，不属于持久化的存储，所以关闭浏览器会清楚数据。和LocalStorage具有相同的方法。


