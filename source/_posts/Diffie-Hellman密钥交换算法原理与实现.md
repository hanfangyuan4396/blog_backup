---
title: Diffie-Hellman密钥交换算法原理与实现
date: 2022-06-01 15:50:23
reward: true
tags: DH密钥交换
---


## 1. Diffie-Hellman密钥交换算法原理
<!-- more -->
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201223172456738.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDM4NzMzOQ==,size_16,color_FFFFFF,t_70)


DH方法针对的是以下困难的局面：Alice和Bob 想共有一个密钥，用于对称加密。但是他们之间的通信渠道是不安全的。所有经过此渠道的信息均会被敌对方Eve看到。为了防止密钥泄露，Diffie与Hellman提出以下密钥交换协议:
1. Alice和Bob先对p和g达成一致，而且公开出来。Eve也就知道它们的值了。
2. Alice取一个私密的整数a，不让任何人知道，发给Bob 计算结果：A=g^a^ mod p. Eve 也看到了A的值。
3. 类似,Bob 取一私密的整数b,发给Alice计算结果B=g^b^ mod p.同样Eve也会看见传递的B是什么。
4. Alice 计算出K=B^a^ mod p=(g^b^)^a^ mod p=g^ab^ mod p.
5. Bob 也能计算出K=A^b^ mod p=(g^a^)^b^ mod p=g^ab^ mod p.
6. Alice 和 Bob 现在就拥有了一个共用的密钥K.
7. 虽然Eve看见了p,g, A and B, 但是鉴于计算离散对数的困难性，他无法知道a和b 的具体值，所以Eve就无从知晓密钥K是什么了。
## 2. cpp实现
```cpp
#include <iostream>
#include <cmath>
#include <stdlib.h>
#include <time.h>
#include <unistd.h>

// 判断输入的p是否是质数
bool isPrime(int p)
{
  if (p <= 1)
  {
    return false;
  }
  for (int i=2; i<=std::sqrt(p); i++)
  {
    if (p % i == 0)
    {
      return false;
    }
  }
  return true;
}


void inputPrime(int &p)
{
    // 判断是否是质数得标志
  bool is_prime = false;
  while (!is_prime)
  {
    // C++对大整数的运算不太友好，稍微大一点的质数在求本原元的时候一乘方就溢出，现在只能用小的质数做密钥交换实验
    std::cout << "Please input a prime(3~17)." << std::endl;
    std::cin >> p;
    is_prime = isPrime(p);
    if(is_prime)
    {
      std::cout << p << " is a prime." << std::endl;
    }
    else
    {
      std::cout << "Is your math taught by your P.E. teacher. " << p << " is not a prime." << std::endl;
    }
  }
  std::cout << "**************************************************" << std::endl;
}

// 求质数p的最大本原元
int getMaxGenerator(int p)
{
  // 最大本原元
  int g;
  // 指数
  int d;
  // 对于使g^d % p = 1成立的最小正整数d，如果d=φ(p)=p-1，则g为p的本原元  
  for (g=p-1; g>2; g--)
  {
    for (d=1; d<p; d++)
    {
      // C++对大整数的运算不太友好，稍微大一点的质数在求本原元的时候一乘方就溢出，现在只能用小的质数做密钥交换实验
      if ((unsigned long long)(std::pow(g, d)) % p == 1)
      {
       break; 
      }
    }
    if (d == p - 1)
    {
      return g;
    }
  }
}

// 生成[1, p-2]集合内的随机整数
int getRandom(int p)
{
  // 时间作为随机数种子
  srand(unsigned(time(0)));
  // 睡会儿，防止alice与bob的时间随机数种子相同
  sleep(1);
  // 生成[a, b]的随机整数公式 (rand() % (b-a+1)) + a
  return (rand() % (p-2 - 1 + 1)) + 1;
}

// 用质数、最大本原元以及随机数计算一个数， g^random_number % p
int getCalculation(int p, int g, int random_number)
{
  return (unsigned long long)(std::pow(g, random_number)) % p;
}

// 计算密钥. calculation_number^random_number % p
int getKey(int p, int random_number, int calculation_number)
{
  return (unsigned long long)(std::pow(calculation_number, random_number)) % p;
}

int main(int argc, char** argv)
{
  // 大质数、大质数本原元, alice产生的随机数，bob产生的随机数，alice计算的数，bob计算的数，alice计算的密钥，bob计算的密钥
  int p, g, alice_random, bob_random, alice_calculation, bob_calculation, alice_key, bob_key;

  inputPrime(p);

  std::cout << "getMaxGenerator g" << std::endl;
  g = getMaxGenerator(p);
  std::cout << "g = " << g << std::endl;
  std::cout << "**************************************************" << std::endl;

  std::cout << "Alice Bob get random" << std::endl;
  alice_random = getRandom(p);
  bob_random = getRandom(p);
  std::cout << "alice_random = " << alice_random << std::endl << "bob_random = " << bob_random << std::endl;
  std::cout << "**************************************************" << std::endl;

  std::cout << "Alice Bob get calculation" << std::endl;
  alice_calculation = getCalculation(p, g, alice_random);
  bob_calculation = getCalculation(p, g, bob_random);
  std::cout << "alice_calculation = " << alice_calculation << std::endl << "bob_calculation = " << bob_calculation << std::endl;
  std::cout << "**************************************************" << std::endl;
  
  std::cout << "Alice Bob get key" << std::endl;
  alice_key = getKey(p, alice_random, bob_calculation);
  bob_key = getKey(p, bob_random, alice_calculation);
  std::cout << "alice_key = " << alice_key << std::endl << "bob_key = " << bob_key << std::endl;
  std::cout << "**************************************************" << std::endl;
  
  std::cout << "Key exchange done!" << std::endl;

  return 0;
}
```
## 3. 程序运行结果

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201223174634522.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDM4NzMzOQ==,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201223174657956.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDM4NzMzOQ==,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201223174702689.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDM4NzMzOQ==,size_16,color_FFFFFF,t_70#pic_center)





参考文章：
[本原元](https://blog.csdn.net/ssff1/article/details/5001336?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-2.control&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-2.control)
[欧拉定理](https://zhuanlan.zhihu.com/p/97772441)
