开源大模型食用指南 · 教科书版（self-llm 重构）

Datawhale · self-llm 重构教科书

# 开源大模型食用指南 教科书版

把 `github.com/datawhalechina/self-llm` 里零散的 50+ 模型教程，重新组织成一本你能一口气读完、读完就能上手的“主线手册”。

面向人群：**完全零基础**、想自己把开源大模型跑起来并微调成专属模型的小白。

编写原则：先讲清楚“为什么”，再讲“怎么做” · 用生活比喻替代术语堆砌 · 每一章都给可直接抄的代码片段与对比表

🧭 这本书和原仓库的关系

原仓库像一座“模型超市”：每个模型一个货架，货架上摆着环境、部署、微调几张说明书。好处是细，坏处是**没有主线**——你看了 10 个模型，会觉得每篇都在重复又每篇都不一样。
本书做的事：先把所有货架背后**共同的套路抽出来**（第 2~4 章），再用 1~2 个代表模型做一遍完整示范，最后告诉你“剩下 50 个模型怎么用同一套思维去读”。**掌握主线，你就掌握了全部。**

## 第 0 章大模型是什么（先建立直觉）

💡 一句话大白话

大模型（LLM）就是一个“读过了几乎整个互联网、然后被训练成超级接话高手”的程序。你给它一句话（提示 / prompt），它接着往下写最合理的下一句。

🔧 生活比喻

把它想成一家**超级图书馆里泡大的作家**：你开头写“从前有座山”，他顺着上下文续写“山里有座庙”。他不会“查资料”，而是靠训练时记住的语言规律“猜”下一个字。猜得越准，模型越聪明。

### 0.1 三个你一定会遇到的词

| 词 | 大白话 | 仓库里对应什么 |
| --- | --- | --- |
| **预训练 (Pretrain)** | 让模型“读万卷书”，学会通用的语言规律。这一步由厂商完成，你不用做。 | 你下载的模型权重，已经是预训练好的。 |
| **部署 (Deploy)** | 把模型“请”到你的电脑/服务器上，让它能对话、能响应请求。 | 第 3 章的五个方法。 |
| **微调 (Fine-tune)** | 给通用模型“开小灶”，用你自己的数据让它变成某个领域的专家（比如甄嬛语气、数学老师）。 | 第 4 章。 |

### 0.2 为什么需要“本地部署”，而不是直接用网页版？

- **免费 & 无限次**：网页/API 按量收费，本地部署一次装好随便用。
- **数据隐私**：敏感数据不出内网，适合公司/科研场景。
- **可定制**：只有本地模型能被你微调成专属模型。
- **需要硬件**：模型越大越吃显卡（GPU）。7B 模型约需 16GB 显存，70B 需要多卡。这是本地的最大门槛。

⚠️ 先认清你的“家底”

本书默认你在 **Linux + NVIDIA 显卡（CUDA）** 环境。云算力平台（AutoDL、阿里云）是最省事的入门方式——租一台带 GPU 的机器，按小时计费。原仓库 `models/General-Setting/02-AutoDL开放端口.md` 专门讲怎么把云机器的端口映射到本地浏览器。

## 第 1 章仓库地图与学习路线（掌握主线的钥匙）

### 1.1 仓库的真实结构（剥掉迷惑）

别被 50+ 模型吓到。仓库顶层其实只有这几类文件夹：

| 文件夹 | 它是干什么的 | 你要不要细看 |
| --- | --- | --- |
| `models/` | **主角**。50+ 模型，每个模型一个子文件夹，里面是该模型的部署+微调教程。 | ✅ 核心，按本文主线读 |
| `models/General-Setting/` | 所有模型**通用**的铺垫：换源、开端口、模型下载、怎么提 Issue/PR。 | ✅ 必读，相当于“公共地基” |
| `dataset/` | 微调用的示例数据集（如甄嬛语料 `huanhuan.json`）。 | 微调时参考 |
| `examples/` | 完整项目案例：Chat-嬛嬛、天机、AMChat、数字生命。 | 学完主线后进阶看 |
| `models_amd/` `models_ascend/` `models_mlx/` | AMD 显卡 / 华为昇腾 NPU / 苹果 M 芯片的专属版本。 | 只有用对应硬件才看 |

### 1.2 每个模型文件夹的“标准长相”

这是全书的**最重要发现**：几乎每个模型文件夹内部，都是同一套编号文件。

