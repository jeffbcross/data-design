# Roadmap for Data Access and Persistence in Angular 2

This document describes the Angular team's goals in improving application data access and persistence. The document is meant to give a high-level overview of priorities and approaches,
without diving too deeply into implementation details and timelines.

## Goal

Help developers make more robust and resilient apps for all web users, in varying technical circumstances, while accounting for real world challenges and constraints of managing application data and assets.

### More specifically...  

 * Provide seamless and transparent user experience in the face of intermittent network connectivity
   * Strike a balance between completely disabling functionality, or completely hiding problems when network connectivity drops
 * Empower users when things go wrong
   * Don't be afraid to say if connectivity has dropped or data synchronization has failed; provide the user options to resolve issues 
 * Easily adapt to varying bandwidth capabilities of users
   * Data and asset fetching should adapt what to fetch/not to fetch, or what alternatives to fetch based on current bandwidth capabilities  
 * Fast load, and high responsiveness 
   * Apps should be intelligent about having the right data available at the right time, to load apps quickly and improve responsiveness to user actions   

### Real-World Constraints to Consider

 * Applications support many browsers, many of which don't have the latest native APIs
 * Backend capabilities differ, in terms of what protocols are supported (Http/1.1, Http/2, WebSockets) and what types of services may be created
 * Application data often comes from many sources, not a single backend
 * The way data is structured, and the way it flows is different for every application
 * Teams building applications may be one person building an app in a week, or a large team of many developers focusing on different parts of an application for several years
 * Angular applications may run in environments other than JavaScript in a Browser (Dart, Node, iOS/Android WebViews, iOS/Android Native)
 * Optimal solutions to data access and performance depend on many factors, including but not limited to the application model, the user's current network latency, the user's current bandwidth, the user's cost for data upload/download, and the user's current processing power.


## Real World Use Cases and Best Practices

The following challenges and best practices were developed to help guide the types of tools needed to provide the optimal user experience. These are meant to be general guides, not to be strict rules. An optimal user experience depends on many factors.a

### Fast Load in All Circumstances

Initial load of an app should immediately provide a reasonable experience in all network conditions, assuming the app has been loaded previously.

### At Least Provide Read-Only Views When Offline

Even if an app decides not to allow mutating data when offline, read-only functionality should be maintained (where practical). Applications should provide cues to users if the data is especially stale, and should consider allowing optimistic mutation of data if the application model allows.

### Eagerly Load Data if Practical 

Applications should leverage good connectivity to make data available offline even if not explicitly requested by the user, with consideration for bandwidth and battery costs to the user.

### Jank-Free UI

The UI should never block user interactivity or hang on network activity.

### Zero-Perceived-Latency Responsiveness

Application should acknowledge every user interaction in &lt;100 ms. Navigation, form submission, changing tabs. 

### Work with Users 

Application should provide clear indications of how connectivity is affecting performance and other capabilities.

### Refresh Survival 

Application state should survive an OS-imposed refresh, even when offline and with a dirty view.

### Intelligent, Uninterrupted Experience with Varying Bandwidth

Application should gracefully transition between experiences optimized for connections with low bandwidth and high bandwidth

### Optimistic, Transparent Mutations

Application should make clear when committed model mutations have not been confirmed as persisted to remote data source, especially when further mutations are being made.

### First-Class Cache

Applications should cache as much as possible, and read from cache as much as possible, using network communication sparingly, even if only portions of a model are able to be cached or read from cache. 

### Data-Aware Navigation 

Applications should wait until a new view has enough data to render before destroying a previous view and rendering the new view. And should not automatically navigate if operation takes too long, but prompt user to manually navigate to new view.

## Layers of Solutions

Not all of the above best practices can be solved by frameworks or libraries, but the design of Angular core can be considered in ways that make these best practices more within reach for developers. These are the layers at which these best practices are being made more possible to achieve: 

 * Good core support
   * The highest priority is to make the core of Angular 2 play nicely with all kinds of data, regardless of how it's structured, how it's wrapped, where it comes from, or how it gets to the app. Two existing examples of these solutions are [pipes](https://angular.io/docs/js/latest/api/pipes/) and [zones](https://github.com/angular/zone.js/), with more solutions being designed or considered, such as deeper integration of services with Angular's router and compiler to support prioritized data fetching and rendering, as well as support server-side rendering.
 * Browser Wrappers
   * Browsers now provide many tools to solve hard problems with data, such as persistence and asset caching. These tools are not always easy to use, and often difficult to test. Here are some native APIs for which Angular will provide thin wrappers around, as well as the approach being taken in the implementation:
     * HTTP APIs (Status: In-progress, [first commit](https://github.com/angular/angular/commit/21568106b114943b59ce37832d82c8fb9a63285b)) (`[XMLHttpRequest](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)`, `[fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)`, etc). Wrappers will provide a composable, Observable-based interface, as well as simplify the process of performing a request. The abstraction will improve mocking and testability, and will make it possible to create isomorphic code to run in different environments (JS in browser, JS in node, Dart, ServiceWorker, etc) by replacing the underlying transport (i.e. `XMLHttpRequest`) using dependency injection. The Http library will provide conveniences to simplify the use of techniques such as request retrying, short polling, long polling, and request cancellation. It will also focus on reducing boilerplate and complexity for projects and teams of all sizes.
     * LocalStorage and SessionStorage (Status: Planned): These APIs will provide mocks to allow testing code without interacting with real browser storage APIs. The abstraction will also provide an [Observable](https://github.com/zenparsing/es-observable) interface on top of the APIs, which will allow subscription to changes to values in the store, specified by key.
     * IndexedDB (Status: Planned)
     * WebSocket (Status: Planned)
     * ServiceWorker/AppCache
 * First-Class Libraries
   * Network State (Status: Research)
   * Task Scheduler (Status: Research)
   * Model Metadata (Status: Research)
   * View State Preservation (Status: Research) i.e. preserve the in-memory model and scroll position to survive OS-forced refresh on mobile browsers where memory is scarce.
 * First-Class and Third-Party Data Frameworks
   * Tactical
   * Falcor
   * Firebase/AngularFire
   * Angular-Meteor
   * Restangular
   * Lovefield
   * BreezeJS
   * etc

## Further Reading

 * @jeffbcross and @synalx present [The Future of Data in Angular](https://www.youtube.com/watch?v=Bm3eDgZZMFs) at ng-vegas 2015.

