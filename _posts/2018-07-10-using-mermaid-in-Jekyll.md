---
layout: post
title:  "如何在GitHub博客Jekyll日志系统中使用图表功能"
categories: Mermaid
tags:  Jekyll Mermaid
author: Evan
---
* content
{:toc}

在写博客时，会发现在Jekyll默认不支持图表功能，如果要在Jekyll中使用图表，可以使用[Mermaid](https://mermaidjs.github.io/)来创建图表。
## 使用步骤
- 首先[下载Mermaid](https://github.com/knsv/mermaid/releases)的JS包,下载最新的release版本，解压后在目录dist中获取js脚本mermaid.min.js。
- 将上一步下载的脚本mermaid.min.js上传到Jekyll的js目录。
- 在要使用图表的页面引用js脚本，如果想在所有日志文件中引用，可以直接将下面代码写入目录\_includes的head.html文件中,但这样可能会使不需要meraid图表也加载js脚本，导致页面加载太慢

```html
<script src="/js/mermaid.min.js"></script>
<script>mermaid.initialize({startOnLoad:true});</script>
```

- 使用下面方法在页面中插入图表

```html
<div class="mermaid">
    CHART DEFINITION GOES HERE
</div>
```

## 案例
流程图源码
```html
<script src="/js/mermaid.min.js"></script>
<script>mermaid.initialize({startOnLoad:true});</script>
<div class="mermaid">
graph TD
    A(Java)
    B(Java se)
    C(Java ee)
    D(Java me)
    A-.->B
    A-.->C
    A-.->D
    classDef className fill:#f9f,stroke:#333,stroke-width:4px;
    class B,C,D className;
    style A fill:#ccf,stroke:#f66,stroke-width:2px,stroke-dasharray: 5, 5
</div>
```
流程图效果图

<script src="/js/mermaid.min.js"></script>
<script>mermaid.initialize({startOnLoad:true});</script>
<div class="mermaid">
graph TD
    A(Java)
    B(Java se)
    C(Java ee)
    D(Java me)
    A-.->B
    A-.->C
    A-.->D
    classDef className fill:#f9f,stroke:#333,stroke-width:4px;
    class B,C,D className;
    style A fill:#ccf,stroke:#f66,stroke-width:2px,stroke-dasharray: 5, 5
</div>

序列图源码
```html
<div class="mermaid">
sequenceDiagram
    Alice->>Bob: Hello Bob, how are you?
    alt is sick
        Bob->>Alice: Not so good :(
    else is well
        Bob->>Alice: Feeling fresh like a daisy
    end
    opt Extra response
        Bob->>Alice: Thanks for asking
    end
</div>    
```
序列图效果图
<div class="mermaid">
sequenceDiagram
    Alice->>Bob: Hello Bob, how are you?
    alt is sick
        Bob->>Alice: Not so good :(
    else is well
        Bob->>Alice: Feeling fresh like a daisy
    end
    opt Extra response
        Bob->>Alice: Thanks for asking
    end
</div>  

## 参考
具体请参考【[Mermaid指南](https://mermaidjs.github.io/)】