---
layout: post
title: "SHELL脚本递归访问目录，文件汇总，最简单的处理空格方式"
date: 2022-05-21 18:07:27 +0800
categories: Shell
---

### 空格处理
搜索了很多方法处理文件名带空格的处理方式，都感觉太复杂，而且比较笨试不出有用的效果，最后还是直接使用符号替换最有用,改完需要记得改回来
```
SAVEIFS=$IFS
IFS=$(echo -en "\n\b")
IFS=$SAVEIFS
```
### shell遍历脚本如下
```
#!/bin/bash

function recursive_copy_file()
{
    # 判断dest_dir是否存在，不存在就新建
    # if [ ! -d $2 ]; then
    #     mkdir -p $2
    # fi
    dirlist=$(ls $1)
    for name in ${dirlist[*]}
    do
        # echo $2
        if [ -f $1/$name ]; then
            file_name=$1/$name
            # 使用目录做为文件名
            new_name=${file_name//\//-}
            echo $new_name
            # 如果是文件，并且$2不存在该文件，则直接copy
            if [ ! -f $2/$new_name ]; then
                cp $1/$name $2/$new_name
            fi
        elif [ -d $1/$name ]; then
            # 如果是目录，并且$2不存在该目录，则先创建目录,将文件按原结构拷贝
            # if [ ! -d $2/$name ]; then
            #     mkdir -p $2/$name
            # fi
            # recursive_copy_file $1/$name $2/$name

            # 递归拷贝，文件汇总到同一文件夹
            recursive_copy_file $1/$name $2
        fi
    done
}

source_dir=/Users/xxx/MY\ Files
dest_dir=/Users/xxx/Files\ Collections

SAVEIFS=$IFS
IFS=$(echo -en "\n\b")
recursive_copy_file $source_dir $dest_dir
IFS=$SAVEIFS
```