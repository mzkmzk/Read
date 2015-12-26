# UI

在执行`javascript`时,大多数浏览器会停止把新UI任务放进队列中.

但当点击一个按钮,会先触发UI变化(假如有),然后再执行`javascript`,如果在`javascript`有改变UI的代码,会等`javascript`执行完了,再把UI任务放进队列.