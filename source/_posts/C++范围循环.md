---
title: C++范围循环
date: 20200521 14:15:00
tags: [C++]
---

范围循环用来遍历容器里的对象, C++11及以后的特性

<!--more-->

- 格式

  ```c++
  for(typename object: container)
  {
      loop body
  }
  ```

  ```c++
  void ForEach(const std::vector<int>& values, void(*func)(int))
  {
      for (int value : values) //范围循环
      {
          func(value);
      }
  }
  void PrintValue(int value)
  {
      std::cout << value << std::endl;
  }
  
  int main()
  {
      std::vector<int> values = { 1,2,3,4,5 };
      ForEach(values, PrintValue);
  
      std::cin.get();
  }
  ```

  比较常用的格式

  ```c++
  for(auto a: ai)
      ;
  for(auto &a: ai)
      ;
  for(auto const &a : ai)
      ;
  ```