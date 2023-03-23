# 基于 JavaScript 和 Essentia 的音频特征提取

> 原文：<https://betterprogramming.pub/audio-features-extraction-with-javascript-ac8c9cd9903a>

## 以下是如何使用 Essentia.js 来发现你喜欢的歌曲有多有活力！

![](img/347efad2330eab06a933b4e6755442ca.png)

照片由[斯蒂芬尼·安德拉德](https://unsplash.com/@stefany_andrade?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

你有没有想过 Spotify 是如何向用户推荐音乐的？

10 月，我为分析网站 Vidhya [Blogathon 25](https://datahack.analyticsvidhya.com/contest/data-science-blogathon-25) 写了一篇[获胜指南](https://www.analyticsvidhya.com/blog/2022/11/creating-a-music-streaming-backend-like-spotify-using-mongodb/)。在那篇文章中，我分享了如何构建一个副本 Spotify 后端，包括一个推荐系统。

但今天，我们将重点分析 NodeJS 音乐的不同方面。与我的 Blogathon 文章类似，我们将使用一个名为 Essentia 的工具。

# 什么是本质？

嗯， [Essentia](https://essentia.upf.edu/) 是一个从音频中检索信息的工具库。它包含一个分析算法语料库，可以执行速度和音调提取、响度检测等任务。他们的免费开源库也有一套 Tensorflow 深度学习模型，用于更高级的分析，如流派和情绪识别。

Essentia 来自音乐科技集团，也是 freesound.org[背后的同一个团队。](http://freesound.org/)

他们的原始库是 C++，但是他们有 Python 和 JavaScript 的绑定。

为了了解哪些功能可以用于歌曲分析，我们可以看看 [Spotify 的 API](https://developer.spotify.com/documentation/web-api/reference/#/operations/get-audio-features) 中提供的一些音频功能。

以下是我挑选的:`danceability, duration, energy, key, mode, loudness, tempo`

现在开始分析吧！

# 安装 Essentia.js

在节点项目内的终端中，运行以下命令:

```
npm i essentia.js
```

# 解码音频文件

所有传入 Essentia 算法的音频都需要解码成一个数组，然后是一个向量。

```
npm i audio-decode
```

这个库可以解码 MP3 和 WAV 文件，并返回每个音频通道的数据。解码后的音频需要转换成矢量(一个`C++`风格的矢量)。

我们现在可以使用以下内容来导入库并设置 Essentia:

```
const { Essentia, EssentiaWASM } = require("essentia.js");
const fs = require("fs");
const decode = require("audio-decode");
const essentia = new Essentia(EssentiaWASM);
```

这是解码音频的函数:

```
const decodeAudio = async (filepath: string) => {
    const buffer = fs.readFileSync(filepath);
    const audio = await decode(buffer);
    const audioVector = essentia.arrayToVector(audio._channelData[0]);
    return audioVector;
};
```

为了测试 Essentia 的算法实现，我们将使用来自 Pixabay 的这个[音频文件](https://cdn.pixabay.com/audio/2022/10/18/audio_31c2730e64.mp3)。将其作为“audio.mp3”放入项目文件夹中

```
(async () => {
    const path = "./audio.mp3";
    const data = await decodeAudio(path);

    // ...
})()
```

# 音频分析

# 可跳舞性

可舞性指的是一首歌适合跳舞的程度。它是其他因素的混合物，如节拍强度和节奏。

```
const computed = essentia.Danceability(data);
// { danceability: N }

const danceability = computed.value;
```

# 持续时间

持续时间是一段音乐的长度。

```
const computed = essentia.Duration(data);
// { duration: N }

const duration = computed.value;
```

# 活力

从数学上来说，信号的能量就是曲线下的面积。就音乐而言，能量衡量的是强度和活动性。

```
const computed = essentia.Energy(data);
// { energy: N }

const energy = computed.value;
```

# 键和模式

音乐基调是构成歌曲基础的一组音符。

调式指的是音阶的类型——大调还是小调。

```
const computed = essentia.KeyExtractor(data);
// { key: "C" | "D" | "E" ..., scale: "major" | "minor", strength: N }

const KEYS = ["C", "D", "E", "F", "G", "A", "B"];

const key = KEYS.indexOf(computed.key);
const mode = computed.scale === "major" ? 1 : 0;
```

# 音量

响度是指一首歌的音量有多大，单位是分贝(dB)。

```
const computed = essentia.DynamicComplexity(data);
// { dynamicComplexity: N, loudness: N }

const loudness = computed.loudness;
```

# 拍子

节奏是一段音乐的速度，单位是每分钟的节拍数。

```
const computed = essentia.PercivalBpmEstimator(data);
// { bpm: N }

const tempo = computed.bpm;
```

以上就是分析的所有音频特征。但是现在我们有了所有这些信息，我们该如何处理呢？嗯，一个建议是建立一个歌曲推荐系统，我已经在这里[解释过了](https://www.analyticsvidhya.com/blog/2022/11/creating-a-music-streaming-backend-like-spotify-using-mongodb/#h2_6)。另一个想法是通过 REST API 使其可用。

# 构建 REST API

对于这个 API，我们将使用 [express](https://www.npmjs.com/package/express) 来处理传入的请求并发送响应。此外，我们可以使用[强大的](https://www.npmjs.com/package/formidable)来处理文件上传，所以在这里查看如何使用它[。](https://medium.com/better-programming/3-ways-to-upload-files-to-google-cloud-storage-with-nextjs-and-formidable-3b58707e7886)

因此，当客户端上传文件时，我们将对其进行解码、分析，然后返回音频特征。

```
import express, { NextFunction } from "express";

import formidable from "formidable";

import fs from "fs";
import { IncomingMessage } from "http";
import { Essentia, EssentiaWASM } from "essentia.js";
import decode from "audio-decode";
import IncomingForm from "formidable/Formidable";

const app = express();
const port = 3000;

const essentia = new Essentia(EssentiaWASM);

const KEYS = ["C", "D", "E", "F", "G", "A", "B"];

app.use(express.json());
app.use(express.urlencoded({ extended: true }));

const parseForm = async (
    form: IncomingForm,
    req: IncomingMessage,
    next: NextFunction
): Promise<{ fields: formidable.Fields; files: formidable.Files }> => {
    return await new Promise((resolve) => {
        form.parse(
            req,
            function (
                err: Error,
                fields: formidable.Fields,
                files: formidable.Files
            ) {
                if (err) return next(err);
                resolve({ fields, files });
            }
        );
    });
};

const decodeAudio = async (filepath: string) => {
    const buffer = fs.readFileSync(filepath);
    const audio = await decode(buffer);
    const audioVector = essentia.arrayToVector(audio._channelData[0]);
    return audioVector;
};

app.post("/upload", async (req, res, next) => {
    const form = formidable();

    const { files } = await parseForm(form, req, next);

    // The file uploaded must have the field name "file"
    const file = files.file as any;

    const data = await decodeAudio(file.filepath);

    const danceability = essentia.Danceability(data).danceability;
    const duration = essentia.Duration(data).duration;
    const energy = essentia.Energy(data).energy;

    const computedKey = essentia.KeyExtractor(data);
    const key = KEYS.indexOf(computedKey.key);
    const mode = computedKey.scale === "major" ? 1 : 0;

    const loudness = essentia.DynamicComplexity(data).loudness;
    const tempo = essentia.PercivalBpmEstimator(data).bpm;

    res.status(200).json({
        danceability,
        duration,
        energy,
        key,
        mode,
        loudness,
        tempo,
    });
});

app.listen(port, () => {
    return console.log(`Express server listening at http://localhost:${port}`);
});
```

# 结论

仅此而已！如果你喜欢这篇文章，请继续关注。

如果您对如何使用这些音频功能有任何其他建议，请发表评论。

你可以在 GitHub 上找到这篇文章的完整代码。

现在再见。

*原发布于*[*https://cs 310 . hash node . dev*](https://cs310.hashnode.dev/audio-features-extraction-with-javascript-and-essentia)*。*