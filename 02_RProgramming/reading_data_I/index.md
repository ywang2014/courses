---
title       : Reading and Writing Data Part I
subtitle    : 
author      : Roger D. Peng, Associate Professor of Biostatistics
job         : Johns Hopkins Bloomberg School of Public Health
logo        : bloomberg_shield.png
framework   : io2012        # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : tomorrow      # 
url:
  lib: ../../libraries
  assets: ../../assets
widgets     : [mathjax]            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
---

## Reading Data #读取数据

There are a few principal functions reading data into R. 
- `read.table`, `read.csv`, for reading tabular data #读取表格数据
- `readLines`, for reading lines of a text file #逐行读取文本数据
- `source`, for reading in R code files (`inverse` of `dump`) #读取R代码文件
- `dget`, for reading in R code files (`inverse` of `dput`) #读取被逆语法分析的R代码文件
- `load`, for reading in saved workspaces
- `unserialize`, for reading single R objects in binary form #读取二进制文件

---

## Writing Data #存储数据

There are analogous functions for writing data to files
- write.table
- writeLines
- dump
- dput
- save
- serialize

---

## Reading Data Files with read.table

The `read.table` function is one of the most commonly used functions for reading data. It has a few important arguments: #参数
- `file`, the name of a file, or a connection #文件名、关系名
- `header`, logical indicating if the file has a header line #表明文件第一行是否是表头行逻辑标识符 ：标签非数据
- `sep`, a string indicating how the columns are separated #分隔符：各列是如何分开的
- `colClasses`, a character vector indicating the class of each column in the dataset 
- #字符向量：长度等于数据集列数，字符向量指明数据集的每一列的数据类型
- `nrows`, the number of rows in the dataset #数据集行数：指定行数
- `comment.char`, a character string indicating the comment character #注释标记符：一般#，可指定其他符号
- `skip`, the number of lines to skip from the beginning #跳到指定行数
- `stringsAsFactors`, should character variables be coded as factors? #默认为T，是否把字符变量编码为因子

---

## read.table

For small to moderately sized datasets, you can usually call read.table without specifying any other arguments

```r
data <- read.table("foo.txt")
```

R will automatically #自动执行
- skip lines that begin with a #  #略过注释符号开头的行
- figure out how many rows there are (and how much memory needs to be allocated) #计算出行数
- figure what type of variable is in each column of the table #判断每一列的数据类型
Telling R all these things directly makes R run faster and more efficiently. #自己设置参数，更加高效执行
- `read.csv` is identical to read.table except that the default separator is a comma. #默认分隔符“，” read.table“空格”

---

## Reading in Larger Datasets with read.table

With much larger datasets, doing the following things will make your life easier and will prevent R from choking.

- Read the help page for read.table, which contains many hints
- Make a rough calculation of the memory required to store your dataset. If the dataset is larger than the amount of RAM on your computer, you can probably stop right here.
- Set `comment.char = ""` if there are no commented lines in your file. #没有注释行，设置为空

---

## Reading in Larger Datasets with read.table

- Use the `colClasses` argument. Specifying this option instead of using the default can make ’read.table’ run MUCH faster, often twice as fast. In order to use this option, you have to know the class of each column in your data frame. If all of the columns are “numeric”, for example, then you can just set `colClasses = "numeric"`. A quick an dirty way to figure out the classes of each column is the following:
- #相同的类型，可以直接设置： `colClasses = "numeric"`

```r      
initial <- read.table("datatable.txt", nrows = 100) #指定行数
classes <- sapply(initial, class)                   #sapply遍历一遍，调用class()
tabAll <- read.table("datatable.txt",
                     colClasses = classes)          #告诉R每一列的数据类型
```

- Set `nrows`. This doesn’t make R run faster but it helps with memory usage. A mild overestimate is okay. You can use the Unix tool `wc` to calculate the number of lines in a file.

---

## Know Thy System

In general, when using R with larger datasets, it’s useful to know a few things about your system.

- How much memory is available?
- What other applications are in use?
- Are there other users logged into the same system? 
- What operating system?
- Is the OS 32 or 64 bit?

---

## Calculating Memory Requirements

I have a data frame with 1,500,000 rows and 120 columns, all of which are numeric data. Roughly, how much memory is required to store this data frame?


1,500,000 × 120 × 8 bytes/numeric 

  = 1440000000 bytes
  
  = 1440000000 / $2^{20}$ bytes/MB  
  
  = 1,373.29 MB
  
  = 1.34 GB                     
