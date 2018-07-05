# 缓存
缓存是一种保存资源副本并在下次请求时直接使用该副本的技术。


# 缓存的种类
很多开发者习惯把cookie、webStorage以及IndexedDB存储的数据也称之为缓存，理由是都是保存在客户端的数据，没有什么区别。其实这是不严谨的，cookie的存在更多的是为了让服务端区别用户，webStorage和IndexedDB则更多用在保存具体的数据和在客户端存储大量结构化数据(文件/blobs)上面。

实际上所谓的缓存只有一种——它是请求资源的副本。试想一下，如果每一个资源我们客户端都会保存一份副本，这会怎么样？客户端会炸掉，开发者会疯掉！所以我们需要一份协议来处理缓存，可以让开发者控制缓存的建立和删除。谁呢？还能有谁，HTTP呗。HTTP协议里定义了很多关于缓存的请求和响应字段，这也是接下来我们重点要逼逼叨的对象，研究下究竟是哪些字段怎么影响缓存的。

# 缓存的好处
缓解服务器压力(不用每次去请求资源)；
提升性能(打开本地资源速度当然比请求回来再打开要快得多)；
减少带宽消耗(我相信你可以理解)


# 问题
那么问题又来了，既然缓存这么好，如果我请求的服务器中间有代理也缓存了怎么办？代理服务器缓存了我的资源导致我没法从源服务器拿到最新的资源怎么办？HTTP当然也想到了这块的诉求。接下来我们也会逐层剖析。

# 缓存分类
缓存在宏观上可以分成两类：
私有缓存和共享缓存。共享缓存就是那些能被各级代理缓存的缓存(咋觉得有点绕)。私有缓存就是用户专享的，各级代理不能缓存的缓存。
微观上可以分下面三类：
1. 浏览器缓存
我相信只要你经常使用某个浏览器🌎(Chrome,Firefox,IE等)，肯定知道这些浏览器在设置里面都是有个清除缓存功能，这个功能存在的作用就是删除存储在你本地磁盘上资源副本，也就是清除缓存。
缓存存在的意义就是当用户点击back按钮或是再次去访问某个页面的时候能够更快的响应。尤其是在多页应用的网站中，如果你在多个页面使用了一张相同的图片，那么缓存这张图片就变得特别的有用。😏
2. 代理服务器缓存
代理服务器缓存原理和浏览器端类似，但规模要大得多，因为是为成千上万的用户提供缓存机制，大公司和大型的ISP提供商通常会将它们设立在防火墙上或是作为一个独立的设备来运营。(下文如果没有特殊说明,所有提到的缓存服务器都是指代理服务器。)
由于缓存服务器不是客户端或是源服务器的一部分，它们存在于网络中，请求路由必须经过它们才会生效，所以实际上你可以去手动设置浏览器的代理，或是通过一个中间服务器来进行转发，这样用户自然就察觉不到代理服务器的存在了。🤥
代理服务器缓存就是一个共享缓存，不只为一个用户服务，经常为大量用户使用，因此在减少相应时间和带宽使用方面很有效：因为同一个缓存可能会被重用多次。
3. 网关缓存
也被称为代理缓存或反向代理缓存，网关也是一个中间服务器，网关缓存一般是网站管理员自己部署，从让网站拥有更好的性能。🙂
CDNS(网络内容分发商)分布网关缓存到整个（或部分）互联网上，并出售缓存服务给需要的网站，比如国内的七牛云、又拍云都有这种服务。

## 浏览器的缓存策略