```
models/Qwen2.5/
├─ 01-...FastApi 部署调用.md     ← 方法①：做成 API 服务
├─ 02-...Langchain 接入.md       ← 方法⑤：接应用框架
├─ 03-...vLLM 部署调用.md        ← 方法④：高性能推理
├─ 04-...WebDemo 部署.md         ← 方法③：网页聊天界面
├─ 05-...Lora 微调.md            ← 微调（第4章）
├─ 06-...o1-like 推理链.md       ← 进阶玩法
├─ 07-...Lora 微调 SwanLab版.md  ← 微调+可视化
├─ readme.md                     ← 该模型简介
└─ images/                       ← 截图
```

🗺️ 主线地图（记住这张图，等于记住整本书）

1. **环境配置** — 装好 Python/conda、换源、下模型
2. **模型部署** — 让模型能对话（5 种方法）
3. **模型微调** — 用自有数据变成专属模型
4. **应用/进阶** — 多模态、推理链、项目实战

原仓库官方建议：**先环境 → 再部署 → 后微调**。因为环境是基础、部署是上手、微调是进阶。初学者优先挑 Qwen2.5 InternLM2 MiniCPM 这类资料全、门槛低的模型。

### 1.3 推荐学习顺序

1. 读完本书第 0~4 章（建立主线）。
2. 挑 **Qwen2.5** 一个模型，照着它的 01→04 文件亲手跑一遍部署。
3. 跑通后，照它的 05/07 文件做一次 LoRA 微调（用仓库自带甄嬛数据）。
4. 换一个你感兴趣的多模态模型（如 Qwen2-VL）体会差异。
5. 读 `examples/` 里的项目，把能力串成产品。

## 第 2 章环境配置（地基，做错后面全垮）

💡 一句话大白话

环境配置 = 给你的模型准备一间“带对电、装对软件”的房间。房间不对，模型根本进不来。

### 2.1 四个地基动作（对应 `General-Setting`）

| 动作 | 解决什么问题 | 对应文件 |
| --- | --- | --- |
| ① pip / conda 换源 | 国内下载快，不然卡死 | `01-pip、conda换源.md` |
| ② 开放端口 | 云机器上的网页/接口能在你本地浏览器打开 | `02-AutoDL开放端口.md` |
| ③ 模型下载 | 把模型权重从网上下到本地 | `03-模型下载.md` |
| ④ 提 Issue / PR | 遇坑求助、给仓库做贡献 | `04-Issue&PR&update.md` |

### 2.2 换源（每个模型教程第一步都会让你做）

```
# pip 换成清华源，下载飞快
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple

# conda 换源（可选）
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
```

### 2.3 建一个独立的 Python 环境（强烈建议）

🔧 生活比喻

不同模型要的 Python / 依赖版本像不同菜系的“火候”。**Conda 虚拟环境**就是给每道菜单独备一口锅，互不串味。否则 A 模型装完，B 模型就跑不起来了。

```
# 创建一个叫 qwen 的隔离环境，Python 3.10 最稳
conda create -n qwen python=3.10 -y
conda activate qwen   # 进入这个环境，之后装的包只在这里
```

### 2.4 把模型下载下来（三种主流方式）

模型权重（几 GB 到上百 GB 的文件）要从模型托管平台拉到本地。仓库教你 4 种，最常用 2 种：

#### 方式 A：HuggingFace + 国内镜像（最通用）

```
pip install -U huggingface_hub

# 新建 download.py，用镜像地址加速
import os
os.environ['HF_ENDPOINT'] = 'https://hf-mirror.com'   # 国内加速镜像
os.system('huggingface-cli download --resume-download '
          'Qwen/Qwen2.5-7B-Instruct --local-dir ./Qwen2.5-7B')
```

#### 方式 B：ModelScope（阿里，国内最稳）

```
pip install modelscope transformers

from modelscope import snapshot_download
model_dir = snapshot_download(
    'qwen/Qwen2.5-7B-Instruct',
    cache_dir='./models'   # 最好写绝对路径
)
```

⚠️ 新手最容易栽的坑

- **路径要用绝对路径**（如 `/root/models/...`），相对路径在 Linux 下常出错。
- 模型很大，**务必用 `--resume-download` 断点续传**，别一次下完。
- 显存不够时，先看第 3 章的“量化”再决定下什么精度的模型。

## 第 3 章部署五法（让模型开口说话）

💡 一句话大白话

“部署”就是把下好的模型加载进内存、能接收你的话并回话。仓库里同一件事写了 5 种姿势，**本质都是“加载模型 + 接收输入 + 生成回复”**，区别只在于“谁来调用、给谁看”。

