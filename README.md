To use, follow these steps:

### 1. Add the attribute to the trigger element:

```html
scrollTrigger="year-1975"
```

### 2. Add this script before </body>:

```jsx
// This is a universal function that can be called multiple times
// This is a universal function that can be called multiple times
class ScrollTrigger {
  constructor(triggerEl, options) {
    this.TRIGGERS_ELEMENTS = document.querySelectorAll(triggerEl);
    this.options = options;
    this.state = {
      triggers: [
        {
          element: null,
          name: null,
          height: 0,
          documentTopOffset: 0,
        },
      ],
      triggerRunStatus: [],
      pageHeight: 0,
      windowHeight: 0,
      currentPosition: 0,
    };
    this.startTrigger();
  }
  startTrigger() {
    this.prepareStatus();
    this.recalculatePage();
    this.recalculateScroll();
    this.dispatcher();
  }
  prepareStatus() {
    this.state.triggerRunStatus = [];
    this.TRIGGERS_ELEMENTS.forEach(() => {
      this.state.triggerRunStatus.push(false);
    });
  }
  dispatcher() {
    const optimizedScrollHandler = this.throttle(
      this.recalculateScroll.bind(this),
      200
    );
    const optimizedResizeHandler = this.throttle(
      this.recalculatePage.bind(this),
      200
    );
    window.addEventListener("scroll", optimizedScrollHandler);
    window.addEventListener("resize", optimizedResizeHandler);
    this.loopRecalculatePage(optimizedResizeHandler);
  }
  loopRecalculatePage(handler) {
    setTimeout(() => {
      handler();
      this.loopRecalculatePage(handler);
    }, 400);
  }
  recalculateScroll() {
    this.state.currentPosition =
      window.pageYOffset || document.documentElement.scrollTop;
    this.state.triggers.forEach((trigger, index) => {
      if (
        this.state.currentPosition >
          trigger.documentTopOffset - this.state.windowHeight &&
        this.state.currentPosition < trigger.documentTopOffset + trigger.height
      ) {
        // into view
        if (this.state.triggerRunStatus[index] === false) {
          if (this.options.intoView !== undefined) {
            this.options.intoView(trigger);
          }
          this.state.triggerRunStatus[index] = true;
          document.body.setAttribute(trigger.name, "");
        }
      } else {
        // out of view
        if (this.state.triggerRunStatus[index] === true) {
          if (this.options.outOfView !== undefined) {
            this.options.outOfView(trigger);
          }
          this.state.triggerRunStatus[index] = false;
          document.body.setAttribute(trigger.name, "");
        }
        document.body.removeAttribute(trigger.name);
      }
    });
  }
  recalculatePage() {
    this.state.windowHeight = window.innerHeight;
    this.state.currentPosition =
      window.pageYOffset || document.documentElement.scrollTop;
    this.state.triggers = [];
    this.TRIGGERS_ELEMENTS.forEach((triggerEl) => {
      const triggerRect = triggerEl.getBoundingClientRect();
      const triggerName = triggerEl.getAttribute("scrollTrigger");
      this.state.triggers.push({
        element: triggerEl,
        name: triggerName,
        intoViewUsed: false,
        outOfViewUsed: false,
        height: triggerEl.clientHeight,
        documentTopOffset: this.state.currentPosition + triggerRect.top,
      });
    });
    this.state.pageHeight = document.body.offsetHeight;
  }
  throttle(callee, timeout) {
    let timer = null;
    return function perform(...args) {
      if (timer) return;
      timer = setTimeout(() => {
        callee(...args);
        clearTimeout(timer);
        timer = null;
      }, timeout);
    };
  }
}

const intoView = (trigger) => {
  // you can add your code here
  console.log(["into view", trigger.name]);
};

const outOfView = (trigger) => {
  // you can add your code here
  console.log(["out of view", trigger.name]);
};

new ScrollTrigger("[scrollTrigger]", {
  intoView: intoView,
  outOfView: outOfView,
});
```

### 3. Add CSS with animation to the body:

> **[year-1975]** – this is the attribute that appears on the body (it is also used in the attribute)
> **.c4-bg-color** – this is the class of the element you want to animate

Easings can be added in Webflow in the transition tab (delay cannot be added), or you can add them here in the CSS.

>

```css
[year-1975] .c4-bg-color {
  background-color: #ea4e3f;
}
```
