### Hi there 👋

Hi every one I'm Dmytro a front-end developer from Ukraine. Live in the USA.
6+ years of demonstrated experience in application development, systems architecture, and design.
Experience working in an Agile environment with a blended team of BA, Software Engineers, UI/UX Designers, and QA.
Strong software engineering foundation: 
- System design and architecture;
- Design patterns;
- Programming principles, and the best code practices.
During my career I was involved mainly in Front-End development, using:
- TypeScript,
- Angular,
- Ionic,
- JavaScript.
- Had solid experience in integration blockchain smart contracts and work with Web3.

Here are some ideas to get you started:

- 🔭 I’m currently working on ...
- 🌱 I’m currently learning Flutter and Mobile UI
- 👯 I’m looking to collaborate on open source Flutter, Angular project
- 💬 Ask me about Angular / Flutter / Ionic / TypeScript / etc.
- 📫 How to reach me: [Github](https://github.com/Zelenyuk1993), [Facebook](https://www.facebook.com/dima.zelenyuk), [Linkedin](https://www.linkedin.com/in/dmytro-zeleniuk/)
- 😄 Pronouns: He/him
- ⚡ Fun fact: It’s not a bug – it’s an undocumented feature.





# 🧩 Tealium Integration – Angular 18 Standalone App

**Last updated:** Mar 30, 2025  
**Owner:** [Your Name]  
**Tags:** `angular18`, `tealium`, `analytics`, `standalone`, `M&T`   
**References:**  
- [Tealium Official Docs](https://docs.tealium.com/)  
- [Tealium utag.js Guide](https://docs.tealium.com/platforms/javascript/install/)  
- [Tealium for Angular](https://docs.tealium.com/platforms/angular/)

---

## 📘 Introduction

Tealium is an enterprise-level analytics and tag management platform used by M&T Bank to register customer to track user interactions and behavior across web applications.  
This guide provides a step-by-step walkthrough to integrate **Tealium** into an **Angular 18 standalone project**, using `utag.js` to track page views and custom events.

---

## 🛠️ Initial Setup

### 1. ✅ Dynamically Load the `utag.js` Script

Avoid placing the script in `index.html`. Instead, dynamically inject it using a service.

**📁 `src/app/core/tealium-loader.service.ts`**
```ts
import { Injectable } from '@angular/core';

@Injectable({ providedIn: 'root' })
export class TealiumLoaderService {
  load(): void {
    const script = document.createElement('script');
    script.src = 'https://your-tealium-url.com/utag.js'; // Replace with actual Tealium script path
    script.async = true;
    document.head.appendChild(script);
  }
}
```

---

### 2. 🧠 Create the `TealiumUtagService`

This service wraps around the standard Tealium methods: `view()`, `link()`, and `setConfig()`.

**📁 `src/app/core/tealium-utag.service.ts`**
```ts
import { Injectable } from '@angular/core';

@Injectable({ providedIn: 'root' })
export class TealiumUtagService {
  private get utag() {
    return (window as any).utag;
  }

  setConfig(config: Record<string, string>): void {
    console.log('Tealium config set:', config);
  }

  view(data: Record<string, any>): void {
    this.utag?.view?.(data);
  }

  link(data: Record<string, any>): void {
    this.utag?.link?.(data);
  }
}
```

---

## 🧱 Angular 18 Standalone Integration

### 3. 🚀 Load Tealium in `main.ts` During Bootstrap

Use the Angular standalone `bootstrapApplication` method to call `TealiumLoaderService` on startup.

**📁 `src/main.ts`**
```ts
import { bootstrapApplication } from '@angular/platform-browser';
import { AppComponent } from './app/app.component';
import { TealiumLoaderService } from './app/core/tealium-loader.service';

bootstrapApplication(AppComponent, {
  providers: [
    TealiumLoaderService
  ]
}).then(appRef => {
  const injector = appRef.injector;
  const tealiumLoader = injector.get(TealiumLoaderService);
  tealiumLoader.load();
});
```

---

## 🔍 Usage Examples

### 4.1. Track Page Views with `view()`

**📁 `dashboard.component.ts`**
```ts
import { Component, OnInit } from '@angular/core';
import { TealiumUtagService } from '../core/tealium-utag.service';

@Component({
  selector: 'app-dashboard',
  standalone: true,
  templateUrl: './dashboard.component.html'
})
export class DashboardComponent implements OnInit {
  constructor(private tealium: TealiumUtagService) {}

  ngOnInit() {
    this.tealium.view({
      page_name: 'dashboard',
      user_id: 'user123'
    });
  }
}
```

---

### 4.2. Track Button Clicks with `link()`

**📁 `example.component.html`**
```html
<button (click)="trackClick()">Click Me</button>
```

**📁 `example.component.ts`**
```ts
import { Component } from '@angular/core';
import { TealiumUtagService } from '../core/tealium-utag.service';

@Component({
  selector: 'app-example',
  standalone: true,
  templateUrl: './example.component.html'
})
export class ExampleComponent {
  constructor(private tealium: TealiumUtagService) {}

  trackClick() {
    this.tealium.link({
      event_name: 'example_button_click',
      timestamp: new Date().toISOString()
    });
  }
}
```

---

## 🧪 Debugging and Verification

- Check browser DevTools > Network tab for `utag.js` and beacon requests
- Manually test in console:
```js
utag.view({ test_event: 'manual_page_view' });
utag.link({ test_event: 'manual_button_click' });
```
- Confirm events appear in Tealium dashboard or via tag management logs

---

## 📖 API Reference

| Method        | Description                                                    |
|---------------|----------------------------------------------------------------|
| `link(data)`  | Tracks in-page interactions (clicks, actions)                  |
| `view(data)`  | Tracks virtual page views                                      |
| `setConfig()` | Optional method to provide runtime configuration for utag.js   |

---

## 📌 Final Notes

- Always ensure `utag.js` is loaded before calling `link()` or `view()`.
- Load the script at the earliest lifecycle moment (`main.ts`).
- Extend `setConfig()` to support environment-based configurations if needed.

---

## ✅ Summary

- ✅ Tealium is loaded via a dynamic service
- ✅ Analytics events are tracked with a lightweight wrapper service
- ✅ Fully compatible with Angular 18 Standalone architecture
- ✅ Easy to extend for custom business events
