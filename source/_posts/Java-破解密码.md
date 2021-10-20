---
title: Java 破解密码
date: 2020-05-01 14:12:36
tags:
- Java
---

## Introduction

[一个简单的练习Java多线程的题目。](https://how2j.cn/k/thread/thread-methods/354.html?p=146743#step2605)

<!--more-->

## 问题

1. 生成一个长度是3的随机字符串，把这个字符串当作 密码

2. 创建一个破解线程，使用穷举法，匹配这个密码

3. 创建一个日志线程，打印都用过哪些字符串去匹配，这个日志线程设计为守护线程

提示： 破解线程把穷举法生成的可能密码放在一个容器中，日志线程不断的从这个容器中拿出可能密码，并打印出来。 如果发现容器是空的，就休息1秒，如果发现不是空的，就不停的取出，并打印。

## 代码

```java

package forlearning;

import java.util.*;

public class Main {

    private static String password = getRandomPassword(3);
    private static List<String> passwordList = new LinkedList<String>();
    private static boolean isDone = false;
    public static void main(String[] args) {

        Thread crackThread = new Thread() {
            @Override
            public void run() {
                super.run();
                char[] chs = new char[3];
                LOOP:
                for (int i = 0; i < 26; ++i) {
                    for (int j = 0; j < 26; ++j) {
                        for (int k = 0; k < 26; ++k) {
                            chs[0] = (char)(i + 'a');
                            chs[1] = (char)(j + 'a');
                            chs[2] = (char)(k + 'a');

                            String pwd = new String(chs);
                            passwordList.add(pwd);
                            if (pwd.equals(password)) {
                                System.out.println("正确密码是：" + pwd);
                                isDone = true;
                                break LOOP;
                            }
                        }
                    }
                }
            }
        };

        Thread logThread = new Thread() {
            @Override
            public void run() {
                super.run();
                int count = 0;
                while (!isDone) {
                    while (!passwordList.isEmpty()) {
                        String pwd = passwordList.remove(0);
                        System.out.printf("Password#%d: %s\n", count++, pwd);
                    }
                    try {
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }
        };

//        logThread.setDaemon(true);
        crackThread.start();
        logThread.start();
    }

    public static String getRandomPassword(int length) {
        String alphabet = "abcdefghijklmnopqrstuvwxyz";
        Random random = new Random();
        StringBuilder tmp = new StringBuilder();
        for (int i = 0; i < length; ++i) {
            int num = random.nextInt(26);
            tmp.append(alphabet.charAt(num));
        }
        return tmp.toString();
    }
}
```
