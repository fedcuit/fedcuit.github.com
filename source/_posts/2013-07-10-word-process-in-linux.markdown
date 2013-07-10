---
layout: post
title: Word process in Linux -- GREP
Date: 2013-07-10 23:46
comments: true
categories: shell grep linux
---

* grep by multi words
```bash
cat file.txt | grep "word1\|word2\|word3"
```

* grep line which doesn't contains word
```bash
cat file.txt | grep -v word
```

* show n lines above and under greped line
```bash
cat file.txt | grep -20 word
```
