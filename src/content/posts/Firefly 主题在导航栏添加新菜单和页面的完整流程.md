---
title: "Firefly 主题在导航栏添加新菜单和页面的完整流程"
description: "Firefly 主题在导航栏添加新菜单和页面的完整流程,修改配置文件，增加MD文件路由"
published: 2025-06-01
tags: ["公益", "教程"]
---

## 📁 需要修改/创建的 4 个文件

项目根目录/
├── src/
│   ├── config/
│   │   ├── navBarConfig.ts          # 1️⃣ 导航栏配置
│   │   └── LinkPresets.ts           # 2️⃣ 链接预设（可选）
│   ├── content/
│   │   ├── config.ts                # 3️⃣ 内容集合配置
│   │   └── welfare/                 # 4️⃣ 内容文件夹
│   │       ├── rural-education.md
│   │       ├── environment.md
│   │       └── animal-rescue.md
│   └── pages/
│       └── welfare/                 # 5️⃣ 页面路由文件夹
│           ├── rural-education.astro
│           ├── environment.astro
│           └── animal-rescue.astro

## 1️⃣ 导航栏配置 (src/config/navBarConfig.ts)

```typescript
// 添加公益项目菜单
links.push({
    name: "公益项目",
    icon: "material-symbols:favorite",
    children: [
        {
            name: "乡村教育支持",
            url: "/rural-education/",
            icon: "material-symbols:school",
        },
        {
            name: "环境保护计划",
            url: "/environment/",
            icon: "material-symbols:forest",
        },
        {
            name: "动物救助行动",
            url: "/animal-rescue/",
            icon: "material-symbols:pets",
        },
        {
            name: "捐赠渠道",
            url: "https://hangdn.com",
            external: true,
            icon: "material-symbols:volunteer-activism",
        },
    ],
});
```
### 关键点：

- 内部链接：url: "/welfare/xxx/"
- 外部链接：加上 external: true

## 2️⃣ 链接预设映射 (src/config/LinkPresets.ts) - 可选
如果需要像 LinkPreset.Welfare 那样使用，添加：
```
[LinkPreset.Welfare]: {
    name: "公益项目",
    url: "/welfare/",
    icon: "material-symbols:favorite",
},
```
## 3️⃣ 内容集合配置 (src/content/config.ts)

```
// 添加 welfare 集合
const welfareCollection = defineCollection({
    loader: glob({ pattern: "**/*.{md,mdx}", base: "./src/content/welfare" }),
    schema: z.object({
        title: z.string(),
        description: z.string().optional(),
        published: z.date().optional(),
        tags: z.array(z.string()).optional(),
    }),
});

export const collections = {
    posts: postsCollection,
    spec: specCollection,
    welfare: welfareCollection,  // ← 必须注册
};

```

## 4️⃣ 内容文件 (src/content/welfare/xxx.md)

```
---
title: "乡村教育支持"
description: "为偏远山区儿童提供教育资源"
published: 2025-06-01
tags: ["公益", "教育"]
---

# 乡村教育支持

这里是文章内容...

```

## 5️⃣ 页面路由文件 (src/pages/welfare/xxx.astro)

```
---
import { getEntry, render } from "astro:content";
import Markdown from "@components/common/Markdown.astro";
import MainGridLayout from "@/layouts/MainGridLayout.astro";

const post = await getEntry("welfare", "rural-education"); // 修改 slug

if (!post) {
    throw new Error("Page not found");
}

const { Content } = await render(post);
---

<MainGridLayout title={post.data.title} description={post.data.description}>
    <div class="flex w-full rounded-(--radius-large) overflow-hidden relative min-h-32">
        <div class="card-base z-10 px-9 py-6 relative w-full">
            <Markdown class="mt-2">
                <Content />
            </Markdown>
        </div>
    </div>
</MainGridLayout>

```

### 关键点：

- getEntry("集合名", "slug")
- 集合名必须与 content/config.ts 中注册的一致
- slug 对应 .md 文件名（不含扩展名）

## 🎯 核心要点
| |  |  |
| :----- | :----- | :------ |
|文件类型|	位置	|作用|
|.astro 路由|	src/pages/welfare/	|布局、获取内容、渲染页面|
|.md 内容|	src/content/welfare/	|实际的文章/页面内容|
|config.ts|	src/content/	|注册内容集合，定义数据结构|
|navBarConfig.ts|	src/config/	|配置导航栏菜单项|

## 🔄 完整工作流程

- 1.在 navBarConfig.ts 中添加菜单项（URL 路径）
- 2.在 content/config.ts 中注册集合（如 welfare）
- 3.创建 .md 文件 在 src/content/集合名/ 下
- 4.创建 .astro 文件 在 src/pages/集合名/ 下
- 5.重启开发服务器 测试
- 6.部署 到生产环境







