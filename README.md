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




h1. 🧩 Tealium Integration – Angular 18 Standalone App (Production Ready)

*Last updated:* Mar 30, 2025  
*Author:* [Your Name]  
*Tags:* _angular18, tealium, analytics, SPA, production_  
*References:*  
- [Tealium Official Docs|https://docs.tealium.com/]  
- [Tealium utag.js Guide|https://docs.tealium.com/platforms/javascript/utag-js/]  
- [Angular Standalone Components|https://angular.io/guide/standalone-components]

----

h2. 📘 Introduction

This guide demonstrates how to integrate Tealium’s utag.js into an *Angular 18 Standalone Application* using a production-ready, scalable pattern.

----

h2. 🛠️ Create TealiumUtagService

This service handles dynamic script loading, full SPA support, and centralized event tracking.

{code:typescript|title=tealium-utag.service.ts}
import {{ Injectable }} from '@angular/core';

@Injectable()
export class TealiumUtagService {
  private scriptSrc = '';
  private configSet = false;

  constructor() {
    (window as any).utag_cfg_ovrd = {{ noview: true }};
    (window as any).utag_data = {{}};
  }

  setConfig(config: {{ account: string; profile: string; environment: string }}) {
    if (config.account && config.profile && config.environment) {
      this.scriptSrc = `https://tags.tiqcdn.com/utag/${{config.account}}/${{config.profile}}/${{config.environment}}/utag.js`;
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

    s.onload = () => {{
      console.log('✅ utag.js loaded');
      callback();
    }};

    s.onerror = () => {{
      console.error('❌ Failed to load Tealium utag.js');
    }};

    t.parentNode?.insertBefore(s, t);
  }

  private track(eventType: 'view' | 'link', data?: any): void {
    if (!this.configSet) {{
      console.warn('⚠️ Tealium config not set.');
      return;
    }}

    const utag = (window as any).utag;
    const doTrack = () => utag?.track(eventType, data);

    if (utag === undefined) {{
      this.getScript(this.scriptSrc, () => doTrack());
    }} else {{
      doTrack();
    }}
  }

  view(data?: any): void {{
    this.track('view', data);
  }}

  link(data?: any): void {{
    this.track('link', data);
  }}
}
{code}

----

h2. ⚙️ Integrate in Application

h3. 1. Register the service in main.ts

{code:typescript|title=main.ts}
import {{ bootstrapApplication }} from '@angular/platform-browser';
import {{ AppComponent }} from './app/app.component';
import {{ TealiumUtagService }} from './app/core/tealium-utag.service';

bootstrapApplication(AppComponent, {{
  providers: [TealiumUtagService]
}}).then(appRef => {{
  const tealium = appRef.injector.get(TealiumUtagService);
  tealium.setConfig({{
    account: 'your-account',
    profile: 'your-profile',
    environment: 'prod'
  }});
}});
{code}

----

h3. 2. Use in components

{code:typescript|title=dashboard.component.ts}
import {{ Component, OnInit }} from '@angular/core';
import {{ TealiumUtagService }} from '../core/tealium-utag.service';

@Component({{
  selector: 'app-dashboard',
  standalone: true,
  templateUrl: './dashboard.component.html'
}})
export class DashboardComponent implements OnInit {{
  constructor(private tealium: TealiumUtagService) {{}}

  ngOnInit(): void {{
    this.tealium.view({{
      page_name: 'dashboard',
      user_id: 'user-123',
      section: 'admin'
    }});
  }}

  trackAction(): void {{
    this.tealium.link({{
      event_name: 'button_click',
      button_id: 'save-settings'
    }});
  }}
}}
{code}

----

h2. 🔍 Testing Tips

* Open browser DevTools → Network tab
* Filter by `collect`
* Confirm request to:
`https://collect.tealiumiq.com/event`
* Inspect request payload

----

h2. 📦 Advanced: Global Data Layer

You can preload a global data layer to include with all events:

{code:javascript}
(window as any).utag_data = {{
  app_name: 'TealiumTestApp',
  user_role: 'admin'
}};
{code}

----

h2. ✅ Summary

* SPA-compatible with async script loading
* Configurable per environment (prod/dev/qa)
* Supports view() and link() with fallback
* Ready for scalable tracking in production
