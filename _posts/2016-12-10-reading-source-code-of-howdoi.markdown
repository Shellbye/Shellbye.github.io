---
author: shellbye
comments: true
date: 2016-12-10 10:27:08+00:00
layout: post
slug: reading-source-code-of-howdoi
title: 阅读源代码之开篇`Howdoi`
categories:
- tech_world
tags:
- Python
---

# 引言  
为了提高自己的可读性和美观程度，我在网站找了一些相关的文章开始学习起来，
读完[这篇]代码风格的文章之后，我觉得我要学习一些Pythonic的开源项目，
读源码这个事情一直想要开始，却一直没有正儿八经开始，只有在遇到重大bug
时，才偶尔（不得不）读一部分。现在，为了使得自己的代码能力能够上一个台阶，
我决定是时候开始大规模的读源码了。^_^


根据[Reading Great Code]的推荐，我准备开始阅读学习[howdoi]，
好吧，其实是发现它只有一个文件，而且才区区270行，所以还是从软柿子开始捏吧。

首先，howdoi是这样一个工具，它让你在命令行界面下完成一些编程问题简单的搜索，
比如官方举例的格式化日期以及tar的参数查询等。

# 安装
安装简单到令人发指，为了稍微增加点复杂度，我建议还是使用`virturalenv`
`virtualenv ~/.virtualenvs/howdoi`  
`source ~/.virtualenvs/howdoi/bin/activate`  
`pip install howdoi`  

# 使用
我简单尝试了一下，很不幸，这个工具“原生”的不支持中国大陆的用户
(国内用户可以尝试使用我修改的基于Bing.com的[版本])，
因为它内置的使用了Google去搜索，如下[SEARCH_URL]的设置：

{% highlight python %}

if os.getenv('HOWDOI_DISABLE_SSL'):  # Set http instead of https
    SEARCH_URL = 'http://www.google.com/search?q=site:{0}%20{1}'
    VERIFY_SSL_CERTIFICATE = False
else:
    SEARCH_URL = 'https://www.google.com/search?q=site:{0}%20{1}'
    VERIFY_SSL_CERTIFICATE = True

{% endhighlight %}

不过没关系，相信绝大多数程序员都会[科学上网]，只是一般的科学上网都是浏览器，
怎么让Terminal也变科学呢？感兴趣的用户可以[戳这里]获得科学知识。

一切就绪，那么让我们试试它的效果如何：

{% highlight bash %}

$ proxychains4 howdoi find file
grep -rnw '/path/to/somewhere/' -e "pattern"

{% endhighlight %}

哇，好厉害，怎么做到的呢？下面我们一一来分析一下。

# 分析

### 1. 无参数运行
在没有任何参数的情况下单独运行`howdoi`会输出help信息，
{% highlight bash %}

$ proxychains4 howdoi
usage: howdoi [-h] [-p POS] [-a] [-l] [-c] [-n NUM_ANSWERS] [-C] [-v]
              [QUERY [QUERY ...]]

instant coding answers via the command line

positional arguments:
  QUERY                 the question to answer

optional arguments:
  -h, --help            show this help message and exit
  -p POS, --pos POS     select answer in specified position (default: 1)
  -a, --all             display the full text of the answer
  -l, --link            display only the answer link
  -c, --color           enable colorized output
  -n NUM_ANSWERS, --num-answers NUM_ANSWERS
                        number of answers to return
  -C, --clear-cache     clear the cache
  -v, --version         displays the current version of howdoi

{% endhighlight %}

像大多数程序一样，没有参数运行`howdoi`会输出帮助信息，其过程是首先程序入口在[最下面]：

{% highlight python %}

if __name__ == '__main__':
    command_line_runner()

{% endhighlight %}

然后在`command_line_runner()`中，首先通过`get_parser`获取参数相关的信息，
其中的[argparse]是Python标准库中用来处理命令行参数的一个包，
由于我们此时的输入没有查询参数（query），所以
程序进入`if not args['query']`中并打印了帮助信息，如下所示：

{% highlight python %}

if not args['query']:
    parser.print_help()
    return

{% endhighlight %}

### 2. 基本搜索
下面我们尝试一下基本的搜索，输入的参数即示例中的`format date bash`,
这一次，执行越过了刚才的参数为空的检查，进入了下面的缓存检查，
由于我们没有禁止缓存，所以就进入了`_enable_cache`:

{% highlight python %}

def _enable_cache():
    if not os.path.exists(CACHE_DIR):
        os.makedirs(CACHE_DIR)
    requests_cache.install_cache(CACHE_FILE)

{% endhighlight %}

