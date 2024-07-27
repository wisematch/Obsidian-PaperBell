---
location: [41.02, 28.57]
name: Istanbul
banner: "40 - Obsidian/附件/banners/global-tracking-banner.jpg"
banner_icon: 🌍
aliases: 
- 伊斯坦布尔
tags:
- city
- trip
banner_y: 0.52
---

```mapview
{"name":"Default","mapZoom":3,"centerLat":38.272688535980976,"centerLng":66.97265625000001,"query":"","chosenMapSource":0,"lock":false,"showLinks":false}
```

```dataviewjs

let folderChoicePath = "00 - 每日日记/DailyNote"
const files = app.vault.getMarkdownFiles().filter(file => file.path.includes(folderChoicePath))
let names = dv.current().aliases ? dv.current().aliases : [];
names.push(dv.current().name)

let arr = files.map(async(file) => { 
    const content = await app.vault.cachedRead(file) 
    let lines = await content.split("\n").filter(line => names.some(name => line.includes(name))) 
    //console.log(lines) 
    return ["[["+file.name.split(".")[0]+"]]", lines] 
}) 

Promise.all(arr).then(values => { 
    const beautify = values.map(value => { 
        const temp = value[1].map(line => { return line }) //美化要重写
        return [value[0],temp] 
    }) 
    const exists = beautify.filter(value => value[1][0] && value[0] != "[[未命名 10]]") .sort(value => value[0],'desc') 
    dv.table(["日期", "动态"], exists) 
})
```

May 27-31, 2024, FG, 第一次出国，游览土耳其伊斯坦布尔