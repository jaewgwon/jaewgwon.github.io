---
title: Node.js서버의 async.queue에 대하여
categories: [Server, Node.js]
tags: [server, nodejs, async, javascript]
---

최근 사내 다른 팀의 프로젝트를 돕고 있다.  
차량에서 발생하는 정보를 Node.js서버로 수신하는데,  
해당 수신서버에서 발생하는 여러 버그를 수정하는 일이다.  

Node.js를 본격적으로 다뤄본 적도 없으니 당연 공부를 해야했고
꽤 중요한 내용인 것 같아 기록해두고자 한다.

나는 안드로이드가 좋은데 첫번째 포스트는 Node.js다.  
일탈이다.   

# 안녕 Node.js  
비즈니스 로직 상 Router의 작성이 필요 없었으며 굳이 express를 사용할 필요도 없었나보다.
Node.js를 사용하여 Sensor gateway에서 송신되는 정보를 수신하는데, 간단한 흐름은 다음과 같다.

1. 차량 정보가 Node.js 서버로 송신
2. 수신된 정보는 async.waterfall로 처리
3. async.waterfall의 처리 과정을 async.queue에 전달(push)

Node.js에 대한 지식이 부족해서 async.queue의 처리가 동기적으로 이루어지는 줄 알았다.
그건 아니고 Node.js 공식 도큐먼트에 의하면 async.queue는 병렬적으로 처리된다. 좋다..

# async.queue
>**queue(worker, [concurrency])**  
>Creates a queue object with the specified concurrency.  
>Tasks added to the queue are **processed in parallel** (up to the concurrency limit).  
>If all workers are in progress, the task is queued until one becomes available.  
>Once a worker completes a task, that task's callback is called.  
>**Arguments**  
>worker(task, callback)
>- An asynchronous function for processing a queued task, which must call its callback(err) argument when finished, with an optional error as an argument. If you want to handle errors from an individual task, pass a callback to q.push().  
>concurrency  
>- An integer for determining how many worker functions should be run in parallel. **If omitted, the concurrency defaults to 1.** If the concurrency is 0, an error is thrown.

참조: [Github - async-js](https://github.com/async-js/async#queue)

```javascript
    const taskQueue = async.queue((task, cb) => {
      task.run(cb);
    }, 20);
```

concurrency 설정을 안하면 기본 1로 설정된다. 잊지 말자.
이렇게 queue를 만들었으면, 아래와 같이 task를 push해주면 된다.

```javascript
    ...{ //event occurs
        taskQueue.push(task, (error) => {
            console.log(error);
        });
    }
```

알아서 다 잘 처리해준다. 좋은녀석..

테스트 느낌으로 첫 포스팅을 작성해보았다.  
다음 프로젝트는 다시 모바일 개발이니까,  
미리 설계좀 생각해두면서 관련 내용을 포스팅하고자 한다.