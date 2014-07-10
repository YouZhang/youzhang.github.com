---
layout: post
title: "ST Work Flow & Tools"
description: ""
category: ALU
tags: [Flow,ST]
---
{% include JB/setup %}
# ST Work Flow & Tools

> **Teams in ST SD**

- eNodeB SW Development Team (FRA)
- ST Team (Test the eNodeB SW)

> **Some Platforms & Tools**

- Jenkins server (JS) -> build & release & load
- jenkins client (JC) -> Run AutoTest in time
- CI Report      (CI) -> Show ET Report by Date & Release & Platform

> **ST Work Flow**

- JS Build new SW and Release -> JS load new package -> JS run TestSuite after 17:00 (ET) -> Log Server -> CI Report Show the Result log 

> **Asgard**

```
Asgard is a leading automation framework for LTE ST team in Alcatel-Lucent Shanghai Bell. Asgard provide a whole set of functions to enable the automation test on your platform efficiently, including manage your platform, tc helper, batch online and daily CI run.
```

- http://172.24.186.218:8080/tchelper
    - read and show tc(test script with xml format) in web
    - 