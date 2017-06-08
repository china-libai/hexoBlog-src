---
title: Python argparse模块详解
date: 2017-06-08 10:27:24
tags: Python
---
>    argparse是python用于解析命令行参数和选项的标准模块，用于代替已经过时的optparse模块。
>    官方文档中讲到的，本文基本都提到了，但只是简要记录，如果需要深入理解，可查看原文。   
>    https://docs.python.org/3/library/argparse.html

## 使用步骤
```
import argparse                     # 导入模板

parser = argparse.ArgumentParser()  # 创建parser

parser.add_argument()               # 添加参数

args = parser.parse_args()          # 参数解析
```

## [ArgumentParser对象](https://docs.python.org/3/library/argparse.html#argumentparser-objects)
    class argparse.ArgumentParser(
        prog=None,                  # 设定程序名称 (defaul: sys.argv[0])
        usage=None,                 # 替换默认的Usage信息
        description=None,           # 程序简要信息说明(参数说明前)
        epilog=None,                # 附加信息说明(参数说明后)
        parents=[],                 # 继承父解析器(parser)
        formatter_class=argparse.HelpFormatter,     # 自定义帮忙信息显示格式(4种)
        prefix_chars='-',           # 参数前缀符号(默认为-,如：-h/--help)
        fromfile_prefix_chars=None, # 从文件中引用参数（与在命令行直接写作用一致，解决参数过多的情况）
        argument_default=None,      # 可设置argparse.SUPPRESS阻止默认参数默认值
        conflict_handler='error',   # 参数冲突处理
        add_help=True,              # 帮助信息中默认添加"-h, --help"描述
        allow_abbrev=True           # 允许参数缩写
    )

## [add_argument()方法](https://docs.python.org/3/library/argparse.html#the-add-argument-method)
    ArgumentParser.add_argument(
        name or flags...    # 选项的名称或列表,例如：foo/-f/--foo
        [, action]      # 采取的基本操作
                            store(默认)         存储参数值
                            store_const        使用该字符串选项时，取用const值
                            store_true         使用该字符串选项时，参数值置为True
                            store_false        使用该字符串选项时，参数值置为False
                            append             同一个命令行中多次使用该字符串选项时，以追加的方式将值添加到list中
                            append_const       将多个字符串选项的const值合并到一个list
                            count              统计选项出现的次数 （如："-vvv",则最终值为3）
                            help               parser默认会添加一个help action。(一般不用理会)
                            version            打印版本信息
                            也可以自定义action类
        [, nargs]       # 该参数值要求的数量
                            数值       指明参数个数
                            ?         提供了参数则取参数值；
                                        无参数但声明了选项字符串则取const值；
                                        无参数也未声明选择字符串则取default值
                            *         所有参数存入list
                            +         与*类似，但参数个数不能为空
                            argparse.REMAINDER  原封不动的记录参数到list中，通常用于将这些参数传递到其它的命令行工具。
        [, const]       # action/nargs部分要求的常值
                            1、当action="store_const"或者"append_const"时需要设置
                            2、当选项为(-f/--foo),nargs='?'，同时未提供具体参数时，取用该值。
        [, default]     # 参数默认值
        [, type]        # 参数类型（内建参数或者函数，也可是自定义函数）
        [, choices]     # 允许的参数值（白名单）,tuple/range
        [, required]    # 选项是否必须，设置为True表示选项必填。
        [, help]        # 参数说明,可以用其它类似 %(prog)s 格式调用prog值；可设置argparse.SUPPRESS使该选项在帮助信息中不可见。
        [, metavar]     # 定义参数在Usage信息中的名称
        [, dest]        # 解析后的属性名称
    )
- ### [自定义action](https://docs.python.org/3/library/argparse.html#action-classes)

    class argparse.**Action**(option_strings, dest, nargs=None, const=None, default=None, type=None, choices=None, required=False, help=None, metavar=None)

## [parse_args()方法](https://docs.python.org/3/library/argparse.html#the-parse-args-method)
    ArgumentParser.parse_args(args=None, namespace=None)
一般情况下，我们直接使用如下命令就可以了：
```python
# args=None, 程序将sys.argv作为参数代入
args = parse.parse_args()              

# 给args赋值，跳过sys.argv，主要用于测试工作，避免每次运行都输入冗长的参数。
args = parser.parse_args(['1', '2', '3', '4'])

# namespace=custom_class，将属性分配到一个已经存在的对象中。
parser.parse_args(args=['--foo', 'BAR'], namespace=custom_class99)
```

## [其它工具](https://docs.python.org/3/library/argparse.html#other-utilities)

- ### [子命令](https://docs.python.org/3/library/argparse.html#sub-commands)
    很多程序把它的功能分到几个子程序中，比如：pip install , pip download , pip uninstall 等. 通过这种方式，可以很方便处理不同程序的参数。

    ArgumentParser.**add_subparsers**([title][, description][, prog][, parser_class][, action][, option_string][, dest][, help][, metavar])

    ```python
    >>> parser = argparse.ArgumentParser()
    >>> subparsers = parser.add_subparsers(dest='subparser_name')
    >>> subparser1 = subparsers.add_parser('1')
    >>> subparser1.add_argument('-x')
    >>> subparser2 = subparsers.add_parser('2')
    >>> subparser2.add_argument('y')
    >>> parser.parse_args(['2', 'frobble'])
    Namespace(subparser_name='2', y='frobble')

    ```


