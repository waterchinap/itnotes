---
title: python中的并行编码
date: 2025-11-05
---

集中了解了python中的并行编码，并尝试了使用多线程和进程，以及协程进行并行编码。

1. 多进程：用于cpu密集型任务。
2. 多线程：用于IO密集型任务。
3. 协程：用于IO密集型任务。

同时，多线程有时会被包装为协程，被统一调试。

## 三种并行的最佳实践

1. 使用多进程：
```python
from concurrent.futures import ProcessPoolExecutor, as_completed
import os

def cpu_bound_task(n):
    """一个需要大量CPU计算的任务"""
    # 模拟复杂计算
    return sum(i * i for i in range(n))

if __name__ == '__main__':
    data_points = [10**6] * 4  # 4个CPU密集型任务
    num_processes = os.cpu_count()
    
    # 模板：使用 ProcessPoolExecutor 上下文管理器
    with ProcessPoolExecutor(max_workers=num_processes) as executor:
        print(f"启动 {num_processes} 个进程处理 {len(data_points)} 个任务...")
        
        # 使用 submit + as_completed 模式，可以实时获取完成结果
        futures = [executor.submit(cpu_bound_task, data) for data in data_points]
        
        for future in as_completed(futures):
            try:
                result = future.result()
                print(f"一个任务完成，结果大致为: {result / 1e12:.2f} 万亿")
            except Exception as e:
                print(f"任务执行出错: {e}")
```

2. 使用多线程：
```python
from concurrent.futures import ThreadPoolExecutor
import requests
import time

def io_bound_task(url):
    """一个模拟网络I/O的线程任务"""
    try:
        response = requests.get(url, timeout=5)
        # 实际I/O发生在 requests.get() 调用中
        return f"URL: {url}, Status: {response.status_code}, Length: {len(response.content)}"
    except Exception as e:
        return f"URL: {url}, 错误: {e}"

# 示例数据
urls = [
    "https://www.google.com", 
    "https://www.baidu.com", 
    "https://www.github.com",
    "https://www.python.org"
] * 2

# 模板：使用 ThreadPoolExecutor
with ThreadPoolExecutor(max_workers=10) as executor: # I/O密集型任务可设置较多线程
    print(f"启动线程池处理 {len(urls)} 个URL...")
    
    # 使用 map 模式，简化代码，结果顺序与输入顺序一致
    for result in executor.map(io_bound_task, urls):
        print(result)
```
进程也可以使用上面的模式，只需要使用 ProcessPoolExecutor 上下文管理器。所以，进程和线程的管理模式基本相同。

3. 使用协程：
```python
import asyncio
import time

async def task(name, delay):
    print(f"Task {name} started, will sleep for {delay} seconds.")
    await asyncio.sleep(delay)
    print(f"Task {name} finished.")
    return f"Result from {name}"

async def main():
    start_time = time.time()
    print("Starting TaskGroup...")
    async with asyncio.TaskGroup() as tg:
        t1 = tg.create_task(task("A", 2))
        t2 = tg.create_task(task("B", 1))
        t3 = tg.create_task(task("C", 3))

    print("All tasks in TaskGroup have completed.")
    print(f"Result of t1: {t1.result()}")
    print(f"Result of t2: {t2.result()}")
    print(f"Result of t3: {t3.result()}")
    end_time = time.time()
    print(f"Total execution time: {end_time - start_time:.2f} seconds.")

asyncio.run(main())
```
相对来说，协程的实现要加一个包装函数，给asyncio.run()来使用，以启动协程。进程和线程虽然不一定要写包装函数，但一般也都是写一个。