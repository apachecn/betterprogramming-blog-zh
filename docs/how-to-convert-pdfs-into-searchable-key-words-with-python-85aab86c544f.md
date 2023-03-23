# 如何用 Python 从 pdf 中提取单词

> 原文：<https://betterprogramming.pub/how-to-convert-pdfs-into-searchable-key-words-with-python-85aab86c544f>

## 仅提取您需要的文本

![](img/d5ffeafe35baa4905b4b04e5c6c4aff2.png)

Raphael Schaller 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

正如我在[的上一篇文章](https://medium.com/better-programming/how-to-connect-to-google-sheets-with-python-83d0b96eeea6)中提到的，我一直在与一个客户合作，帮助他们解析数百个 PDF 文件，提取关键词，使它们变得可搜索。

解决问题的一部分是弄清楚如何从所有这些 PDF 文件中提取文本数据。你可能会惊讶地发现事情没那么简单。你看，pdf 是 Adobe 的专有格式，当涉及到从每个文件中自动提取信息的过程时，它有自己的小怪癖。

幸运的是，我们有适合这项工作的语言:Python。现在，我已经明确了我对 Python 的热爱。它很容易阅读，有一大堆很棒的库，可以让你做基本上任何事情。它是你多功能腰带上的完美工具。正如我之前提到的，它让你成为蝙蝠侠。

接下来是一个教程，教你如何解析一个 PDF 文件，并把它转换成一个关键字列表。

# 设置

对于本教程，我将使用 Python 3.6.3。你可以使用任何你喜欢的版本(只要它支持相关的库)。

要学习本教程，您需要以下 Python 库:

*   [PyPDF2](https://pypi.org/project/PyPDF2/) (将简单的基于文本的 PDF 文件转换成 Python 可读的文本)
*   [textract](https://textract.readthedocs.io/en/stable/) (将重要的扫描 PDF 文件转换成 Python 可读的文本)
*   [NLTK](https://www.nltk.org/) (清理短语并将其转换为关键字)

每个库都可以在终端(在 macOS 上)中使用以下命令安装:

```
pip install PyPDF2pip install textractpip install nltk
```

这将下载解析 PDF 文档和提取关键字所需的库。为此，请确保您的 PDF 文件存储在您编写脚本的文件夹中。

启动您最喜欢的编辑器并键入:

```
Note: All lines starting with # are comments.
```

## 步骤 1:导入所有库

```
import PyPDF2 
import textractfrom nltk.tokenize import word_tokenize
from nltk.corpus import stopwords
```

## 第二步:阅读 PDF 文件

```
#Write a for-loop to open many files (leave a comment if you'd like to learn how).filename = '*enter the name of the file here'* #open allows you to read the file.pdfFileObj = open(filename,'rb')#The pdfReader variable is a readable object that will be parsed.pdfReader = PyPDF2.PdfFileReader(pdfFileObj)#Discerning the number of pages will allow us to parse through all the pages.num_pages = pdfReader.numPages
count = 0
text = ""#The while loop will read each page.
while count < num_pages:
    pageObj = pdfReader.getPage(count)
    count +=1
    text += pageObj.extractText()#This if statement exists to check if the above library returned words. It's done because PyPDF2 cannot read scanned files.if text != "":
   text = text#If the above returns as False, we run the OCR library textract to #convert scanned/image based PDF files into text.else:
   text = textract.process(fileurl, method='tesseract', language='eng')#Now we have a text variable that contains all the text derived from our PDF file. Type print(text) to see what it contains. It likely contains a lot of spaces, possibly junk such as '\n,' etc.#Now, we will clean our text variable and return it as a list of keywords.
```

## 步骤 3:将文本转换成关键词

```
#The word_tokenize() function will break our text phrases into individual words.tokens = word_tokenize(text)#We'll create a new list that contains punctuation we wish to clean.
punctuations = ['(',')',';',':','[',']',',']#We initialize the stopwords variable, which is a list of words like "The," "I," "and," etc. that don't hold much value as keywords.stop_words = stopwords.words('english')#We create a list comprehension that only returns a list of words that are NOT IN stop_words and NOT IN punctuations.keywords = [word for word in tokens if not word in stop_words and not word in punctuations]
```

现在，您已经将文件的关键字存储为一个列表。你可以用它做任何你想做的事。如果您想让 PDF 可搜索或解析大量文件并进行聚类分析，请将其存储在电子表格中。你也可以用它来创建一个求职简历推荐系统。

我希望你觉得这篇教程很有价值！如果你有任何要求，想要一些澄清，或发现一个错误，请让我知道！