- ### [文件类型对象](https://docs.python.org/3/library/argparse.html#filetype-objects)
    add_argument()中的FileType的参数"工厂"。

    class argparse.**FileType**(mode='r', bufsize=-1, encoding=None, errors=None)
    ```python
    >>> parser = argparse.ArgumentParser()
    >>> parser.add_argument('--raw', type=argparse.FileType('wb', 0))
    >>> parser.add_argument('out', type=argparse.FileType('w', encoding='UTF-8'))
    >>> parser.parse_args(['--raw', 'raw.dat', 'file.txt'])
    Namespace(out=<_io.TextIOWrapper name='file.txt' mode='w' encoding='UTF-8'>, raw=<_io.FileIO name='raw.dat' mode='wb'>)

    ```

- ### [参数分组](https://docs.python.org/3/library/argparse.html#argument-groups)
    在Usage信息中的参数分组，如pip -h可以看到"Commands","General Options"分组。

    ArgumentParser.**add_argument_group**(title=None, description=None)

    ```python
    >>> parser = argparse.ArgumentParser(prog='testPROG', add_help=False)
    >>> group1 = parser.add_argument_group('group1', 'group1 description')
    >>> group1.add_argument('foo', help='foo help')
    >>> group2 = parser.add_argument_group('group2', 'group2 description')
    >>> group2.add_argument('--bar', help='bar help')
    
    >>> parser.print_help()
    
    usage: testPROG [--bar BAR] foo

    group1:
    group1 description

    foo    foo help

    group2:
    group2 description

    --bar BAR  bar help
    ```

- ### [互斥](https://docs.python.org/3/library/argparse.html#mutual-exclusion)
    参数互斥！

    ArgumentParser.**add_mutually_exclusive_group**(required=False)

    ```python
    >>> parser = argparse.ArgumentParser(prog='PROG')
    >>> group = parser.add_mutually_exclusive_group(required=True)
    >>> group.add_argument('--foo', action='store_true')
    >>> group.add_argument('--bar', action='store_false')
    >>> parser.parse_args([])
    usage: PROG [-h] (--foo | --bar)
    PROG: error: one of the arguments --foo --bar is required
    ```

- ### [解析器默认配置](https://docs.python.org/3/library/argparse.html#parser-defaults)
    在解析器级别给参数设置默认值(优先级高于在add_argument方法中的设置)，也可以获取默认值。

    ArgumentParser.**set_defaults**(**kwargs)       # 设置默认值

    ArgumentParser.**get_default**(dest)            # 获取默认值

    ```python
    >>> parser = argparse.ArgumentParser()
    >>> parser.add_argument('foo', type=int)
    >>> parser.set_defaults(bar=42, baz='badger')   # 不审查是否在命令行中声明，故而bar，baz可以直接添加
    >>> parser.parse_args(['736'])
    Namespace(bar=42, baz='badger', foo=736)

    >>> parser = argparse.ArgumentParser()
    >>> parser.add_argument('--foo', default='bar') # 解析器级别默认值总是覆盖参数级别默认值
    >>> parser.set_defaults(foo='spam')
    >>> parser.parse_args([])
    Namespace(foo='spam')

    >>> parser = argparse.ArgumentParser()
    >>> parser.add_argument('--foo', default='badger')
    >>> parser.get_default('foo')                   # 获取默认值
    'badger'

    ```

- ### [打印帮忙](https://docs.python.org/3/library/argparse.html#printing-help)
    用于打印帮助信息。

    ArgumentParser.**print_usage**(file=None)

    ArgumentParser.**print_help**(file=None)

    ArgumentParser.**format_usage**()

    ArgumentParser.**format_help**()

- ### [部分解析](https://docs.python.org/3/library/argparse.html#partial-parsing)
    有些脚本只解析部分参数，放过其余的参数以便传递给其它脚本或程序。 这种情况下使用 parse_known_args() 。跟parse_args()用法一样，但当参数过多的情况下并不会报错，而是将多余的参数放到一个新的tuple中。

    ArgumentParser.**parse_known_args**(args=None, namespace=None)

    ```python
    >>> parser = argparse.ArgumentParser()
    >>> parser.add_argument('--foo', action='store_true')
    >>> parser.add_argument('bar')
    >>> parser.parse_known_args(['--foo', '--badger', 'BAR', 'spam'])
    (Namespace(bar='BAR', foo=True), ['--badger', 'spam'])     # ['--badger', 'spam']即为多余的参数。
    ```

- ### [自定义文件解析](https://docs.python.org/3/library/argparse.html#customizing-file-parsing)

- ### [退出方法](https://docs.python.org/3/library/argparse.html#exiting-methods)

- ### [optparse代码升级](https://docs.python.org/3/library/argparse.html#upgrading-optparse-code)
    原本argparse是与optparse保持兼容的，但是！@#￥%……&*（。升级办法如下：
1.    Replace all optparse.OptionParser.add_option() calls with ArgumentParser.add_argument() calls.
2.    Replace (options, args) = parser.parse_args() with args = parser.parse_args() and add additional ArgumentParser.add_argument() calls for the positional arguments. Keep in mind that what was previously called options, now in argparse context is called args.
3.    Replace callback actions and the callback_* keyword arguments with type or action arguments.
4.    Replace string names for type keyword arguments with the corresponding type objects (e.g. int, float, complex, etc).
5.    Replace optparse.Values with Namespace and optparse.OptionError and optparse.OptionValueError with ArgumentError.
6.    Replace strings with implicit arguments such as %default or %prog with the standard Python syntax to use dictionaries to format strings, that is, %(default)s and %(prog)s.
7.    Replace the OptionParser constructor version argument with a call to parser.add_argument('--version', action='version', version='<the version>').