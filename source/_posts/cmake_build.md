---
title: cmake build
date: 2019-05-28 11:20:49
tags: cmake
---

注意：不使用`CMAKE_BUILD_TYPE`， 默认为`Debug`

- `Release`

  ```bash
    mkdir Release
    cd Release
    cmake -DCMAKE_BUILD_TYPE=Release ..
    make
  ```

- `Debug`

  ```bash
    mkdir Debug
    cd Debug
    cmake -DCMAKE_BUILD_TYPE=Debug ..
    make
  ```

备注：`windows` 使用`CMAKE_BUILD_TYPE` 后续更新