| 方法 | 它适合什么场景 | 你看到的样子 | 难度 |
| --- | --- | --- | --- |
| **① Transformer 基础调用** | 写脚本直接跑，理解原理 | 终端里 print 出回复 | ★ |
| **② FastAPI 部署** | 做成接口，给别人/程序调用 | 一个 http 服务 `/chat` | ★★ |
| **③ WebDemo** | 做个网页聊天界面自己玩 | 浏览器里的聊天框（Gradio） | ★★ |
| **④ vLLM 部署** | 高并发、高吞吐、要快 | 批量高速推理服务 | ★★★ |
| **⑤ LangChain 接入** | 接知识库 / 做 Agent 应用 | RAG、智能体工作流 | ★★★ |

🗺️ 五法怎么选

先跑通 **①** 理解原理 → 想给别人用就做 **②** → 想自己点着玩就做 **③** → 要扛流量/追求速度用 **④** → 要接知识库做产品用 **⑤**。

### 3.1 方法①：Transformer 基础调用（必读，万法之源）

所有部署都从“加载模型和分词器”开始。记住这两行就是你和模型之间的翻译官：

```
from transformers import AutoModelForCausalLM, AutoTokenizer

# 1) 加载（把模型权重读进显存）
model = AutoModelForCausalLM.from_pretrained(
    "./Qwen2.5-7B", device_map="auto", torch_dtype="auto")
# 2) 分词器（把文字↔数字互转）
tokenizer = AutoTokenizer.from_pretrained("./Qwen2.5-7B")

# 3) 对话
messages = [{"role":"user","content":"你好，介绍一下你自己"}]
text = tokenizer.apply_chat_template(messages, tokenize=False, add_generation_prompt=True)
inputs = tokenizer(text, return_tensors="pt").to("cuda")
outputs = model.generate(**inputs, max_new_tokens=512)
print(tokenizer.decode(outputs[0], skip_special_tokens=True))
```

### 3.2 方法②：FastAPI（把模型变成接口）

本质：把上面的“对话”包成一个网页能请求的接口。别人发 JSON 过来，你回 JSON。

```
from fastapi import FastAPI
from pydantic import BaseModel
app = FastAPI()

class Req(BaseModel):
    prompt: str

@app.post("/chat")
def chat(req: Req):
    # 这里复用 3.1 的 model / tokenizer
    inputs = tokenizer(req.prompt, return_tensors="pt").to("cuda")
    out = model.generate(**inputs, max_new_tokens=512)
    return {"response": tokenizer.decode(out[0], skip_special_tokens=True)}
```

启动：`uvicorn main:app --host 0.0.0.0 --port 8000`，再用 `02-AutoDL开放端口.md` 把 8000 端口映射到本地。

### 3.3 方法③：WebDemo（Gradio 聊天界面）

几行代码生成一个浏览器聊天框，最适合自己体验。

```
import gradio as gr
def reply(text):
    # 复用 model/tokenizer 生成
    return generated_text

gr.ChatInterface(reply).launch(server_name="0.0.0.0", server_port=7860)
```

### 3.4 方法④：vLLM（要快、要扛量时用）

vLLM 用“连续批处理(PagedAttention)”技术，吞吐量远超原生 transformers，是**上线服务**的首选。

```
from vllm import LLM, SamplingParams
llm = LLM(model="./Qwen2.5-7B")
params = SamplingParams(temperature=0.8, max_tokens=512)
outputs = llm.generate(["你好，介绍一下你自己"], params)
print(outputs[0].outputs[0].text)
```

### 3.5 方法⑤：LangChain 接入（接知识库 / 做应用）

LangChain 是把“大模型 + 你的资料 + 工具”串起来的框架。最常见用法是 **RAG（检索增强）**：先搜你自己的文档，再把相关段落喂给模型回答。

```
from langchain_community.llms import HuggingFacePipeline
# 把本地模型包成 LangChain 能用的对象
llm = HuggingFacePipeline(pipeline=your_pipe)
# 之后即可接 VectorStore / Retriever 做知识库问答
```

🔧 生活比喻

原生 transformers 像“你直接跟厨师点菜”；LangChain 像“你雇了个管家，管家先去档案室翻资料、再综合厨师意见，最后给你一份带出处的报告”。做产品离不开它。

### 3.6 关键概念：量化（显存不够时的救命绳）

| 精度 | 显存占用 | 说明 |
| --- | --- | --- |
| FP16 / BF16 | 7B≈14GB | 原汁原味，最准 |
| INT8 | 7B≈7GB | 略损精度，省一半 |
| INT4 / GPTQ / AWQ | 7B≈4GB | 笔记本也能跑，精度可接受 |

