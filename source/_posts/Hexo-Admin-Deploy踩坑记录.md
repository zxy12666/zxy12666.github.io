title: Hexo Admin Deploy踩坑记录
author: 张翔宇
tags:
  - hexo admin
categories:
  - hexo
date: 2019-03-01 16:22:00
---
## 参考

[hexo-admin/issues](https://github.com/jaredly/hexo-admin/issues/94)

## 背景

由于Windows的某种原因，依据网站的大部分教程，均会抛出`deploy Error: spawn UNKNOWN`异常。而GitHub上issues给出了相关解决方案，现在总结一下具体的过程

## 填坑

*   打开站点配置文件(`\xxxx.github.io\_config.yml`)，在`admin`中加入`deployCommand: &#39;sh hexo-deploy.sh&#39;`。示例：

    <figure class="highlight plain"><table><tr><td class="gutter"><pre><span class="line">1</span>
<span class="line">2</span>
<span class="line">3</span>
<span class="line">4</span>
<span class="line">5</span>
</pre></td><td class="code"><pre><span class="line">admin:</span>
<span class="line">  username: 看不见</span>
<span class="line">  password_hash: 看不见</span>
<span class="line">  secret: 看不见</span>
<span class="line">  deployCommand: &apos;sh hexo-deploy.sh&apos;</span>
</pre></td></tr></table></figure>

*   在根目录中(`\xxxx.github.io\`)新建`hexo-deploy.sh`文件，内容为：

    <figure class="highlight plain"><table><tr><td class="gutter"><pre><span class="line">1</span>
</pre></td><td class="code"><pre><span class="line">hexo g -d</span>
</pre></td></tr></table></figure>

*   打开deploy.js(`\xxxx.github.io\node_modules\hexo-admin\deploy.js`)，将`var proc = spawn(command, [message], {detached: true});`更改为`var proc = spawn((process.platform === &quot;win32&quot; ? &quot;hexo.cmd&quot; : &quot;hexo&quot;), [&#39;d&#39;, &#39;-g&#39;]);`
更改完后的代码：

    <figure class="highlight plain"><table><tr><td class="gutter"><pre><span class="line">1</span>
<span class="line">2</span>
<span class="line">3</span>
<span class="line">4</span>
<span class="line">5</span>
<span class="line">6</span>
<span class="line">7</span>
<span class="line">8</span>
<span class="line">9</span>
<span class="line">10</span>
<span class="line">11</span>
<span class="line">12</span>
<span class="line">13</span>
<span class="line">14</span>
</pre></td><td class="code"><pre><span class="line">module.exports = function (command, message, done) &#123;</span>
<span class="line">  done = once(done);</span>
<span class="line">  var proc = spawn((process.platform === &quot;win32&quot; ? &quot;hexo.cmd&quot; : &quot;hexo&quot;), [&apos;d&apos;, &apos;-g&apos;]);</span>
<span class="line">  var stdout = &apos;&apos;;</span>
<span class="line">  var stderr = &apos;&apos;;</span>
<span class="line">  proc.stdout.on(&apos;data&apos;, function(data)&#123;stdout += data.toString()&#125;)</span>
<span class="line">  proc.stderr.on(&apos;data&apos;, function(data)&#123;stderr += data.toString()&#125;)</span>
<span class="line">  proc.on(&apos;error&apos;, function(err) &#123;</span>
<span class="line">    done(err, &#123;stdout: stdout, stderr: stderr&#125;);</span>
<span class="line">  &#125;);</span>
<span class="line">  proc.on(&apos;close&apos;, function () &#123;</span>
<span class="line">    done(null, &#123;stdout: stdout, stderr: stderr&#125;);</span>
<span class="line">  &#125;);</span>
<span class="line">&#125;</span>
</pre></td></tr></table></figure>

## 效果

![效果图](http://hlx-blog.oss-cn-beijing.aliyuncs.com/18-5-1/52473923.jpg)