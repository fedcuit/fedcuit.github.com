---
layout: post
title: "Python中的时间与日期"
date: 2012-09-24 21:17
comments: true
categories: date time python 
---

* `dateutil.parser.parse` 将一个日期字符串转换为带时区信息的日期对象
```python
from dateutil.parser import parse

date = parse("08.10.2012" + " 00:00:00+0000", dayfirst=True)

>>>date
>>>datetime.datetime(2012, 10, 8, 0, 0, tzinfo=tzutc())

date = parse("08.10.2012" + " 00:00:00+0000", dayfirst=False)

>>>date
>>>datetime.datetime(2012, 8, 10, 0, 0, tzinfo=tzutc())
```
*如果使用的python版本是2.x, 需要使用的python-dateutil的版本是1.x, python-dateutil 2.x版本需要python3.x支持*


* `datetime.timedelta` 在日期对象上添加偏移量
```python
from datetime import timedelta

>>>date
>>>datetime.datetime(2012, 8, 10, 0, 0, tzinfo=tzutc())

date = date + timedelta(days=1)

>>>date
>>>datetime.datetime(2012, 8, 11, 0, 0, tzinfo=tzutc())

date = date + timedelta(hours=1)

>>>date
>>>datetime.datetime(2012, 8, 11, 1, 0, tzinfo=tzutc())
```

* `datetime.datetime.strptime` 将日期字符串转换为日期对象
```python
from datetime import datetime

date = datetime.strptime('2012.08.10', '%Y.%m.%d')

>>>date
>>>datetime.datetime(2012, 8, 10, 0, 0)
```
