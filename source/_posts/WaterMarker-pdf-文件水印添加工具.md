---
title: WaterMarker - pdf 文件水印添加工具
author: HWH
math: true
mermaid: true
tags:
  - Python
categories:
  - - Tools
  - - Tutorial
abbrlink: 36cef2e7
date: 2023-01-05 09:48:38
---

### 写在前面
- 昨天想给 pdf 添加个水印，结果 wps 居然需要收费，果断选择写一个 pdf 文件水印添加工具
- 本工具能够自适应的为 pdf 文件每页添加水印
- 本工具完全由 Python 编写，支持脚本运行和编译器内执行
- 同时支持如下自定义
  - 水印文字      (text)
  - 水印透明度    (alpha)
  - 水印字体      (font)
  - 水印字体大小   (size)
  - 水印旋转角度   (angle)


### 编写环境
- Python: 3.7.0
- pypdf2: 3.0.1
- reportlab: 3.6.12


### 核心代码分析

#### 创建水印函数

- 函数的签名如下

```python
def create_watermark(content, width, height, args)
```

- 函数需传入四个参数
  - content: 水印文字
  - width: pdf 当前页的宽
  - height: pdf 当前页的高
  - args: 命令行参数
- 为了自适应 pdf 每页，使得在不同大小的页面添加水印时均能够以合适的大小添加水印文字
- 代码中计算了当前页面与标准 A4 页面宽高的比例

```python
# 比例，用于自适应 pdf 页面大小
width = float(width) * 0.0352
height = float(height) * 0.0352
ratio_w = width / 21
ratio_h = height / 29.7
```

- 并在设置字体字号和绘制文本部分对相应数值进行修正

```python
# 设置字体，默认采用 SimSun 字体
c.setFont(args.font, args.size * (ratio_w + ratio_h) / 2)

# 画几个文本,注意坐标系旋转的影响
for i in range(5):
    for j in range(10):
        # 使用 比例 自适应水印文字位置
        a = 10 * (i - 1) * ratio_w
        b = 5 * (j - 2) * ratio_h
        c.drawString(a * cm, b * cm, content)
```

#### 添加水印函数

- 函数的签名如下

```python
def add_watermark(pdf_file_in, pdf_file_out, args)
```

- 函数需传入三个参数
  - pdf_file_in: 需添加水印文件的路径
  - pdf_file_out: 添加水印后文件的输出路径
  - args: 命令行参数

- 使用 pypdf2 库中的 `PdfWriter` 类创建 pdf 文件写出对象
- 根据 pdf_file_in 读取 pdf 文件，并使用 `PdfReader` 类创建 pdf 文件对象

```python
pdf_output = PdfWriter()
input_stream = open(pdf_file_in, 'rb')
pdf_input = PdfReader(input_stream, strict=False)
```

- 对每一页 pdf 分别添加水印
- 每次获取每页的实际宽高，与前一页对比，若不一致则重新根据当前页面生成水印文件

```python
# 给每一页打水印
for i in range(pageNum):
    page = pdf_input.pages[i]
    # 获取当前页面实际宽高
    width = pdf_input.pages[i].mediabox.width
    height = pdf_input.pages[i].mediabox.height
    if width != last_width or height != last_height:
        pdf_file_mark = create_watermark(watermark_text, width, height, args)  # 生成水印文件
        pdf_watermark = PdfReader(open(pdf_file_mark, 'rb'), strict=False)  # 读入水印pdf文件
        last_width = width
        last_height = height
    page.merge_page(pdf_watermark.pages[0])
    page.compress_content_streams()  # 压缩内容
    pdf_output.add_page(page)
pdf_output.write(open(pdf_file_out, 'wb'))
```


#### 主函数

- 为实现脚本方式执行，使用 argparse 库定义命令行解析器对象并添加命令行参数

```python
# 定义命令行解析器对象
parser = argparse.ArgumentParser(description='WaterMarker of argparse')

# 添加命令行参数
parser.add_argument('--text', default='watermark', help="Text to add watermark")
parser.add_argument('-F', '--file', default='', help="The path to the file to add the watermark to")
parser.add_argument('--font', default='SimSun', help="Font used for watermark text")
parser.add_argument('--size', type=int, default=30, help="Font size used for watermark text, defaults to 30, "
                                                         "the size will adjust itself as the page changes")
parser.add_argument('--alpha', type=float, default=0.1, help="Transparency of watermark text, between 0.0 and 1.0")
parser.add_argument('--angle', type=int, default=30, help="Rotate the canvas by the angle theta (in degrees)")
parser.add_argument('-O', '--output', default='', help="File output path after adding watermark (including the "
                                                       "file name), the default is the original file directory")

# 从命令行中结构化解析参数
args = parser.parse_args()
```

- 对命令行参数进行分析，以避免用户重复输入水印文字或者需添加水印的 pdf 文件

```python
# 检查命令行参数 text 的值是否为默认值，是则要求用户输入水印文字，若用户输入空，则采用命令行参数的默认值
# 若命令行参数的值不为默认值，则跳过用户输入水印文字
text = ''
if args.text == 'watermark':
    text = input("请输入水印文字：")
watermark_text = args.text if text == '' else text

# 处理方式与上类似
pdf_file_in = ''
if args.file == '':
    pdf_file_in = input("请输入文件路径：").strip('"').strip(' ')
else:
    pdf_file_in = args.file
```

- 同时对文件进行了限制，以确保传入文件为 pdf 格式

```python
if os.path.splitext(pdf_file_in)[-1] == ".pdf":
     pdf_file_in = pdf_file_in.replace('\\', '/')
     # 文件输出路径
     if args.output == '':
         pdf_file_out = pdf_file_in.replace('.pdf', '') + '（添加水印）.pdf'
     else:
         pdf_file_out = args.output.replace('\\', '/')
     add_watermark(pdf_file_in, pdf_file_out, args)
     print("添加水印成功")
     print("文件路径为: {}".format(pdf_file_out.replace('/', '\\')))
 else:
     print("输入文件不为pdf格式!!!")
```


### 使用案例
- 由于支持使用脚本运行，以下内容将重点介绍脚本执行方式

#### 脚本具体参数如下
``` bash
optional arguments:
  -h, --help            show this help message and exit
  --text TEXT           Text to add watermark
  -F FILE, --file FILE  The path to the file to add the watermark to
  --font FONT           Font used for watermark text
  --size SIZE           Font size used for watermark text, defaults to 30, the
                        size will adjust itself as the page changes
  --alpha ALPHA         Transparency of watermark text, between 0.0 and 1.0
  --angle ANGLE         Rotate the canvas by the angle theta (in degrees)
  -O OUTPUT, --output OUTPUT
                        File output path after adding watermark (including the
                        file name), the default is the original file directory
```

#### 具体案例
- 以下为具体使用案例，其中除 `-F` 和 `-O` 参数外均为默认值
- 若未使用 `--text` 或 `-F` 参数将会要求用户手动输入

```bash
python watermarker.py --text "WaterMarker" -F "C:\test.pdf" --font "SimSun" --alpha 0.1 --size 30 --angle 30 -O "./result.pdf"
```

### 写在最后
- 感谢大家阅读和使用，本工具源码已上传至 [github](https://github.com/HWH-2019/WaterMarker)
- 本工具采用 GPL2 开源协议，详细内容参见 [协议](https://github.com/HWH-2019/WaterMarker/blob/main/LICENSE)
- 若有更好的想法或者任何疑问，欢迎评论，或前往 github 提交 issue，或通过邮件 <huang_wen_huan@163.com> 联系我，我将及时更新和回复
