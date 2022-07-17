---
title: Gecko 1.9 Changes affecting websites
slug: Mozilla/Firefox/Releases/3/Site_compatibility
translation_of: Mozilla/Firefox/Releases/3/Site_compatibility
---
<div>{{FirefoxSidebar}}</div><p>此页设法提供在 <a href="cn/Gecko">Gecko</a> 1.8 和 Gecko 1.9 之间的变动概要，这些变动可能会影响某些网站的行为或网页渲染。</p>
<p>参见 <a href="cn/Firefox_3_for_developers">Firefox 3 开发者参考</a></p>
<h2 id=".E4.BA.8B.E4.BB.B6">事件</h2>
<h3 id=".E6.8D.95.E8.8E.B7_load_.E4.BA.8B.E4.BB.B6.E7.9B.91.E5.90.AC">捕获 load 事件监听</h3>
<p>在 Gecko 1.8 中，不能在图片上设置 load 事件监听。在 Gecko 1.9 中，已在 {{ Bug(234455) }} 中修复。但是在某些网站中，由于捕获 load 事件的事件监听器不正确而导致问题。参见 {{ Bug(335251) }} 中的讨论。要修复这个问题，出错的页面不再需要设置事件监听器。</p>
<p>例如，如下：</p>
<pre class="brush: js">window.addEventListener('load', yourFunction, true);
</pre>
<p>应该更改为：</p>
<pre class="brush: js">window.addEventListener('load', yourFunction, false);
</pre>
<p>事件捕获如何工作的解释，参见 <a href="http://www.w3.org/TR/DOM-Level-2-Events/events.html#Events-flow-capture">DOM Level 2 事件捕获</a></p>
<h3 id="preventBubble_.E5.B7.B2.E8.A2.AB.E7.A7.BB.E5.87.BA">preventBubble 已被移出</h3>
<h3 id=".E5.B0.91.E8.AE.B8.E6.97.A7.E7.9A.84.E4.BA.8B.E4.BB.B6_API_.E4.B8.8D.E5.86.8D.E8.A2.AB.E6.94.AF.E6.8C.81">少许旧的事件 API 不再被支持</h3>
<h2 id="DOM">DOM</h2>
<h3 id="WRONG_DOCUMENT_ERR">WRONG_DOCUMENT_ERR</h3>
<h2 id=".E8.8C.83.E5.9B.B4">范围</h2>
<h3 id="intersectsNode_.E5.B7.B2.E8.A2.AB.E7.A7.BB.E5.87.BA">intersectsNode 已被移出</h3>
<h3 id="compareNode_.E5.B7.B2.E8.A2.AB.E7.A7.BB.E5.87.BA">compareNode 已被移出</h3>
<h2 id="HTML">HTML</h2>
<h3 id=".3Cobject.3E_.E4.B8.AD.E7.9A.84.E8.AE.B8.E5.A4.9A_bug_.E5.B7.B2.E7.BB.8F.E4.BF.AE.E5.A4.8D">&lt;object&gt; 中的许多 bug 已经修复</h3>