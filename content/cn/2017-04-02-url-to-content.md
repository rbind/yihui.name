---
title: 从网址到网页（真相竟然是……）
date: '2017-04-02'
slug: url-to-content
---

最近一年好像流行“真相竟然是这样”或者“啥啥竟然咋咋”的句式。从来不拒绝恶俗的我也在标题里跟个风。

![真相竟然是这样](https://slides.yihui.name/gif/elevator.gif)

简单科普一项跟域名有关的知识，也是回答一个基本问题：从你在浏览器地址栏敲完一个网址（或点开一个链接）到你看到那个网页，这期间背后发生了哪些事情？弄清楚这个问题的答案之后，我们就能更好理解域名有关的配置。比如你敲一个网址 `https://yihui.name/cn/` 然后回车。

解释域名知识之前先科普另一个小知识：如果链接是以文件夹名结尾，那么一般情况下服务器会在那个文件夹下找一个叫 `index.html` 的文件。注意这是“一般情况”，具体找哪个文件取决于服务器的软件配置。对于普通青年，当它是 `index.html` 也不会错到哪儿去。所以我们实际上访问的是 `https://yihui.name/cn/index.html`。^[显然不带 `index.html` 的链接更清爽。我一般看见别人的网址里带上 `index.html` 会觉得浑身不舒畅。为这个拥挤的世界节约十个字符是码农应尽的责任与义务！]

首先发生的事情是域名 `yihui.name` 需要被解析，最终目的是要找到域名对应的网站服务器，这个服务器通常是通过它的 IP 地址被找到。为了找到这个 IP 记录，得通过域名服务器（Name Server）。每个域名必须设置域名服务器，域名服务器上保存着这个域名的所有记录，这些记录有很多种，最常见的是 A 记录（域名到 IP 的映射关系）、MX 记录（邮件服务器）和 CNAME 记录（别名）。通常每一家域名注册商都会提供自己的域名服务器，但用户可以选择修改用别人家的域名服务器。下面是一条最常见的路径：

> 网址 → 域名 → 域名服务器 → 域名记录 → A 记录（或 CNAME 记录）→ IP → 服务器 → 文件夹 → 文件 → 内容返回到浏览器

继续上面的例子，大概也就是这样：

> `https://yihui.name/cn/` → `yihui.name` → `albert.ns.cloudflare.com`（域名服务器）→查到 CNAME 记录 `yihui.netlify.com` → 继续查到 CNAME 对应的 IP 地址 104.130.69.120  →找到服务器→服务器一看，哦，这个域名在我这里有收录，稍等，我把这个域名对应的文件夹下的 `cn/index.html` 文件返回给你→浏览器得到 HTML 文件并渲染出来

我当初入网站坑的时候，CNAME 记录还不太流行，通常域名都是设置 A 记录，也就是纯 IP 地址。现今 CNAME 也不是那么冷门了，例如 Github 就率先用了这个办法在 Github Pages 中支持用户自定义域名，大致说来就是 Github 会免费分配给你一个它的子域名，形如 `*.github.io`，你可以把你自己的域名设置一个别名记录，指向这个子域名，比如 `animation.yihui.name` 指向 `yihui.github.io`，由于 Github 有[我的记录](https://github.com/yihui/animation.yihui.name/blob/gh-pages/CNAME)，当用户访问 `animation.yihui.name` 时，Github 知道该把哪个网页返回给用户。CNAME 跟 A 记录比起来最大的优势就是 CNAME 是活的（一个域名指向另一个域名），而 A 记录是死的 IP 地址，CNAME 指向的域名可以随意搬家，而用户不需要去修改记录。用码农的话说，一个是传引用，一个是传值。

所有域名记录都称作 DNS 记录，每一家域名注册商应该都会同时提供域名服务器和 DNS 记录的修改。如果你选择用域名注册商默认的域名服务器，那么你可以在它家直接修改 DNS 记录。如果你选择用别家的域名服务器，那就需要在别家修改 DNS 记录，因为 DNS 记录是归域名服务器管，而不归具体的域名注册商管。

说了这一大圈，终于可以解释我年初的[小绿锁](/cn/2017/01/cloudflare/)一文中提到的 Cloudflare 了。说到底，它就是充当了域名服务器的角色，顺便从域名层面上提供了很多别的功能（如 CDN 加速、压缩 HTML/JS/CSS 文件等）。你可以在你的域名注册商那里修改域名服务器指向 Cloudflare 提供的域名服务器，然后在 Cloudflare 上添加 DNS 记录做域名解析。

如果你的网站是静态网站（纯 HTML 页面），那么我强力推荐 [Netlify](https://www.netlify.com)。这是我见过的综合性能最好的一家静态网站发布商，它的免费服务中已经有了很多实用的功能，比如支持 Hugo/Jekyll 等静态网站编译工具、免费 https、自定义重定向，等等。静态网站发布工具的传统大牌是亚马逊的 S3（跟 R 里面的 S3 毫无关系），但说实话亚马逊的工具我用起来从来没觉得顺手过，可能我是一个假的开发者。之前我也是一直用 Github Pages，虽然也挺好用，但功能比 Netlify 少，更重要的是，Github Pages 网站必须通过 GIT 管理，我的洁癖有点不太能接受用 GIT 管理副产品，也嫌麻烦。版本控制应该是用来控制源代码，当然，管理副产品也不是完全没有意义，有时候你可能确实想看看网站里那些 HTML 页面有什么异常的 git 差异。

Netlify 把网站发布简化到了极致，你甚至可以直接拖拽一个编好了的网页文件夹上传发布，几秒钟的事儿。当然，每次重复拖拽上传也很麻烦，所以我是把 Github 库和 Netlify 连起来，每次我只需要管往我的 Github 源代码库里推送新内容，编译发布的事情就留给 Netlify 去做了。如果你是用 blogdown 建站，那么 Netlify 上的编译命令可以选 `hugo_0.19`（或者 0.18 或更低版本），发布文件夹填 `public`^[因为 Hugo 默认会把网站编译到 `public` 文件夹下，类似的，Jekyll 会编译到 `_site` 文件夹下。]。Netlify 默认会分配一个随机的子域名给你，形如 `*.netlify.com`，你可以选择用它，也可以选择自定义域名^[如果你没有自己的域名而且想注册的话，参见我前面的文章《[域名起名服务](/cn/2017/03/domain-name/)》]。自定义域名在 DNS 层面的设置也就是把这个域名的 CNAME 指向 Netlify 分配给你的子域名。

如果你用了 Netlify，其实就没什么必要用 Cloudflare 了，因为后者的功能前者基本上都已经提供了，所以你没必要在域名注册商那里修改域名服务器。目前据我的了解，只有两项功能后者有前者的免费服务里没有：

1. 压缩 HTML/JS/CSS 文件。Cloudflare 可以免费压缩这些文件以节省带宽，其实基本上也就是把你的 HTML 源代码里的无意义空行空格删掉。这个功能在 Netlify 上属于收费功能。

1. 加密邮箱地址。Cloudflare 可以自动把页面上的邮箱地址用 JavaScript 代码加密，这样垃圾广告商就不能直接搜索到邮箱了，但人眼仍然可以看见正确的邮箱地址。

如果这两个功能对你意义不大，那么就只剩一个问题要考虑了：你的域名要不要用 www 前缀。这个问题有点让人头疼，也是域名问题里的一个大坑。时间有限，我不多说。如果求简单，那么就用 www，Netlify 上的域名设置参见[它的文档](https://www.netlify.com/docs/custom-domains/)。如果追求简短网址而不想要 www 的话，技术细节会复杂一些但配置也并没那么复杂，客官们可以看[这篇文章](https://www.netlify.com/blog/2017/02/28/to-www-or-not-www/)。简单说来如下：

带 www 前缀的域名实际上是一个子域名（二级域名），任何子域名都可以设置别名记录，但顶级域名（不带 www）理论上不能设置别名。Cloudflare 提供了一个黑招来绕过这个问题，这个招数名称叫“CNAME Flattening”，说白了也就是让顶级域名也可以设置别名。如果你真心希望你的网址前面不要带 www（短啊），那么可能只能选择 Cloudflare 了；否则，没有使用 Cloudflare 的必要。另外，尽管 Cloudflare 号称提供云加速，实际上在天朝访问时它是减速器，网站必须要备案^[天朝龟腚了不备案的网站不许云加速。]以及是企业付费用户才能在天朝加速。当然，即使你使用 Cloudflare 做域名解析服务，你也可以把它当做纯域名解析工具，不使用加速功能。

最后对 Cloudflare 用户再说一个小坑：如果你是 Cloudflare 和 Netlify 同时使用，那么在 Netlify 上启用 https 时，需要先关闭 Cloudflare 的 DNS 设置中的 CDN 加速功能，即：点一下那个云标志，让它变灰。然后在 Netlify 上启用 https，否则可能无法启用。启用完毕了在 Cloudflare 上要不要重新开启 CDN 都无所谓，看你在不在乎我前面说的几个功能了。

等再有空的时候我再说一说邮箱的配置。对于技术可以自理的客官，直接去 [Migadu](https://www.migadu.com) 注册并按照指引设置 DNS 记录即可（主要是 MX 记录）。有一个自己域名的邮箱看起来很别致，比起铺天盖地的 Gmail 或 QQ 邮箱帅多了。早年间，Gmail 还免费提供邮箱服务，后来关掉了变成了收费服务，我入坑早，所以我还有 `xie@yihui.name` 邮箱。我这里说的 Migadu 是众多免费邮箱服务中的一种，免费功能有限，但如果你纯粹只是为了帅，那也完全够用了。我的黑招是，开启 Migadu 服务之后只用邮箱别名（Alias），不用实体邮箱，也就是把你自定义的别名邮箱转发到你真正的邮箱。

至此，一个静态网站所需要的基本知识，除了 R 包 [blogdown](https://github.com/rstudio/blogdown) 话题略大我没细说之外，应该已经够多数人学习、掌握、应用了。