---
title: 命令行快速打开idea工程
date: 2018-01-22 17:20:07
tags: tool
categories:
---

> 每次用 idea 打开 java 工程时，都要手动选择路径，特别麻烦。如果能在命令行中，`cd`到工程目录下，直接打开就好了。可惜 idea 本身并不提供这样的命令行工具，所以还是用脚本执行吧。

在目录`~/.myscripts`中新建脚本文件`openIdea.sh`，内容如下：

```shell
#!/bin/sh

# check for where the latest version of IDEA is installed
IDEA=`ls -1d /Applications/IntelliJ\ * | tail -n1`
wd=`pwd`

# were we given a directory?
if [ -d "$1" ]; then
#  echo "checking for things in the working dir given"
  wd=`ls -1d "$1" | head -n1`
fi

# were we given a file?
if [ -f "$1" ]; then
#  echo "opening '$1'"
  open -a "$IDEA" "$1"
else
    # let's check for stuff in our working directory.
    pushd $wd > /dev/null

    # does our working dir have an .idea directory?
    if [ -d ".idea" ]; then
#      echo "opening via the .idea dir"
      open -a "$IDEA" .

    # is there an IDEA project file?
    elif [ -f *.ipr ]; then
#      echo "opening via the project file"
      open -a "$IDEA" `ls -1d *.ipr | head -n1`

    # Is there a pom.xml?
    elif [ -f pom.xml ]; then
#      echo "importing from pom"
      open -a "$IDEA" "pom.xml"

    # can't do anything smart; just open IDEA
    else
#      echo 'cbf'
      open "$IDEA"
    fi

    popd > /dev/null
fi
```

命令行执行

```shell
chmod 777 ~/.myscripts/openIdea.sh
echo "alias idea='sh ~/.myscripts/openIdea.sh'" >> ~/.profile
source ~/.profile
```

# 使用方式

`cd`到工程目录下，命令行执行`idea`即可。或者命令行直接执行`idea XXX`，`XXX`是项目路径。

# ref

* <https://luyiisme.github.io/2017/04/12/idea-open-project/>
* <https://gist.github.com/chrisdarroch/7018927>
