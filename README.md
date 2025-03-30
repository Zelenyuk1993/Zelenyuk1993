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

## 📘 Introduction

This guide demonstrates how to integrate Tealium’s `utag.js` into an **Angular 18 Standalone Application** using a production-ready, scalable pattern.  
It supports asynchronous loading, configuration by environment, SPA behavior (`noview: true`), and easy tracking of `view` and `link` events.

---

## 🛠️ Create `TealiumUtagService`

This service handles:
- dynamic `utag.js` loading
- full SPA support
- dynamic environment configuration
- centralized `track`, `view`, `link` methods

**📁 `src/app/core/tealium-utag.service.ts`**

```ts
import { Injectable } from '@angular/core';

@Injectable()
export class TealiumUtagService {
  private scriptSrc = '';
  private configSet = false;

  constructor() {
    (window as any).utag_cfg_ovrd = { noview: true }; // disable auto page view for SPA
    (window as any).utag_data = {}; // optional global data layer
  }

  setConfig(config: { account: string; profile: string; environment: string }) {
    if (config.account && config.profile && config.environment) {
      this.scriptSrc = \`https://tags.tiqcdn.com/utag/\${config.account}/\${config.profile}/\${config.environment}/utag.js\`;
      this.configSet = true;
    }
  }

  getScript(src: string, callback: () => void): void {
    const d = document;
    const s = d.createElement('script');
    const t = d.getElementsByTagName('script')[0];
    s.type = 'text/javascript';
    s.async = true;
    s.charset = 'utf-8';
    s.src = src;

    s.onload = () => {
      console.log('✅ utag.js loaded');
      callback();
    };

    s.onerror = () => {
      console.error('❌ Failed to load Tealium utag.js');
    };

    t.parentNode?.insertBefore(s, t);
  }

  private track(eventType: 'view' | 'link', data?: any): void {
    if (!this.configSet) {
      console.warn('⚠️ Tealium config not set. Call setConfig() first.');
      return;
    }

    const utag = (window as any).utag;
    const doTrack = () => utag?.track(eventType, data);

    if (utag === undefined) {
      this.getScript(this.scriptSrc, () => doTrack());
    } else {
      doTrack();
    }
  }

  view(data?: any): void {
    this.track('view', data);
  }

  link(data?: any): void {
    this.track('link', data);
  }
}
```

---

## ⚙️ Integrate in Application

### 1. Register the service in `main.ts`

**📁 `src/main.ts`**

```ts
import { bootstrapApplication } from '@angular/platform-browser';
import { AppComponent } from './app/app.component';
import { TealiumUtagService } from './app/core/tealium-utag.service';

bootstrapApplication(AppComponent, {
  providers: [TealiumUtagService]
}).then(appRef => {
  const tealium = appRef.injector.get(TealiumUtagService);
  tealium.setConfig({
    account: 'your-account',
    profile: 'your-profile',
    environment: 'prod' // or 'dev' / 'qa'
  });
});
```

---

### 2. Use in components

**📁 Any standalone component**

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

  ngOnInit(): void {
    this.tealium.view({
      page_name: 'dashboard',
      user_id: 'user-123',
      section: 'admin'
    });
  }

  trackAction(): void {
    this.tealium.link({
      event_name: 'button_click',
      button_id: 'save-settings'
    });
  }
}
```

---

## 🔍 Testing Tips

1. Open browser DevTools → Network tab
2. Filter by `collect`
3. Confirm request to:
   ```
   https://collect.tealiumiq.com/event
   ```
4. View payload with your custom fields

---

## 📦 Advanced: Global Data Layer

You can preload a global data layer that will be merged into each event:

```ts
(window as any).utag_data = {
  app_name: 'TealiumTestApp',
  user_role: 'admin'
};
```

You can still pass custom event-specific fields when calling `.view()` or `.link()`.

---

## ✅ Summary

- Fully SPA-compatible with async script loading
- Configurable for any environment (prod/dev/qa)
- Supports `view()` and `link()` with fallback
- Ready for enterprise use and scalable tracking

---
