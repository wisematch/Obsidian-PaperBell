---
name: Scaling Law
aliases: 
- 规模效应
- Scaling Law
- scaling law
- 规模理论
ch: 规模理论
framework:
tags:
- concept
---
## Description
规模效应，OpenAI在2020年提出的概念，具体如下：
1. 对于Decoder-only的模型，计算量$C$(Flops), 模型参数量$N$, 数据大小$D$(token数)，三者满足: $C \approx 6ND$。(推导见论文)  
    
2. 模型的最终性能**主要与**计算量$C$(Flops), 模型参数量$N$, 数据大小$D$(token数)相关，而与模型的具体结构(层数/深度/宽度)基本无关。

这个基本概念成为大模型的**理论支撑**。

但是**计算效率最优**这个观点是针对**训练阶段**而言的，即学到的训练模型最强，并不是推理阶段，实际应用中更关注推理性能。

Meta在LLaMA的观点是：给定模型的目标性能，并不需要用最优的计算效率在**最快**时间训练好模型，而应该在更大规模的数据上，训练一个相对**更小**模型，这样的模型在推理阶段的成本更低，尽管训练阶段的效率不是最优的（同样的算力其实能获得更优的模型，但是模型尺寸也会更大）。根据Scaling Law，10B模型只需要200B的数据，但是作者发现7B的模型性能在1T的数据后还能继续提升。


**证伪？**
[推翻OpenAI结论，DeepMind重新定义预训练的参数和规模关系！ (sina.cn)](https://k.sina.cn/article_1494921451_591ab0eb019013vgi.html?from=tech)
Training Compute-Optimal Large Language Models [2203.15556 (arxiv.org)](https://arxiv.org/pdf/2203.15556)


有许多小模型达到越级的性能，如Mobile LLM。

[(17) Inoreader - 挑战Scaling Law，Meta发布移动端350M小模型MobileLLM，性能比肩7B LLaMA-v2](https://www.inoreader.com/article/3a9c6e747c043e99-scaling-lawmeta350mmobilellm7b-llama-v2)

## 相关论文

```dataviewjs

let names = dv.current().aliases ? dv.current().aliases : [];
names.push(dv.current().name)

// 参考 https://forum.obsidian.md/t/for-loops-and-dataviewjs/46284
// every: 每个要素都在；
// some: 某个要素在

dv.table(["论文","期刊","年份"],
dv.pages(`#paper`)
	.where(t => names.some(x => t.concepts.includes(x)))
	.map(b => [b.file.link, b.journal, b.paper_date])
	.sort(b => b.paper_date, 'desc')
)
```
## 相关想法

```dataviewjs

let folderChoicePaths = ["00 - 每日日记/DailyNote", "Inputs", "Outputs", "Projects"];
const specificTag = "#SC/想法"; // 指定要检查的标签，可以更改

const files = app.vault.getMarkdownFiles().filter(file => folderChoicePaths.some(path => file.path.includes(path)) );

let names = dv.current().aliases ? dv.current().aliases : [];
names.push(dv.current().name);
names.push(dv.current().ch);

let arr = files.map(async(file) => { 
    const content = await app.vault.cachedRead(file);
    // 确保文件内容包含特定标签
    if (content.includes(specificTag)) {
        let lines = content.split("\n").filter(line => names.some(name => line.includes(name)));
        return ["[[" + file.name.split(".")[0] + "]]", lines];
    }
    return null; // 如果不包含标签，返回 null
}); 

Promise.all(arr).then(values => { 
    // 过滤掉 null 结果
    const filteredValues = values.filter(value => value != null);

    const beautify = filteredValues.map(value => { 
        const temp = value[1].map(line => line); // 美化处理
        return [value[0], temp]; 
    });

    const exists = beautify.filter(value => value[1][0] && value[0] != "[[未命名 10]]")
        .sort((a, b) => a[0].localeCompare(b[0])); // 排序处理修正为 localeCompare

    dv.table(["日期", "动态"], exists);
});

```
