了解generator的同学应该知道，generator函数的执行需要调用迭代器iterator.next()，这个iterator就是generator函数执行返回的对象，那么generator如果用在工作中去处理异步的话，那么我们需要让generator函数自执行，去处理函数中的异步流程，类似于redux-saga中的sagaMiddleware.run(saga)，当然sagaMiddleware.run()处理的事情肯定不止如此😄。

run函数的使用
```
run(function* () {
  const value1 = yield 1;
  console.log(value1);
  const value2 = yield value1 + 2;
  console.log(value2);
})
```

run代码的简单实现
```
const run = (generator) => {
  let iterator = generator();
  let result = iterator.next();
  const step = () => {
    if (!result.done) {
      result = iterator.next(result.value);
      step();
    }
  }
  step();
}
```