仓库里很多部署教程会写 `load_in_4bit=True` 或 `quantization_config`，就是量化。显存小就开 4bit。

## 第 4 章模型微调（把通用模型变成“你的”模型）

💡 一句话大白话

微调 = 给通用模型做“岗前培训”。只喂它你关心领域的对话样本，它就学会用那种风格/知识回答。

🔧 生活比喻

预训练模型是“通才毕业生”；**全参数微调**是让他回炉重造四年（贵、慢、要很多显卡）；**LoRA/QLoRA** 是只给他报个两周速成班（便宜、快、只改一小部分）。新手一律从 LoRA 起步。

### 4.1 两种微调路线对比

|  | 全参数微调 (Full) | LoRA / QLoRA（推荐） |
| --- | --- | --- |
| 改动的参数 | 全部 | 只加一小对“适配器”矩阵 |
| 显存需求（7B） | ≥80GB（多卡） | ≥16~24GB（单卡可） |
| 速度 / 成本 | 慢、贵 | 快、省 |
| 仓库常见文件 | 少见 | `05-...Lora 微调.md` |
| QLoRA 是什么 | — | LoRA + 4bit 量化，显存再砍一刀 |

### 4.2 微调四步法（所有模型通用）

1. **准备数据** — 整理成对话 jsonl
2. **写配置** — 选 LoRA 参数
3. **跑训练** — 启动脚本
4. **合并 & 测试** — 加载适配器对话

#### 步骤 1：数据集长什么样

仓库 `dataset/huanhuan.json`（甄嬛语料）是标准格式——一串“人机多轮对话”：

```
[
  {
    "conversation": [
      {"role": "user",      "content": "你是谁？"},
      {"role": "assistant", "content": "臣妾是甄嬛，甄嬛小婉转……"}
    ]
  }
]
```

#### 步骤 2~3：用 XTuner / PEFT 跑 LoRA（范式代码）

```
# 以 XTuner 为例（仓库 InternLM / Qwen 系列常用）
# 1. 准备数据：把 json 转成 XL 格式
xtuner convert shopmallmm ...  # 详见各模型 05 文件

# 2. 复制并改配置
xtuner copy-cfg qwen2_5_lora ./

# 3. 启动训练（QLoRA 会自己量化基座）
xtuner train ./qwen2_5_lora.py

# 4. 把 LoRA 权重转回完整模型
xtuner convert pth_to_hf ./qwen2_5_lora.py ./iter_xxx.pth ./hf_adapter
```

⚠️ 关键参数直觉

- `r`（秩）：LoRA 容量，常用 8/16/32，越大越“学得猛”但也越易过拟合。
- `lora_alpha`：缩放，常设为 2×r。
- `learning_rate`：LR，LoRA 一般 1e-4 ~ 2e-4。
- `max_length`：单条样本最大长度，超了截断。

#### 步骤 4：加载微调后的模型对话

```
from peft import AutoPeftModelForCausalLM
model = AutoPeftModelForCausalLM.from_pretrained("./hf_adapter",
        device_map="auto", torch_dtype="auto")
# 之后对话代码与 3.1 完全相同
```

### 4.3 训练过程怎么“看得到”（可视化）

仓库 07 文件常用 **SwanLab**（国产，类 TensorBoard）记录 loss 曲线。训练脚本里加一行 logger 即可在网页看曲线，判断有没有训崩。

## 第 5 章多模态与进阶（从“会聊天”到“会看会想”）

### 5.1 多模态模型（MLLM）

普通 LLM 只吃文字；**多模态**模型还能吃图片/音频/3D。仓库里这类模型命名带后缀：

| 模型 | 能处理 |
| --- | --- |
| `Qwen2-VL` / `Qwen3-VL` | 图片 + 视频 |
| `Qwen-Audio` | 音频 |
| `GLM-4.1V-Thinking` | 图片 + 深度思考 |
| `Hunyuan3D-2` / `SpatialLM` | 3D / 空间 |

它们的部署代码和文字模型几乎一样，只是输入多一个 `image` 字段。看懂第 3 章，这类模型照葫芦画瓢即可。

### 5.2 o1-like 推理链（让模型“先想再答”）

仓库部分模型（如 Qwen2.5 的 06 文件）教实现“思考-回答”两阶段：先让模型输出一段推理过程，再给最终答案。**数学/代码题准确率显著更高**。这是当前大模型热点方向，理解它对你面试 AI 岗位很有用。

