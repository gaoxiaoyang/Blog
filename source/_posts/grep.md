---
title: Linux cmd
date: 2020-07-09 16:44:49
tags: 
- grep
- linux
- nohup

---

- `grep` 搜索`test`, 只显示前后几个字符

```shell
> echo "111_test_str" | grep -o -P '.{0,2}test.{0,4}'
> 1_test_str
```

- `nohup` 后台启动

```shell
> nohup sh test.sh param1 param2 >> nohup.out 2>&1 &
```

- 循环读取文件每一行，并执行指令

```bash
  cat vins.txt | while read i;do echo -n "hget XNY.CARINFO "$i | redis-cli -n 1 -h 10.20.170.5 | ./jq .vin,.uuid ; done
```

  