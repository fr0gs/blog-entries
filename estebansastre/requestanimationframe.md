# Understanding requestAnimationFrame

# Introduction 

I never quite got what was the *requestAnimationFrame* function I kept seeing while reading web articles since I never really dove deep in graphics in the browser. 

Still, I think it will be a good exercise and reminder for the future to write a small article to structure and understand the basic concept and usage of the function.

# Overview

For the human brain to be fooled into thinking that a sequence of images flow into movement (animate), you need to provide at least 24 images (frames) per second, otherwise you'll start to notice cuts. Most computer screens have an "image refresh" (or frames/per/second) rate of 60fps, meaning in turn that you will see 60 images in the course of one second.

If we divide the slice of time for perception (a second) into the refresh rate of the screen it gives us the time slot available to perform an animation (both calculating and painting) for a single frame. We use *miliseconds* since **setTimeout**, **setInterval** use them to measure time.

```sh
1000ms / 60 fps = 16.67ms/frame
```

The smoothness of an animation depends on whether each frame of the animation is executed within that *16.67ms* time frame.

An example animation [taken from here](https://hacks.mozilla.org/2011/08/animating-with-javascript-from-setinterval-to-requestanimationframe/) but slightly modified includes a green square being moved horizontally from left to right using the **setInterval** function ([Demo here](https://codepen.io/anon/pen/PaYJrB)).

```htm
<div id="animated"><div>
```
```css
#animated {
    position: absolute;
    width: 20px;
    height: 20px;
    background: green;
}
```
```javascript
let elem = document.getElementById("animated"),
  left = 0,
  timer;

function animate() {
  elem.style.left = ( left += 10 ) + "px";
  
  if ( left == 1000 ) {
    clearInterval(timer);
  }
}

timer = setInterval(function() {
  animate();
}, 17);
```

As seen, I've rounded the 16.67ms to 17ms, and you can see the continuity and somewhat fluidity of the animation. What will happen if we change the interval timer to say, 100ms, is that animation will feel choppy and stumbling. Additionally, there is [no real guarantee](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setTimeout#Reasons_for_delays_longer_than_specified) that the **setInterval** function will comply with the passed timeout.

# What is requestAnimationFrame()

It is a javascript API function that allows the user to perform animation-based loops more efficiently than the usual alternatives: **for-loop** or **setTimeout** & **setInterval** functions. 

Intensive graphics animations run with **requestAnimationFrame** API will get some preferent treatment and be optimized to create a smoother feel in the browser, compared to the previously mentioned counterparts:

* Animations will only run when visible: the browser will drastically throttle animation if it isn't executed in the active tab, hence saving up a ton of cpu cycles. 
* Browser with **setTimeout** will update the screen arbitrarily, trying to match the animation's redraw with the whole screen repaint, losing cpu cycles in the process.
* It does not require an update interval, hence adapting to the refresh rate of the computer. Animations are refreshed *when possible*, not *when told*.
* It will group all the animations into a single repaint, saving a lot of cpu cycles too.
* Battery friendly: as a consequence of the previous reasons.

Animations can be of any kind, either by manipulating the DOM, using WebGL, using CSS transitions, or using the *canvas* HTML element.

# How does it work

Use of [the **requestAnimationFrame** API](https://developer.mozilla.org/es/docs/Web/API/Window/requestAnimationFrame) is very simple:

* It accepts a callback function that will be called before triggering a repaint. The callback will receive the timestamp when the API was called. 
* It returns an integer representing the *frame id* that can be passed to the **cancelAnimationFrame** API to stop it from being painted.

The former example transformed to use **requestAnimationFrame** can be [found in this pen](https://codepen.io/anon/pen/XYWbLJ):

```javascript
let elem = document.getElementById("animated");

function animate(left) {
  return requestAnimationFrame((timestamp) => {
    elem.style.left = (left + 10) + "px";
    
    if (left < 1000) 
      return animate(left+10);
  });
}

animate(0);
```

Instead of using a pure function it can also be done by tracking the *left* variable externally, as seen [in this pen](https://codepen.io/anon/pen/Yvzrqy):

```javascript
let elem = document.getElementById("animated"),
    left = 0;

function animate(timestamp) {
  elem.style.left = (left += 10) + "px";
    
  if (left < 1000)
    return requestAnimationFrame(animate);
}

requestAnimationFrame(animate);
```

## Cancelling requestAnimationFrame() execution

As stated previously, animations can be cancelled by making a call to the **cancelAnimationFrame** API and passing the *frame  id* returned by **requestAnimationFrame**. [Check this pen](https://codepen.io/anon/pen/Yvzrqy) to see the animation cancelled after one second. Then, comment out the `setTimeout` line and see how the green square moves way forward.

```javascript
let elem = document.getElementById("animated"),
    left = 0,
    frameId;

function animate(timestamp) {
  elem.style.left = (left += 10) + "px";
    
  if (left < 1000)
    frameId = requestAnimationFrame(animate);
}

frameId = requestAnimationFrame(animate);

setTimeout(() => cancelAnimationFrame(frameId), 1000);
```


## Slowing down the animation

The frame rate of an animation using **requestAnimationFrame** can also be adjusted by throttling the call to the API inside a **setTimeout** call and dividing a second between the frame rate desired, slowering it down, like shown [in this pen](https://codepen.io/anon/pen/GGROvG).

```javascript
let elem = document.getElementById("animated"),
    left = 0,
    fps = 10;

function animate(timestamp) {
  setTimeout(() => {
    elem.style.left = (left += 10) + "px";
    
    if (left < 1000)
      requestAnimationFrame(animate);
  }, 1000/fps);
}

requestAnimationFrame(animate);
```

So, that is pretty much it. **requestAnimationFrame** is a basic construct used in modern javascript frameworks and libraries such as React, Vue, Preact, Phaser, Pixi, etc..


Have fun!