## 第 6 章不同硬件平台（选对货架）

同一套主线，换硬件只看“仓库里对应的那个文件夹”：

| 你的硬件 | 看哪里 | 注意 |
| --- | --- | --- |
| NVIDIA 显卡（最常见） | `models/` | 主线默认这一套 |
| AMD 显卡 | `models_amd/` | 用 ROCm，工具换 LM Studio / Ollama / llama.cpp |
| 华为昇腾 NPU | `models_ascend/` | 用 MindIE / vLLM-ascend，需看通用环境指南 |
| 苹果 M 芯片 | `models_mlx/` | 用 Apple MLX 框架，笔记本本地跑 |

## 第 7 章实战项目案例（把能力串成产品）

`examples/` 是“学完主线后看别人怎么落地”的范本：

| 项目 | 它教你 |
| --- | --- |
| **Chat-嬛嬛** | 用《甄嬛传》台词做 LoRA 微调，得到甄嬛语气的聊天模型（最经典的微调 demo） |
| **Tianji-天机** | 人情世故社交场景：提示词 + 智能体 + 数据 + 微调 + RAG 全流程 |
| **AMChat** | 基于 InternLM2-Math 做高等数学答疑（垂直领域 LoRA） |
| **数字生命** | 用特制数据集微调出“像你自己”的 AI，亮点是**数据集制作** |

## 第 8 章学习路线与排坑 FAQ

### 8.1 三个月路线图（给转型 AI 岗位的你）

1. **第 1 周**：通读本书，租 AutoDL 机器，跑通 Qwen2.5 的 ①→③ 部署。
2. **第 2~3 周**：做一遍甄嬛 LoRA 微调（05/07 文件），理解数据→训练→合并全流程。
3. **第 4~6 周**：用 LangChain + 一个模型搭个“个人知识库助手”（对应方法⑤）。
4. **第 2 月**：挑一个多模态模型（Qwen2-VL）跑通图文，理解 MLLM。
5. **第 3 月**：读 `examples/`，做一个完整小项目写进简历（这正是你 job-hunter 作品集的素材）。

### 8.2 新手高频坑

| 现象 | 原因 / 解法 |
| --- | --- |
| CUDA out of memory | 显存爆了 → 开 4bit 量化、换小模型、或用 QLoRA |
| 下载模型极慢/中断 | 没换源 / 没用镜像 / 没加 `--resume-download` |
| 网页打不开 | 云机器端口没映射（看 `02-AutoDL开放端口.md`） |
| 导入包报错 | 装错环境 → 确认 `conda activate` 了、版本匹配 |
| 微调后“学傻了” | 学习率太大 / 数据太少 → 调小 lr、补数据 |

🗺️ 最后再送你一张“读任意模型文件”的通用公式

拿到 `models/任意模型/` 文件夹，按这个顺序读就不会晕：

先 readme 看模型简介 → 找 0x 环境 装依赖 → 跑 01 基础调用 → 挑 02~04 你要的部署方式 → 做 05/07 微调。所有模型都是这个骨架，差异只在“模型名和个别参数”。

## 附录术语表（随查随用）

| 术语 | 一句话 |
| --- | --- |
| LLM | 大语言模型，吃文字吐文字的 AI |
| MLLM | 多模态大模型，还能吃图/音/视频 |
| Conda | Python 环境管理器，给每个项目单独备“锅” |
| Transformer | 当下大模型的基础架构库（HuggingFace 出品） |
| Tokenizer | 分词器，文字↔数字的中介 |
| CUDA | NVIDIA 显卡的并行计算平台 |
| 量化 (Quantization) | 压缩模型精度省显存（FP16→INT4） |
| LoRA / QLoRA | 高效微调法，只训一小部分参数 |
| vLLM | 高性能推理框架，吞吐远超原生 |
| LangChain | 把模型+数据+工具串成应用的框架 |
| RAG | 检索增强：先搜资料再让模型答，防胡说 |
| FastAPI | 用 Python 写网页接口的工具 |
| Gradio | 几行代码生成网页 Demo 界面 |
| SwanLab | 国产训练可视化工具（看 loss 曲线） |
| ModelScope | 阿里模型托管平台，国内下模型稳 |

本教科书由 self-llm 仓库（datawhalechina/self-llm，《开源大模型食用指南》）重构而来，所有方法、文件路径与工具均对应原仓库真实结构。原仓库持续更新，模型列表与具体参数以仓库最新版为准。建议配合原仓库 `models/` 目录边读边练。
