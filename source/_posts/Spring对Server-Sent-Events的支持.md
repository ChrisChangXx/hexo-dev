---
title: Spring对Server-Sent Events的支持
date: 2022-06-05 22:07:54
tags:
---

[Spring对Server-Sent-Events的支持](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-ann-async-sse)

## SSE

SseEmitter (a subclass of ResponseBodyEmitter) provides support for Server-Sent Events, where events sent from the
server are formatted according to the W3C SSE specification. To produce an SSE stream from a controller, return
SseEmitter, as the following example shows:

```
@GetMapping(path="/events", produces=MediaType.TEXT_EVENT_STREAM_VALUE)
public SseEmitter handle() {
    SseEmitter emitter = new SseEmitter();
    // Save the emitter somewhere..
    return emitter;
}

// In some other thread
emitter.send("Hello once");

// and again later on
emitter.send("Hello again");

// and done at some point
emitter.complete();
```

While SSE is the main option for streaming into browsers, note that Internet Explorer does not support Server-Sent
Events. Consider using Spring’s WebSocket messaging with SockJS fallback transports (including SSE) that target a wide
range of browsers.

## 前端支持

```vue
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>fetch-sse</title>
  <script src="js/vue3.2.13/dist/vue.global.js"></script>
</head>
<body>
<div id="sse-rendering">
  <p>Accept:{{message}}</p>
  <button v-on:click="fetchData">开始</button>
  <button v-on:click="closeFetch">结束</button>
</div>
</body>
<script>
  const SseRendering = {
    data() {
      return {
        message: null,
        eventSource: null
      }
    },
    methods: {
      fetchData() {
        console.log(window)
        let sse = this
        this.eventSource = new EventSource("http://localhost:8080/test-sse");
        this.eventSource.onmessage = function (e) {
          sse.message = e.data
          console.log(this)
          console.log(e.data)
        }
      },
      closeFetch() {
        if (this.eventSource != null) {
          this.eventSource.close()
        }
      }
    }
  }
  Vue.createApp(SseRendering).mount("#sse-rendering")
</script>
</html>
```