在`_enable_cache`中，通过使用[requests-cache]为相同的请求做了缓存，
可以加速相似的请求的返回。

最后程序进入`howdoi`，准备开始真正的工作。在`howdoi`中，
首先把查询参数拼接了起来，然后进入`_get_instructions`中，
在`_get_instructions`中，又进入到`_get_links`去获取答案的连接，
其中`_get_result`发起网络请求，获取返回结果；
然后用[pyquery]对得到的网页进行解析，从其中提取出Google搜索结果列表页的网页连接，
然后将结果返回到`_get_instructions`中的links，
如果Google的搜索结果中没有任何网页，那么`_get_instructions`将会返回False，
此时就会在命令行打印`Sorry, couldn't find any help with that topic`，
如[216]所示：

{% highlight python %}

def howdoi(args):
    args['query'] = ' '.join(args['query']).replace('?', '')
    try:
        return _get_instructions(args) or 'Sorry, couldn\'t find any help with that topic\n'
    except (ConnectionError, SSLError):
        return 'Failed to establish network connection\n'

{% endhighlight %}

当然，在正常的情况下，Google还是会返回一些结果的，程序开始收集答案。
在确定了用户通过参数传入的需要的答案的数量（`args['num_answers']`）
以及解析起始答案的位置之后，
程序进入`_get_answer`中去寻找答案，并将答案按照固定格式打印到Terminal中：

{% highlight python %}

answers = []
append_header = args['num_answers'] > 1
initial_position = args['pos']
for answer_number in range(args['num_answers']):
    current_position = answer_number + initial_position
    args['pos'] = current_position
    answer = _get_answer(args, links)
    if not answer:
        continue
    if append_header:
        answer = ANSWER_HEADER.format(current_position, answer)
    answer += '\n'
    answers.append(answer)
return '\n'.join(answers)

{% endhighlight %}

在`_get_answer`中，通过`_get_questions`过滤了连接，
只保留了那些[stackoverflow.com]中的问题，接着获取用户指定的位置的问题的连接，
如果用户只是需要连接本身，就只返回连接，否则就去抓取连接对应的网络中的内容。

{% highlight python %}

if args.get('link'):
    return link
page = _get_result(link + '?answertab=votes')
html = pq(page)

{% endhighlight %}

在解析问题页面时，首先当然是要处理排在第一位的回答，
其中的`instructions`表示是否包含代码片段，
`args['tags']`则表示该问题所标记的标签，
紧接着，如果没有找到代码，并且用户没有输入`all`标记参数，
则返回第一个答案的所有文本信息，如果用户标注了`all`，
则依次处理第一个答案下面的所有文本，遇到普通文本直接打印，
遇到代码，则使用`_format_output`格式化后打印。

{% highlight python %}

first_answer = html('.answer').eq(0)
instructions = first_answer.find('pre') or first_answer.find('code')
args['tags'] = [t.text for t in html('.post-tag')]

if not instructions and not args['all']:
    text = first_answer.find('.post-text').eq(0).text()
elif args['all']:
    texts = []
    for html_tag in first_answer.items('.post-text > *'):
        current_text = html_tag.text()
        if current_text:
            if html_tag[0].tag in ['pre', 'code']:
                texts.append(_format_output(current_text, args))
            else:
                texts.append(current_text)
    texts.append('\n---\nAnswer from {0}'.format(link))
    text = '\n'.join(texts)
else:
    text = _format_output(instructions.eq(0).text(), args)
if text is None:
    text = NO_ANSWER_MSG
text = text.strip()
return text

{% endhighlight %}

就这样，一个标准的查询流程就完成了。


[这篇]:http://docs.python-guide.org/en/latest/writing/style/
[Reading Great Code]:http://docs.python-guide.org/en/latest/writing/reading/
[howdoi]:https://github.com/gleitz/howdoi
[SEARCH_URL]:https://github.com/gleitz/howdoi/blob/master/howdoi/howdoi.py#L47-L52
[科学上网]:https://github.com/shadowsocks
[戳这里]:https://github.com/shadowsocks/shadowsocks/wiki/Using-Shadowsocks-with-Command-Line-Tools
[最下面]:https://github.com/gleitz/howdoi/blob/master/howdoi/howdoi.py#L270
[argparse]:https://docs.python.org/2/howto/argparse.html
[requests-cache]:https://pypi.python.org/pypi/requests-cache
[pyquery]:https://pythonhosted.org/pyquery/
[216]:https://github.com/gleitz/howdoi/blob/master/howdoi/howdoi.py#L216
[stackoverflow.com]:http://stackoverflow.com/
[pdb]:https://pymotw.com/2/pdb/
[版本]:https://github.com/Shellbye/howdoi
