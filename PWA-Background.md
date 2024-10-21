# PWA - Progressive Web App

Created by: Richard
Created time: June 10, 2024 8:06 AM
Tags: Intermediate Package

# Progressive Web App

### Key Resources:

1. https://pwabuilder.com

1. https://web.dev/articles/pwa-checklist
2. https://developer.chrome.com/docs/workbox
3. https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps
4. https://serviceworkies.com/
5. [https://caniuse.com](https://caniuse.com/) for checking compatibility/support of specific features across browsers

Testing Tools

1. https://developer.chrome.com/docs/lighthouse/overview
2. [https://www.browserstack.com/guide/how-to-test-pwa](https://www.browserstack.com/guide/how-to-test-pwa) for testing across browsers

### **What is a PWA?**

While having a `manifest.json` file is a crucial step for making a web app installable as a Progressive Web App (PWA), it is not the only requirement. Here are the key components required to make a web app installable as a PWA:

1. **Manifest File**: The `manifest.json` file must be properly configured with essential properties such as `name`, `short_name`, `start_url`, `display`, `icons`, `background_color`, and `theme_color`.
2. **Service Worker**: A service worker is a script that your browser runs in the background, separate from the web page, opening the door to features that don't need a web page or user interaction. The service worker is essential for making a web app work offline and for caching assets.
3. **Served Over HTTPS**: PWAs must be served over a secure connection (HTTPS). This is important for security reasons, especially since service workers can intercept network requests.
4. **Valid Web App Manifest**: The `manifest.json` must be correctly referenced in the HTML `<head>` section of your web pages.
5. **Responsive Design**: The app should be responsive and work well on different screen sizes and orientations.

Manifest.JSON

```
{
  "name": "My Web App",
  "short_name": "WebApp",
  "start_url": "/index.html",
  "display": "standalone",
  "background_color": "#ffffff",
  "theme_color": "#000000",
  "icons": [
    {
      "src": "/images/icons-192.png",
      "sizes": "192x192",
      "type": "image/png"
    },
    {
      "src": "/images/icons-512.png",
      "sizes": "512x512",
      "type": "image/png"
    }
  ]
}

```

HTML <HEAD>

```
<link rel="manifest" href="/manifest.json">

```

service-worker.js

```
self.addEventListener('install', function(event) {
/ Perform install steps
  event.waitUntil(
    caches.open('my-cache')
      .then(function(cache) {
        console.log('Opened cache');
        return cache.addAll([
          '/',
          '/index.html',
          '/styles.css',
          '/script.js',
          '/images/icons-192.png',
          '/images/icons-512.png'
        ]);
      })
  );
});

self.addEventListener('fetch', function(event) {
  event.respondWith(
    caches.match(event.request)
      .then(function(response) {
        // Cache hit - return response
        if (response) {
          return response;
        }
        return fetch(event.request);
      }
    )
  );
});

```

Main JS File need service worker registering

```
if ('serviceWorker' in navigator) {
  window.addEventListener('load', function() {
    navigator.serviceWorker.register('/service-worker.js').then(function(registration) {
      console.log('ServiceWorker registration successful with scope: ', registration.scope);
    }, function(error) {
      console.log('ServiceWorker registration failed: ', error);
    });
  });
}

```

Must be served over HTTPS, all tested by Google's Lighthouse

## Session 1 - Manifest Show & Tell

### In 30 secs

**What is it:** A JSON file that provides metadata about the PWA to the browser.

- **Key Ideas:**
    - Defines how the app appears and behaves when installed.
    - Specifies app name, icons, start URL, and display options.
    - Enables the "Add to Home Screen" prompt.
    - Supports theme and background color for UI consistency.
    - Enhances user experience by providing a native app-like look and feel.
- **Entry Point:**
    - The `manifest.json` file located in the root directory of the PWA.
- **Design Decisions:**
    - Decide on the app's name, short name, and description.
    - Choose appropriate icons in various sizes.
    - Set the start URL and scope for navigation.
    - Determine display mode (fullscreen, standalone, etc.).
    - Define theme and background colors for branding consistency.

### Resources

1. https://www.w3.org/TR/appmanifest/
2. https://web.dev/articles/add-manifest
3. https://developer.chrome.com/docs/extensions/reference/manifest
4. https://developer.mozilla.org/en-US/docs/Web/Manifest
5. https://learn.microsoft.com/en-us/microsoft-edge/progressive-web-apps-chromium/how-to/web-app-manifests
6. https://blog.pwabuilder.com/posts/the-manifest-editor/
7. https://docs.pwabuilder.com/#/home/pwa-intro?id=web-app-manifests

### Key Questions:

What are the key fields in a web app manifest and how do they impact the PWA's appearance and behavior?
How can we customize the manifest to improve user experience and engagement, such as adding splash screens and icons?
What tools can we use to validate and optimize our manifest file?

### Background Reading for Web App Manifests

1. What are the key fields in a web app manifest and how do they impact the PWA's appearance and behavior?

Key Fields in a Web App Manifest:

- name
Description: The name of the web application, displayed in the app launcher and possibly elsewhere in the user interface.
Impact: Provides the primary identifier for the app; shown to users during installation.
Example: "name": "My Payment App"

- short_name
Description: A shorter version of the name, used when there is insufficient space to display the full name.
Impact: Ensures the app name fits well in limited UI spaces, like home screen icons.
Example: "short_name": "PayApp"

- start_url
Description: The URL that loads when the app is launched.
Impact: Defines the entry point of the app; ensures users start at a consistent page.
Example: "start_url": "/index.html"

- display
Description: Defines the display mode of the app (e.g., standalone, fullscreen, minimal-ui, browser).
Impact: Controls how the app appears and behaves when launched, influencing user experience.
Example: "display": "standalone"

- background_color
Description: The background color of the splash screen and the background color of the application.
Impact: Enhances visual consistency during the app loading process.
Example: "background_color": "#ffffff"

- theme_color
Description: The theme color for the application.
Impact: Affects the color of the app's title bar in some browsers, enhancing branding.
Example: "theme_color": "#0000ff"

- icons
Description: An array of objects representing the icons for the app in various sizes.
Impact: Ensures the app looks good on different devices and contexts, enhancing visual appeal.
Example:

```json
"icons": [
  {
    "src": "/images/icon-192x192.png",
    "sizes": "192x192",
    "type": "image/png"
  },
  {
    "src": "/images/icon-512x512.png",
    "sizes": "512x512",
    "type": "image/png"
  }
]
```

- scope
Description: The navigation scope of the web app.
Impact: Restricts the navigation to a subset of the web; helps in maintaining a consistent experience.
Example: "scope": "/app/"

- description
Description: A short description of the web application.
Impact: Provides additional context to users about the app’s purpose and functionality.
Example: "description": "A user-friendly payment app for managing transactions"

1. How can we customize the manifest to improve user experience and engagement, such as adding splash screens and icons?
Customizing the Manifest:

Icons:
Provide icons in multiple sizes and resolutions to ensure they look good on various devices and contexts.
Example:

```json
"icons": [
  {
    "src": "/images/icon-48x48.png",
    "sizes": "48x48",
    "type": "image/png"
  },
  {
    "src": "/images/icon-72x72.png",
    "sizes": "72x72",
    "type": "image/png"
  },
  {
    "src": "/images/icon-96x96.png",
    "sizes": "96x96",
    "type": "image/png"
  },
  {
    "src": "/images/icon-144x144.png",
    "sizes": "144x144",
    "type": "image/png"
  },
  {
    "src": "/images/icon-192x192.png",
    "sizes": "192x192",
    "type": "image/png"
  },
  {
    "src": "/images/icon-512x512.png",
    "sizes": "512x512",
    "type": "image/png"
  }
]
```
```

Splash Screen Customization:
Use background_color to set the background of the splash screen.
Provide a high-resolution icon to ensure it looks good when the app is launched.
Example:

```json
```
"background_color": "#ffffff",
"theme_color": "#0000ff",
"icons": [
  {
    "src": "/images/splash-512x512.png",
    "sizes": "512x512",
    "type": "image/png"
  }
]
```

Display Modes:

Choose the display mode that best fits the app’s purpose. standalone mode provides an app-like experience without browser UI elements.
Example:

```json
```
"display": "standalone"
```
```

3. What tools can we use to validate and optimize our manifest file?

### Tools for Validating and Optimising Manifests:

Lighthouse:

A tool integrated into Chrome DevTools that provides audits for performance, accessibility, and PWA best practices.
Use Lighthouse to validate and get suggestions for improving your manifest.
How to use:
1. Open Chrome DevTools (F12).
2. Go to the "Lighthouse" tab.
3. Run an audit for "Progressive Web App."

PWA Builder:
An online tool that helps create and validate web app manifests. Provides a manifest editor and checks for common issues.

Web Manifest Validator:
A simple tool to validate your web app manifest.Web Manifest Validator Commented Example of a Web App Manifest.

```json

{
  "name": "My Payment App",  // Full name of the app, displayed during installation
  "short_name": "PayApp",  // Short name used on the home screen
  "start_url": "/index.html",  // URL to launch the app
  "display": "standalone",  // Display mode for the app, 'standalone' makes it look like a native app
  "background_color": "#ffffff",  // Background color for the splash screen
  "theme_color": "#0000ff",  // Theme color for the app
  "description": "A user-friendly payment app for managing transactions",  // Description of the app
  "icons": [  // Array of icons in different sizes
    {
      "src": "/images/icon-48x48.png",
      "sizes": "48x48",
      "type": "image/png"
    },
    {
      "src": "/images/icon-72x72.png",
      "sizes": "72x72",
      "type": "image/png"
    },
    {
      "src": "/images/icon-96x96.png",
      "sizes": "96x96",
      "type": "image/png"
    },
    {
      "src": "/images/icon-144x144.png",
      "sizes": "144x144",
      "type": "image/png"
    },
    {
      "src": "/images/icon-192x192.png",
      "sizes": "192x192",
      "type": "image/png"
    },
    {
      "src": "/images/icon-512x512.png",
      "sizes": "512x512",
      "type": "image/png"
    }
  ],
  "scope": "/app/"  // Navigation scope of the app
}
```

### Summary

By understanding the key fields in a web app manifest and how they impact the PWA's appearance and behavior, you can customize the manifest to improve user experience and engagement. Utilizing tools like Lighthouse, PWA Builder, and the Web Manifest Validator will help ensure your manifest is optimized and follows best practices.

## Session 2 - Service Worker Show & Tell

### In 30 secs

- **What is it:** A script that runs in the background, enabling offline functionality and other background tasks.
- **Key Ideas:**
    - Intercepts network requests to provide offline access.
    - Manages caching of assets and data for performance improvements.
    - Handles push notifications to re-engage users.
    - Supports background synchronization for reliable data updates.
    - Enhances reliability by serving cached content during network outages.
- **Entry Point:**
    - The `service-worker.js` file registered in your main JavaScript entry point.
- **Design Decisions:**
    - Determine what resources should be cached and when.
    - Choose a caching strategy (Cache First, Network First, etc.).
    - Implement logic for handling push notifications and background sync.
    - Set up event listeners for install, activate, and fetch events.
    - Ensure service worker updates are managed smoothly to avoid conflicts.

Open Questions:

1. What is the key word `navigator` mean
    1. 
    
    Navigator - [https://developer.mozilla.org/en-US/docs/Web/API/Navigator](https://developer.mozilla.org/en-US/docs/Web/API/Navigator)
    
    The **`Navigator`** interface represents the state and the identity of the user agent. It allows scripts to query it and to register themselves to carry on some activities.
    
    A `Navigator` object can be retrieved using the read-only [`window.navigator`](https://developer.mozilla.org/en-US/docs/Web/API/Window/navigator) property.
    
    - **What is it:**
        - An interface representing the state and identity of the user agent.
    - **Key Ideas:**
        - Provides access to the device's capabilities and user preferences.
        - Facilitates registration and management of service workers.
        - Supports querying and managing permissions.
        - Enables device-related functionalities like geolocation, media devices, and hardware concurrency.
        - Allows checking for installed related applications.
    - **Entry Point:**
        - Accessed via the `navigator` object in JavaScript.
    - **Design Decisions:**
        - Utilize `navigator.serviceWorker` to manage service worker lifecycle.
        - Use `navigator.permissions` to handle user permissions effectively.
        - Implement `navigator.getInstalledRelatedApps()` to check for related app installations.
        - Leverage device capabilities provided by `navigator` for enhanced functionalities.
        - Ensure secure contexts (HTTPS) are used when accessing sensitive `navigator` properties and methods.
    
2. Broadcast Channel API Usecase [https://developer.mozilla.org/en-US/docs/Web/API/Broadcast_Channel_API](https://developer.mozilla.org/en-US/docs/Web/API/Broadcast_Channel_API)
    
    ### Broadcast Channel API: Summary
    
    1. **What is it:** A web API for enabling communication between different browsing contexts (windows, tabs, iframes, web workers) of the same origin.
        1. **Key Ideas:**
            - Facilitates real-time data synchronization across multiple contexts.
                - Operates within the same origin for security reasons.
                - Simple API with methods for creating channels, sending messages, and listening for messages.
                - Ensures all subscribed contexts receive broadcasted messages.
                - Native to modern browsers with no need for external libraries.
            1. **Entry Point:** Create a new instance using `const channel = new BroadcastChannel('channel_name');`.
            2. **Design Decisions:**
                - Choose unique, descriptive channel names.
                - Implement robust message handling and security measures.
                - Consider the performance implications of frequent messaging.
                - Plan for fallback mechanisms for non-supporting browsers.
            3. **Use Case:** Real-time data synchronization, such as keeping multiple tabs of a collaborative document editing tool updated simultaneously.
            

### Resources:

1. https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API/Using_Service_Workers

2. https://serviceworkies.com/
3. https://web.dev/learn/pwa/service-workers
4. https://docs.pwabuilder.com/#/home/sw-intro

### Key Questions:

1. How do service workers enhance the PWA's performance and offline capabilities?
2. What are the best practices for writing and maintaining service workers, including error handling and updates?
3. How can we debug and test service workers effectively using Chrome DevTools?

### Service Workers: Detailed Overview

1. How do service workers enhance the PWA's performance and offline capabilities?
Enhancing Performance and Offline Capabilities:

- Caching:
Service workers can intercept network requests and cache responses, allowing for quicker load times and reduced reliance on the network.
Caching strategies (e.g., Cache First, Network First) help optimize the performance and ensure that users can access content even when offline.
Offline Support:

By caching important resources and data, service workers enable the PWA to function offline or in low-connectivity scenarios.
    
    
    Users can still interact with the app, access cached content, and perform actions that can later be synchronized when connectivity is restored.
    
- Background Sync:

Service workers can use the Background Sync API to defer actions until the user has a stable internet connection.
This ensures that critical data (e.g., form submissions, transactions) is reliably sent to the server without user intervention.
- Push Notifications:
Service workers can handle push notifications, allowing the app to re-engage users with timely updates and information.

2. What are the best practices for writing and maintaining service workers, including error handling and updates?

Best Practices for Writing and Maintaining Service Workers:

- Modular and Maintainable Code:

Write modular code by breaking down the service worker script into smaller functions or modules.
Use clear and descriptive names for functions and variables to improve readability and maintainability.
- Caching Strategies:

Choose appropriate caching strategies based on the type of resource e.g., 
1. Cache First for static assets 
2. Network First for dynamic content
    1. etc 
    
    Implement cache versioning to manage updates and avoid serving stale content.
    
- Error Handling:

Implement robust error handling to manage network failures, cache errors, and other issues.
Use try-catch blocks and handle promise rejections to ensure the service worker doesn't crash.
- Update Handling:

Use the skipWaiting and clients.claim methods to control the activation of new service worker versions.
Inform users about updates and provide a way to refresh the app to get the latest version.

- Performance Optimisation:

Minimise the service worker script size to reduce the initial load time.
Cache only necessary resources and use efficient caching strategies to optimise performance.

3. How can we debug and test service workers effectively using Chrome DevTools?

Debugging and Testing Service Workers Using Chrome DevTools:

- Service Worker Panel:

Open Chrome DevTools (F12 or right-click > Inspect).
Go to the "Application" tab and select "Service Workers" from the sidebar.
Here you can see the status of service workers, unregister them, and update them manually.
- Console Logs:

Use console.log statements in your service worker script to log information and errors.
Check the "Console" tab in DevTools to see these logs and debug issues.
Network Panel:
Use the "Network" tab to monitor network requests and see which ones are served from the cache.
Filter by "Service Worker" to view requests intercepted by the service worker

- Application Panel:

Inspect the "Cache Storage" section to see cached resources and manage caches.
Check the "Background Sync" and "Push Messaging" sections for related events and data.
Breakpoints:

Set breakpoints in your service worker script using the "Sources" tab to step through code and inspect variables.

### Sample Service Worker Code with Comments - Necessary Features

```jsx
// Define cache names
const CACHE_NAME = 'my-pwa-cache-v1';
const urlsToCache = [
  '/', // The root of the site (e.g., http://example.com/)
  '/index.html', // Main HTML file (e.g., http://example.com/index.html)
  '/styles.css', // Main CSS file (e.g., http://example.com/styles.css)
  '/script.js', // Main JS file (e.g., http://example.com/script.js)
  '/images/icon-192x192.png', // App icon (e.g., http://example.com/images/icon-192x192.png)
  '/images/icon-512x512.png', // Larger app icon (e.g., http://example.com/images/icon-512x512.png)
  '/offline.html' // Offline fallback page (e.g., http://example.com/offline.html)
];

// Install event - caching static assets
// This event is triggered when the service worker is installed
self.addEventListener('install', event => {
  event.waitUntil(
    caches.open(CACHE_NAME).then(cache => {
      console.log('Opened cache');
      return cache.addAll(urlsToCache); // Add all URLs to the cache
    })
  );
  self.skipWaiting(); // Activate worker immediately after installation
});

// Activate event - updating cache and cleaning up old caches
// This event is triggered when the service worker is activated
self.addEventListener('activate', event => {
  const cacheWhitelist = [CACHE_NAME];
  event.waitUntil(
    caches.keys().then(cacheNames => {
      return Promise.all(
        cacheNames.map(cacheName => {
          if (!cacheWhitelist.includes(cacheName)) {
            return caches.delete(cacheName); // Delete old caches
          }
        })
      );
    })
  );

  // Enable navigation preload if supported
  if (self.registration.navigationPreload) {
    self.registration.navigationPreload.enable();
  }

  self.clients.claim(); // Claim clients immediately after activation
});

// Fetch event - responding with cached resources or fetching from network
// This event is triggered for every network request
self.addEventListener('fetch', event => {
  if (event.request.mode === 'navigate') {
    // Handle navigation requests
    event.respondWith(
      fetch(event.request).catch(() => caches.match('/offline.html')) // Serve offline page if fetch fails
    );
  } else {
    // Handle other requests (e.g., CSS, JS, images)
    event.respondWith(
      caches.match(event.request).then(response => {
        if (response) {
          return response; // Return cached response if found
        }
        return fetch(event.request).then(networkResponse => {
          if (!networkResponse || networkResponse.status !== 200 || networkResponse.type !== 'basic') {
            return networkResponse; // Return network response if valid
          }
          return caches.open(CACHE_NAME).then(cache => {
            cache.put(event.request, networkResponse.clone()); // Cache the network response
            return networkResponse;
          });
        });
      }).catch(error => {
        console.error('Fetch failed; returning offline page instead.', error);
        if (event.request.headers.get('accept').includes('image')) {
          return new Response('<svg width="400" height="180"><rect width="400" height="180" style="fill: #f00;"></rect><text x="50%" y="50%" text-anchor="middle" fill="#fff" dy=".3em">Image not available</text></svg>', { headers: { 'Content-Type': 'image/svg+xml' } });
        }
      })
    );
  }
});

```

### Sample Service Worker Code with Comments - Advanced Features

```jsx
// Listen for push events - notifications
// This event is triggered when a push notification is received
self.addEventListener('push', event => {
  const title = 'Push Notification';
  const options = {
    body: event.data ? event.data.text() : 'Push message with no payload',
    icon: '/images/icon-192x192.png',
    badge: '/images/icon-192x192.png'
  };
  event.waitUntil(
    self.registration.showNotification(title, options) // Show notification
  );
});

// Listen for sync events - background sync
// This event is triggered when the service worker can retry a failed sync
self.addEventListener('sync', event => {
  if (event.tag === 'sync-tag-example') {
    event.waitUntil(syncData()); // Call syncData function on sync event
  }
});

// Function to sync data with the server
function syncData() {
  // Example function to sync data with the server
  // Sends a request to '/sync-endpoint' and logs the response
  return fetch('/sync-endpoint').then(response => response.json()).then(data => {
    console.log('Data synced successfully:', data);
  }).catch(error => {
    console.error('Sync failed:', error);
  });
}

// Example of stale-while-revalidate strategy for API requests
// This strategy serves cached content while updating the cache with new content
self.addEventListener('fetch', event => {
  if (event.request.url.includes('/api/')) { // Match API requests
    event.respondWith(
      caches.open(CACHE_NAME).then(cache => {
        return cache.match(event.request).then(response => {
          const fetchPromise = fetch(event.request).then(networkResponse => {
            cache.put(event.request, networkResponse.clone()); // Update cache with new data
            return networkResponse;
          });
          return response || fetchPromise; // Return cached data or fetch promise
        });
      })
    );
  }
});

// Broadcast Channel API for communication between service worker and main thread
const broadcast = new BroadcastChannel('sw-messages');

// Example of sending a message from the service worker
broadcast.postMessage({ type: 'CACHE_UPDATED', payload: { url: event.request.url } });

// In your main thread (app), listen for messages from the service worker
// Example of receiving a message
const broadcast = new BroadcastChannel('sw-messages');
broadcast.onmessage = event => {
  if (event.data.type === 'CACHE_UPDATED') {
    console.log('Cache updated for:', event.data.payload.url);
    // Update UI or perform other actions based on the message received
  }
};

// In your main thread (app), handle service worker updates
if ('serviceWorker' in navigator) {
  navigator.serviceWorker.register('/service-worker.js').then(registration => {
    registration.onupdatefound = () => {
      const installingWorker = registration.installing;
      installingWorker.onstatechange = () => {
        if (installingWorker.state === 'installed') {
          if (navigator.serviceWorker.controller) {
            // New update available, notify the user
            console.log('New or updated content is available.');
          } else {
            // Content cached for offline use
            console.log('Content is now available offline!');
          }
        }
      };
    };
  }).catch(error => {
    console.error('Error during service worker registration:', error);
  });
}

```

Summary

Service workers significantly enhance the performance and offline capabilities of PWAs by implementing advanced caching strategies, enabling offline support, handling background sync, and push notifications. By following best practices such as modular code, robust error handling, and efficient cache management, you can maintain a reliable and performant service worker. Utilizing Chrome DevTools allows you to effectively debug and test your service workers, ensuring they function correctly in various scenarios.

### Supplementary Questions

 
Will I have more than one service worker?
Typically, you would have one service worker per scope within a web application. The scope of a service worker is defined by the path where the service worker script is located. Here’s a more detailed explanation:

Understanding Service Worker Scope

Scope Definition:
The scope of a service worker is the set of URLs that the service worker can control. By default, a service worker's scope is the directory in which the service worker script is located and all subdirectories.

Single Service Worker:
In most cases, a single service worker is sufficient to manage all caching, background sync, and push notifications for a given PWA. This service worker can control all pages within its scope.
Multiple Service Workers:

You can technically have multiple service workers if your web application has distinct parts that need separate service workers. Each service worker would control a different scope of the application.

Example: You might have one service worker for the main application and another for an admin dashboard located in a different directory.

However, this approach is rare and can complicate your application’s architecture. It’s generally better to manage everything within a single service worker if possible.

Example Scenario with Multiple Scopes
Main Application Service Worker
Located at /service-worker.js, controlling everything under the root path:

```jsx
// Main Application Service Worker at /service-worker.js

const MAIN_CACHE_NAME = 'main-app-cache-v1';

self.addEventListener('install', event => {
  event.waitUntil(
    caches.open(MAIN_CACHE_NAME).then(cache => {
      return cache.addAll([
        '/',
        '/index.html',
        '/styles/main.css',
        '/scripts/main.js'
      ]);
    })
  );
});

self.addEventListener('fetch', event => {
  event.respondWith(
    caches.match(event.request).then(response => {
      return response || fetch(event.request).then(networkResponse => {
        return caches.open(MAIN_CACHE_NAME).then(cache => {
          cache.put(event.request, networkResponse.clone());
          return networkResponse;
        });
      });
    })
  );
});
```

Admin Dashboard Service Worker
Located at /admin/service-worker.js, controlling everything under the /admin path:

```jsx
// Admin Dashboard Service Worker at /admin/service-worker.js

const ADMIN_CACHE_NAME = 'admin-cache-v1';

self.addEventListener('install', event => {
  event.waitUntil(
    caches.open(ADMIN_CACHE_NAME).then(cache => {
      return cache.addAll([
        '/admin/',
        '/admin/index.html',
        '/admin/styles/admin.css',
        '/admin/scripts/admin.js'
      ]);
    })
  );
});

self.addEventListener('fetch', event => {
  event.respondWith(
    caches.match(event.request).then(response => {
      return response || fetch(event.request).then(networkResponse => {
        return caches.open(ADMIN_CACHE_NAME).then(cache => {
          cache.put(event.request, networkResponse.clone());
          return networkResponse;
        });
      });
    })
  );
});
```

Best Practices
Single Service Worker per PWA:

Aim to use a single service worker for your entire application whenever possible. This simplifies the architecture and makes maintenance easier.

Managing Scope:
Carefully manage the scope of your service worker by placing the service worker script in an appropriate directory.
If your application requires multiple scopes, ensure each service worker is properly scoped to control only the necessary parts of your application.

Versioning and Updates:
Use cache versioning to manage updates effectively. This ensures that old caches are cleaned up and new content is served efficiently.

Summary
While it’s possible to have multiple service workers in an application, it’s generally advisable to manage everything with a single service worker for simplicity and maintainability. Each service worker controls a specific scope, defined by its location in the file system. By carefully managing the scope and responsibilities of each service worker, you can ensure a robust and efficient PWA.

## Session 3 Session 3: Caching Method & Strategy

In 30 secs

- **What is it:** The process of storing resources to improve performance and offline availability.
- **Key Ideas:**
    - Improves load times by serving cached assets.
    - Reduces network requests and bandwidth usage.
    - Supports offline functionality by caching essential resources.
    - Enables different caching strategies for different types of resources.
    - Provides control over cache management and updates.
- **Entry Point:**
    - Implemented within the `service-worker.js` file.
- **Design Decisions:**
    - Identify critical resources to cache for offline use.
    - Choose appropriate caching strategies for different scenarios.
    - Determine cache versioning and invalidation policies.
    - Implement logic to handle cache updates and stale content.
    - Balance between performance improvements and storage limitations.

### Resources:

1. [https://developer.chrome.com/docs/workbox/caching-strategies-overview](https://developer.chrome.com/docs/workbox/caching-strategies-overview) 

2. [https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps/Guides/Caching](https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps/Guides/Caching) 

3. [https://developer.chrome.com/docs/workbox](https://developer.chrome.com/docs/workbox) (Google PWA tool) 

4. [https://www.pwabuilder.com/](https://www.pwabuilder.com/) (Microsoft PWA tool)

1. 

Questions

1. What does GQL Flutter package give us [https://pub.dev/packages/graphql#policies](https://pub.dev/packages/graphql#policies) (Current Flutter App cache)
2. Is there a Flutter package for Service Workers esp. Version control  & Cache busting
3. graphql_flutter: ^5.1.2

---

### Example Caching  Scenario: A Note-Taking App

- **Cache Storage:** Used to store the app shell (HTML, CSS, JS) and static assets (icons, fonts).
- **IndexedDB:** Used to store user notes, which can be indexed and searched.
- **Local Storage:** Used to store simple, user-specific settings like theme preference or last viewed note.

### For Fourdotzero

- **HTTP-Only Cookies with Secure Flag (Limited Use): Auth Token**
- **Server-Side Storage: Refresh Token**
- **Cache Storage:** Used to store the app shell (HTML, CSS, JS) and static assets (icons, fonts).
- **IndexedDB:** Used to store user notes, which can be indexed and searched.
- **Local Storage:** Used to store simple, user-specific settings like theme preference or last viewed note.
- UserName:

---

### Key Questions:

1. What are the different caching strategies (Cache First, Network First, Stale-While-Revalidate, etc.) and when should each be used?
2. How can we ensure the cached data is updated regularly without compromising offline capabilities?
3. What are the best practices for handling large data sets in the cache to avoid storage limitations and performance issues?

### More Information:

### Pre-Read Consultation: Caching Strategies

1. What are the different caching strategies:
    1.  Cache First 
    2. Network First 
    3. Stale-While-Revalidate 
    4. Cache Only
    5. Network Only 

### Caching Strategies Overview:

There are many strategies of caching, as follows

- Cache First (Cache Falling Back to Network)
    
    Description: The service worker first tries to serve the content from the cache. If the content is not available in the cache, it fetches it from the network and then caches it for future requests.
    
    Use Case: Ideal for static assets like images, CSS, and JavaScript files that don't change frequently.
    Example:
    
    ```jsx
    self.addEventListener('fetch', event => {
    event.respondWith(
    caches.match(event.request).then(response => {
    return response || fetch(event.request).then(networkResponse => {
    return caches.open('my-cache').then(cache => {
    cache.put(event.request, networkResponse.clone());
    return networkResponse;
    				});
    			});
    		})
    	);
    });
    ```
    
- Network First (Network Falling Back to Cache)
    
    Description: The service worker tries to fetch the content from the network first. If the network request fails, it serves the content from the cache.
    
    Use Case: Suitable for dynamic content, such as user-generated content or real-time data from APIs.
    Example:
    
    ```jsx
    self.addEventListener('fetch', event => {
    event.respondWith(
    fetch(event.request).then(networkResponse => {
    return caches.open('my-cache').then(cache => {
    cache.put(event.request, networkResponse.clone());
    return networkResponse;
    			});
    		}).catch(() => caches.match(event.request))
    	);
    });
    ```
    
- Stale-While-Revalidate
    
    Description: The service worker serves the content from the cache and simultaneously fetches an updated version from the network. The updated content is then cached for future requests.
    
    Use Case: Useful for content that needs to be fresh but can display cached versions momentarily, such as news articles or blog posts.
    Example:
    
    ```jsx
    self.addEventListener('fetch', event => {
    event.respondWith(
    caches.match(event.request).then(response => {
    const fetchPromise = fetch(event.request).then(networkResponse => {
    return caches.open('my-cache').then(cache => {
    cache.put(event.request, networkResponse.clone());
    return networkResponse;
    					});
    				});
    			return response || fetchPromise;
    		})
    	);
    });
    ```
    
- Cache Only
    
    Description: The service worker only serves content from the cache. If the content is not available in the cache, it returns an error.
    Use Case: Suitable for offline-only resources that should never attempt to go to the network.
    Example:
    
    ```jsx
    self.addEventListener('fetch', event => {
    event.respondWith(
    caches.match(event.request)
    	);
    });
    ```
    

- Network Only
    
    Description: The service worker always fetches the content from the network and does not cache it.
    Use Case: Appropriate for sensitive data that must always be fetched fresh, like login authentication.
    Example:
    
    ```jsx
    self.addEventListener('fetch', event => {
    	event.respondWith(
    	fetch(event.request)
    	);
    });
    ```
    
    Pre-Read Consultation: Updating Cached Data
    

2. How can we ensure the cached data is updated regularly without compromising offline capabilities?

Strategies for Regularly Updating Cached Data:

- Stale-While-Revalidate Strategy:
    
    This strategy is designed to serve cached content while simultaneously fetching updated content in the background. The updated content is then cached for future use.
    Advantage: Users always see content quickly (from the cache) while the app ensures freshness for subsequent visits.
    Periodic Background Sync:
    
    Use the Background Sync API to defer tasks until the user has stable connectivity. This can be used to update cached data periodically.
    Advantage: Reduces the need to update data on every request and improves offline reliability.
    
- Cache Expiration Policies:
    
    Implement cache expiration policies using libraries like Workbox, which can automatically expire and update cached data based on predefined rules (e.g., time-based expiration).
    Advantage: Ensures that stale data is not served indefinitely.
    
- Versioned Caching:
    
    Use versioning for your cached resources. When deploying a new version of your app, update the cache names to ensure old caches are replaced.
    Advantage: Guarantees that users get the latest version of the app after an update.
    

3. What are the best practices for handling large data sets in the cache to avoid storage limitations and performance issues?

Best Practices for Handling Large Data Sets:

- Use IndexedDB for Large Data Sets:
    
    Store large and complex data sets in IndexedDB instead of the Cache API or Local Storage. IndexedDB is designed for handling large amounts of structured data and offers better performance.
    Advantage: Avoids the limitations and performance issues of the Cache API for large data sets.
    
- Selective Caching:
    
    Cache only the most frequently accessed and critical resources. Avoid caching unnecessary or rarely used data.
    Advantage: Reduces storage usage and keeps the cache lean and efficient.
    
- Implement Cache Quotas:
    
    Set quotas and implement policies to clear old or less important data when the cache reaches a certain size.
    Advantage: Prevents the cache from growing indefinitely and consuming too much storage.
    
- Optimize Data Storage:
    
    Compress data before caching it. Use efficient data formats (e.g., JSON over XML) and minify assets (CSS, JavaScript).
    Advantage: Reduces the size of cached data, saving storage space and improving load times.
    
- Background Sync for Non-Critical Data:
    
    Use background sync to fetch and cache non-critical data when the user has a stable connection, rather than caching everything up front.
    Advantage: Balances between fresh content and storage efficiency.
    
    By understanding and applying these caching strategies, your team can ensure that your PWA remains performant, reliable, and user-friendly, even when dealing with large data sets and the need for regular updates.
    

## Session 4 - Local Storage vs IndexedDB (User vs Application Data)

### Resources

1. https://developer.mozilla.org/en-US/docs/Web/API/Window/indexeddb
2. https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage
3. https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Client-side_web_APIs/Client-side_storage
4. https://www.w3schools.com/jsref/prop_win_localstorage.asp
5. https://www.w3schools.com/html/html5_indexeddb.asp

### Local Storage

1. **What is it:** Local Storage is a web storage API that allows developers to store key-value pairs in a web browser with no expiration time.
2. **Key Ideas:**
    - **Persistence:** Data persists even after the browser is closed and reopened.
    - **Capacity:** Typically allows for up to 5-10 MB of storage per origin.
    - **Simplicity:** Easy to use with a straightforward API ( , `localStorage.getItem`).
    - **Synchronous Access:** Data is accessed synchronously, which can block the main thread if not used carefully.
    - **Security:** Not designed for secure storage of sensitive data, as it is accessible via JavaScript.
3. **Entry Point:**
    - Accessed via the `window.localStorage` object in JavaScript.
4. **Design Decisions:**
    - Determine what non-sensitive data should be stored locally for fast access.
    - Ensure data size limits are respected to avoid storage quota errors.
    - Use for storing lightweight data such as user preferences, themes, and small caches.
    - Avoid storing sensitive information due to lack of security.

### IndexedDB

1. **What is it:** IndexedDB is a low-level API for storing large amounts of structured data, including files and blobs, in the browser.
2. **Key Ideas:**
    - **Complex Data Handling:** Supports complex data types and indexing for efficient querying.
    - **Asynchronous Access:** Non-blocking access to data, which improves performance for large datasets.
    - **Transactions:** Ensures data integrity with transactional support.
    - **Storage Capacity:** Can handle much larger amounts of data compared to Local Storage, typically in the range of hundreds of MBs to several GBs.
    - **Security:** Better suited for storing more significant data, but still requires encryption for sensitive information.
3. **Entry Point:** Accessed via the `indexedDB` object in JavaScript.
4. **Design Decisions:**
    - Use for storing large datasets, complex data structures, and offline application data.
    - Implement appropriate indexing to optimize query performance.
    - Handle transactions carefully to ensure data consistency.
    - Encrypt sensitive data before storing it in IndexedDB.

### Cached Storage

1. **What is it:** Cached Storage, also known as the Cache API, is used for storing network request and response pairs in a cache, allowing for efficient offline access and improved performance.
2. **Key Ideas:**
    - **Offline Support:** Enables applications to function offline by serving cached responses when the network is unavailable.
    - **Performance:** Improves load times by caching frequently accessed resources.
    - **Flexible Storage:** Allows caching of different types of responses, including HTML, CSS, JavaScript, and API responses.
    - **Control:** Developers have fine-grained control over what gets cached and how it is managed.
    - **Security:** Ensures that sensitive data is not inadvertently cached and exposed.
3. **Entry Point:**
    - Accessed via the `caches` object in JavaScript, often in conjunction with Service Workers.
4. **Design Decisions:**
    - Determine which resources should be cached for offline access and performance.
    - Implement appropriate caching strategies (e.g., Cache First, Network First) based on application needs.
    - Manage cache storage limits and implement cache invalidation policies to avoid excessive storage use.
    - Ensure sensitive data is not cached or is cached securely with proper access controls.

### 

### Case Study

### Key Questions:

1. What are the key differences between Local Storage and IndexedDB in terms of functionality, performance, and use cases?
2. How should we decide what type of data to store in Local Storage versus IndexedDB, especially for user authentication and application data?
3. What are the best practices for data management, synchronization, and security when using Local Storage and IndexedDB?

| **Data Type** | **Platform** | **Caching Strategy** | **Storage Type** | **Managed By** | **Version Control Method** |
| --- | --- | --- | --- | --- | --- |
| **Application (HTML, CSS & JS)** | iOS/Android | In-Memory Cache | Hive | `graphql_flutter` | App versioning (e.g., `package_info_plus`) |
|  | Chrome Ext |  |  |  |  |
|  | PWA | In-Memory Cache | IndexedDB or LocalStorage | `graphql_flutter` | Service worker with cache versioning |
| **User Config** | iOS/Android | Persistent Cache | Hive | `graphql_flutter` | App versioning (e.g., `package_info_plus`) |
|  | Chrome Ext | Persistent Cache | IndexedDB or LocalStorage | `graphql_flutter` | Service worker with cache versioning |
|  | PWA |  |  |  |  |
| PII |  |  |  |  |  |
| **User Authentication** | iOS/Android | In-Memory Cache | Secure Storage/Hive | `graphql_flutter` | Token expiration/versioned API endpoints |
|  | Chrome Ext/PWA | In-Memory Cache | Secure Storage (Web Crypto API) | `graphql_flutter` | Token expiration/versioned API endpoints |
| Secret key  |  |  |  |  |  |
| Password |  |  |  |  |  |
| Access Token
Id Token 
Refresh Token |  |  |  |  |  |
| **Users Critical Data** | iOS/Android | Persistent Cache | Hive | `graphql_flutter` | App versioning (e.g., `package_info_plus`) |
|  | Chrome Ext/PWA | Persistent Cache | IndexedDB or LocalStorage | `graphql_flutter` | Service worker with cache versioning |
| **Users Non-Critical Data** | iOS/Android | In-Memory + Lazy Load | Hive | `graphql_flutter` | App versioning (e.g., `package_info_plus`) |
|  | Chrome Ext/PWA | In-Memory + Lazy Load | IndexedDB or LocalStorage | `graphql_flutter` | Service worker with cache versioning |

Local Storage vs IndexedDB

1. What are the key differences between Local Storage and IndexedDB in terms of functionality, performance, and use cases?

Local Storage:

Functionality:
API: Simple key-value storage.
Synchronous: Operates synchronously, blocking the main thread.
Data Type: Stores data as strings.
Max Storage: Typically around 5-10 MB per origin.
Performance:

Synchronous Operations: Can block the main thread, potentially causing performance issues for large data sets.
Speed: Fast for small amounts of data.
Use Cases:

Simple Data: Suitable for storing simple, lightweight data such as user preferences, settings, or small caches.
Quick Reads: Ideal for data that needs to be read quickly and frequently.

IndexedDB:

Functionality:
API: Powerful NoSQL database.
Asynchronous: Operates asynchronously, not blocking the main thread.
Data Type: Supports complex data types including objects, arrays, and binary data.
Max Storage: Can handle larger storage capacities, typically in the range of hundreds of MBs to several GBs, depending on the browser and device.
Performance:

Asynchronous Operations: Does not block the main thread, suitable for large datasets.
Efficiency: Designed for high-performance read/write operations on large datasets.
Use Cases:

Complex Data: Suitable for storing complex, large-scale data such as user-generated content, application state, or offline data.
Transactions: Supports transactions, making it ideal for applications requiring complex data manipulation and integrity.

2. How should we decide what type of data to store in Local Storage versus IndexedDB, especially for user authentication and application data?

Local Storage:

User Preferences and Settings: 

Store simple data that users may frequently access and change, such as theme preferences or last visited pages.
Small Caches: Store small, non-sensitive caches of data that can improve user experience.
Session Data: Store data that needs to persist only for the duration of a session.

IndexedDB:

User Authentication Data:
Tokens and Credentials: Store authentication tokens and other credentials securely, especially if they are complex or need to be manipulated.

Encrypted Data: 

Use IndexedDB to store sensitive data in an encrypted format to ensure security.

Application Data:
Large Datasets: Store application data that is complex or large, such as transaction records, user profiles, or offline data.
State Management: Use IndexedDB for managing application state that needs to persist across sessions and network connectivity changes.

3. What are the best practices for data management, synchronization, and security when using Local Storage and IndexedDB?
Best Practices for Local Storage:

Limited Use:
Use Local Storage for non-sensitive, simple data that is accessed frequently and does not require complex querying or manipulation.

Size Management:

Be mindful of storage limits (5-10 MB). Regularly clear unused data to avoid exceeding the limit.
Security:

Do not store sensitive information (e.g., passwords, personal data) in Local Storage as it is not secure.
Consider encrypting data if it must be stored in Local Storage, although this is not recommended for highly sensitive data.

### Best Practices for IndexedDB:
-

- Complex Data Management:
Use IndexedDB for complex and large datasets that require indexing and transactional capabilities.
Leverage the asynchronous nature of IndexedDB to avoid blocking the main thread during data operations.
- Data Synchronization:
Implement background synchronization using service workers to keep the IndexedDB data in sync with the server.
Handle conflicts and merge changes appropriately to ensure data integrity.
Security:

Store sensitive data securely by encrypting it before storing it in IndexedDB.
Use robust encryption libraries (e.g., CryptoJS) to ensure data is protected.
Regularly audit and clean up data to avoid storing unnecessary sensitive information.
- Performance Optimization:
Use indices to optimize query performance for large datasets.
Batch read/write operations to minimize the number of transactions and improve efficiency.
- Max Limits:
Local Storage:

Per Origin: Typically 5-10 MB.
Data Size per Key: Maximum size per key is about 5 MB.
IndexedDB:

Per Origin: Typically hundreds of MBs to several GBs, depending on the browser and device.
Data Size per Entry: Can store large objects, arrays, and binary data without a strict size limit per entry, as long as the overall storage quota is not exceeded.

### Summary

Local Storage vs IndexedDB:

Local Storage is best for simple, small, non-sensitive data that requires quick read/write access and is suitable for user preferences, settings, and small caches.
IndexedDB is designed for complex, large-scale data, supporting asynchronous operations, transactions, and high-performance querying, making it suitable for user authentication data, application state, and large datasets.

Best Practices:

Use Local Storage sparingly and avoid storing sensitive data. Ensure data size limits are respected.

Use IndexedDB for large and complex datasets, ensure data security through encryption, and optimize performance with indexing and batched operations.

By adhering to these practices, you can efficiently manage, synchronize, and secure data in your web applications.

## Session 5  Inter-Tab Communication Between a Website and a PWA

Summary of Cross-Origin Communication Between a Website and a PWA
Objective: To facilitate real-time, secure communication between a website (Tab A) and a PWA (Tab B) from different origins. The goal is for the website to send messages to the PWA, such as instructions to open specific pages or perform certain actions.

### ChatGPT Session: https://chatgpt.com/share/365af24e-6d0a-410b-b68c-22ee4db82651(see end of chat for  postmessage method, see start for discussion on Content Security Policy and middle for some noddy questions on Sign in)

Summary of Origin, CORS, and XSS

1. Origin

Definition: The origin of a web page is defined by its `scheme (protocol)`, `host (domain)`, and `port`. For example, the origin of 

https://example.com:8080 is equal https://example.com:8080

Not equal port = https://example.com:8081

Not equal protcol = http://example.com:8080 

Not equal hostname = https://ample.com:8080 

Same-Origin Policy: A security feature that restricts how scripts on one origin can interact with resources on another origin.

2. CORS (Cross-Origin Resource Sharing)

Definition: A mechanism that allows a web application running at one origin to request resources from a different origin.

Headers Used:

- Access-Control-Allow-Origin: Specifies which origins can access the resource.
- Access-Control-Allow-Methods: Specifies the methods (e.g., GET, POST) allowed when accessing the resource.
- Access-Control-Allow-Headers: Specifies the headers that can be used when making the actual request.

Purpose: To enable secure cross-origin requests and data sharing between different origins.

3. XSS (Cross-Site Scripting)
Definition: A security vulnerability where an attacker injects malicious scripts into content from otherwise trusted websites.

Types:

- Stored XSS: The malicious script is stored on the target server (e.g., in a database).
- Reflected XSS: The malicious script is reflected off a web server, such as in an error message or search result.
- DOM-Based XSS: The vulnerability exists in client-side code rather than server-side.
- Security Vectors and Protection Strategies

Origin Security Vectors:

Same-Origin Policy: Ensures that scripts from one origin cannot access resources from another origin without permission.
Protection: Browsers enforce this policy to prevent malicious scripts from accessing sensitive data on different origins.

CORS Security Vectors:

Validating Requests: Ensures that only allowed origins can access resources.
Protection: Use CORS headers to specify which origins, methods, and headers are permitted.
Example: Access-Control-Allow-Origin: https://trusted-site.com
Preflight Requests: Browsers send an OPTIONS request before the actual request to check permissions.

XSS Security Vectors:

Input Validation: Ensure that all user inputs are validated and sanitized.
Output Encoding: Encode outputs to prevent execution of malicious scripts.
Content Security Policy (CSP): A security header that helps prevent XSS by specifying allowed content sources.
Example: Content-Security-Policy: default-src 'self'; script-src 'self' https://trusted-cdn.com
Protection: These measures prevent attackers from injecting and executing malicious scripts in the context of a trusted website.

### Summary

Origin: Refers to the protocol, host, and port of a URL, with the same-origin policy restricting interactions between different origins.

CORS: Allows controlled access to resources from different origins using specific headers to ensure security.

XSS: A vulnerability where attackers inject malicious scripts, mitigated through input validation, output encoding, and CSP.

## Key Methods for Inter Tab Messaging:

### Option A - PostMessage API:

Description: Allows secure cross-origin communication between different windows or iframes by sending messages that can be received by a listener in the target window or iframe.
Use Case: Ideal for direct communication between tabs or iframes under different origins.
Implementation:

Website (Tab A): [https://developer.mozilla.org/en-US/docs/Web/API/ServiceWorker/postMessage](https://developer.mozilla.org/en-US/docs/Web/API/ServiceWorker/postMessage)

```jsx
// Function to send a message to the PWA
function sendMessageToPWA(message) {
  const targetOrigin = 'https://your-pwa-domain.com';
  window.postMessage(message, targetOrigin);
}

// Example: Notify PWA to open the payment app
sendMessageToPWA({ action: 'open_payment_app' });
```

PWA (Tab B):

```jsx

// Listen for messages from other origins
window.addEventListener('message', (event) => {
  // Verify the origin of the message
  if (event.origin === 'https://your-website-domain.com') {
    const message = event.data;
    if (message.action === 'open_payment_app') {
      // Logic to open the payment app or perform any action
      console.log('Received message to open payment app');
      // Perform necessary actions, e.g., opening a specific page in the PWA
      window.location.href = '/payment.html';
    }
  }
});
```

### Option B - Using a Backend Server as an Intermediary (WebSockets):

Description: Involves setting up a backend server to facilitate communication between different origins using WebSockets, allowing real-time, bidirectional communication between clients.
Use Case: Suitable for more complex scenarios where direct communication is not feasible, and real-time updates are required.
Implementation:
Backend Server (Node.js with WebSocket):

```jsx
const WebSocket = require('ws');

const wss = new WebSocket.Server({ port: 8080 });

wss.on('connection', (ws) => {
  ws.on('message', (message) => {
    // Broadcast message to all connected clients
    wss.clients.forEach((client) => {
      if (client !== ws && client.readyState === WebSocket.OPEN) {
        client.send(message);
      }
    });
  });
});
```

Website (Tab A):

```jsx
const ws = new WebSocket('ws://your-backend-server.com:8080');

// Function to send a message to the PWA
function sendMessageToPWA(message) {
  ws.send(JSON.stringify(message));
}

// Example: Notify PWA to open the payment app
sendMessageToPWA({ action: 'open_payment_app' });

// Listen for messages from the server (if needed)
ws.onmessage = (event) => {
  const message = JSON.parse(event.data);
  console.log('Message from server:', message);
};
```

PWA (Tab B):

```jsx
const ws = new WebSocket('ws://your-backend-server.com:8080');

// Listen for messages from the server
ws.onmessage = (event) => {
  const message = JSON.parse(event.data);
  if (message.action === 'open_payment_app') {
    // Logic to open the payment app or perform any action
    console.log('Received message to open payment app');
    // Perform necessary actions, e.g., opening a specific page in the PWA
    window.location.href = '/payment.html';
  }
};

// Example of sending a message back (if needed)
function sendMessageBack(message) {
  ws.send(JSON.stringify(message));
}
```

### Security Implications and Mitigations

- Security Implications:
Man-in-the-Middle Attacks: Communication can be intercepted by unauthorized parties.
Cross-Site Scripting (XSS): Messages could potentially be used to inject malicious scripts.
Unauthorized Access: Ensuring only authorized clients can send and receive messages is critical.
- Mitigation Strategies:
    1. Use Secure WebSockets (WSS): Use wss:// instead of ws:// to ensure encrypted communication.
    2. Message Validation: Validate the structure and content of messages before processing them.
    3. Origin Checks and Authentication: Implement authentication to ensure only authorized clients can connect and send messages.
    4. Content Security Policy (CSP): Implement a strong CSP to mitigate XSS attacks.
    Optimizing Event Messaging
    
- Optimisation Strategies:
    1. Efficient Data Handling: Minimize the amount of data sent in messages to reduce processing time and latency.
    2. WebSocket Connection Management: Maintain persistent WebSocket connections to avoid the overhead of establishing new connections repeatedly.
    3. Asynchronous Processing: Handle messages asynchronously to avoid blocking the main thread.
    4. Performance Monitoring: Monitor the performance of your messaging system and optimize as needed.

### Summary

To achieve cross-origin communication between a website and a PWA, the PostMessage API and WebSocket-based backend server are effective methods. The PostMessage API allows direct communication between tabs or iframes under different origins, while a backend server using WebSockets facilitates real-time, bidirectional communication. Ensuring secure communication involves using secure connections, validating messages, checking origins, and implementing strong CSPs. Optimizing messaging for real-time responsiveness requires efficient data handling, maintaining persistent connections, asynchronous processing, and performance monitoring. These strategies enable secure, reliable, and efficient communication between your website and PWA.

## Session 6 - Push Notifications

### Resources:

1. https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps/Tutorials/js13kGames/Re-engageable_Notifications_Push
2. https://developer.mozilla.org/en-US/docs/Web/API/Notifications_API
3. https://developer.mozilla.org/en-US/docs/Web/API/Push_API
4. https://docs.pwabuilder.com/#/home/native-features?id=push-notifications

[https://firebase.google.com/docs/cloud-messaging/flutter/client#web](https://firebase.google.com/docs/cloud-messaging/flutter/client#web)
[https://firebase.google.com/docs/cloud-messaging/js/client#web](https://firebase.google.com/docs/cloud-messaging/js/client#web)

Guide for implementing push notifications on a PWA via FCM:

[https://medium.com/@kartik_nema/push-notifications-using-firebase-cloud-messaging-90638eae4abf](https://medium.com/@kartik_nema/push-notifications-using-firebase-cloud-messaging-90638eae4abf)

The Notification API is used to send the notification. Configurable options are detailed here:

[https://developer.mozilla.org/en-US/docs/Web/API/Notification/Notification](https://developer.mozilla.org/en-US/docs/Web/API/Notification/Notification)

### Key Questions:

1. How do we implement push notifications in a PWA, including user permission handling?
2. What are the security considerations for sending and receiving push notifications, especially for sensitive payment data?
3. How can we ensure that push notifications are delivered reliably and timely across different devices and platforms?

### In 30 Secs

- **What is it:**
    - A technology that allows web applications to send messages to a user's device, even when the app is not actively in use.
- **Key Ideas:**
    - **User Engagement:** Keeps users engaged by delivering timely updates and information.
    - **Permission-Based:** Users must grant permission to receive notifications.
    - **Background Service:** Uses service workers to handle notifications in the background.
    - **Cross-Platform:** Works on various devices and operating systems.
    - **Rich Media Support:** Can include text, images, and action buttons.
- **Entry Point:**
    - Implemented using the Push API and Service Workers, starting with user permission request: `Notification.requestPermission()`.
- **Design Decisions:**
    - Determine the types of notifications to send (e.g., updates, reminders).
    - Ensure the content is relevant and valuable to the user.
    - Use clear and concise messaging with appropriate media.
    - Implement best practices for user privacy and data protection.
    - Consider frequency and timing to avoid notification fatigue.
- **Use Case:**
    - **Example:** A news website sends breaking news alerts to users, or an e-commerce site notifies users about order updates and special offers, keeping users informed and engaged.

### Push Notifications in PWAs: Detailed Overview

1. How do we implement push notifications in a PWA, including user permission handling?

- Implementation Steps:
Register a Service Worker:

Push notifications in PWAs require a service worker to handle incoming push messages and display notifications.

- Request User Permission:

Ask the user for permission to send notifications. This is a user-friendly way to ensure they are aware and agreeable to receiving notifications.
- Subscribe to Push Service:

Once permission is granted, subscribe the user to a push service. This involves generating a subscription object that contains the endpoint and keys required to send notifications.
Send Push Notification:

Use a push service (like Firebase Cloud Messaging) to send push messages to the subscribed clients.

- Handle Push Events:

In the service worker, handle the push event to display notifications.
Code Example:

```jsx
/ Register the service worker
if ('serviceWorker' in navigator && 'PushManager' in window) {
  navigator.serviceWorker.register('/service-worker.js').then(swReg => {
    console.log('Service Worker Registered', swReg);
    // Request permission for notifications
    askPermission().then(permissionResult => {
      if (permissionResult === 'granted') {
        // Subscribe to the Push Service
        subscribeUserToPush(swReg);
      }
    });
  }).catch(error => {
    console.error('Service Worker Error', error);
  });
}

// Function to request notification permission
function askPermission() {
  return new Promise((resolve, reject) => {
    const permissionResult = Notification.requestPermission(result => {
      resolve(result);
    });

    if (permissionResult) {
      permissionResult.then(resolve, reject);
    }
  });
}

// Function to subscribe user to push service
function subscribeUserToPush(swReg) {
  const applicationServerKey = urlB64ToUint8Array('YOUR_PUBLIC_VAPID_KEY');
  swReg.pushManager.subscribe({
    userVisibleOnly: true,
    applicationServerKey: applicationServerKey
  }).then(subscription => {
    console.log('User is subscribed:', subscription);
    // Send subscription to server
    sendSubscriptionToServer(subscription);
  }).catch(error => {
    console.error('Failed to subscribe user:', error);
  });
}

// Function to convert URL-safe base64 string to Uint8Array
function urlB64ToUint8Array(base64String) {
  const padding = '='.repeat((4 - base64String.length % 4) % 4);
  const base64 = (base64String + padding)
    .replace(/-/g, '+')
    .replace(/_/g, '/');
  const rawData = window.atob(base64);
  const outputArray = new Uint8Array(rawData.length);
  for (let i = 0; i < rawData.length; ++i) {
    outputArray[i] = rawData.charCodeAt(i);
  }
  return outputArray;
}

// Service Worker: service-worker.js
self.addEventListener('push', event => {
  const data = event.data ? event.data.json() : {};
  const title = data.title || 'Default Title';
  const options = {
    body: data.body || 'Default message body',
    icon: '/images/icon-192x192.png',
    badge: '/images/icon-192x192.png'
  };
  event.waitUntil(
    self.registration.showNotification(title, options)
  );
});
```

2. What are the security considerations for sending and receiving push notifications, especially for sensitive payment data?

Security Considerations:

- Secure Communication:
Ensure all communication between the client and the server is over HTTPS to prevent man-in-the-middle attacks.

- VAPID Keys:
Use VAPID (Voluntary Application Server Identification for Web Push) keys for authenticated communication between your application server and the push service.
This adds a layer of trust and ensures that only authorised servers can send push messages.
- Encryption:
Encrypt the payload of the push message to ensure that even if intercepted, the content cannot be read.
Use libraries like web-push for Node.js to handle encryption and sending of push messages.
- Permission Handling:
Clearly explain why you are requesting notification permissions and what kind of notifications the user will receive.
Respect user preferences and provide an easy way for them to opt-out of notifications.
- Sensitive Data Handling:
Avoid sending sensitive payment data in push notifications. Instead, notify the user about the event and direct them to a secure part of the application to view the details.
Example: "You have a new transaction. Open the app to see details."

3. How can we ensure that push notifications are delivered reliably and timely across different devices and platforms?

Ensuring Reliable and Timely Delivery:

- Use a Reliable Push Service:
Services like Firebase Cloud Messaging (FCM) are designed to handle push notifications at scale and across multiple platforms (iOS, Android, web).
- Monitor Delivery Status:
Monitor the delivery status of your push messages using the push service's APIs. This helps identify issues with delivery and take corrective actions.
Retry Mechanism:

Implement a retry mechanism for push notifications that fail to send. Ensure your application can handle temporary network issues or server downtime.
- Optimize Payload Size:
Keep the payload size of push messages small to avoid issues with delivery. Different platforms may have different size limits.
- User Preferences:
Allow users to customize their notification preferences (e.g., frequency, types of notifications). This ensures they receive notifications they care about and are less likely to disable them.
- Test Across Devices:
Regularly test your push notification implementation across different devices and platforms to ensure consistent behavior and delivery.
Example Using Firebase Cloud Messaging (FCM)

Server-side Example (Node.js using web-push):

```jsx
const webPush = require('web-push');

// Generate VAPID keys (run once and store them securely)
const vapidKeys = webPush.generateVAPIDKeys();
console.log(vapidKeys);

// Set VAPID details
webPush.setVAPIDDetails(
  'mailto:example@yourdomain.org',
  'YOUR_PUBLIC_VAPID_KEY',
  'YOUR_PRIVATE_VAPID_KEY'
);

// Function to send a push notification
function sendPushNotification(subscription, data) {
  webPush.sendNotification(subscription, JSON.stringify(data))
    .then(response => {
      console.log('Push sent successfully:', response);
    })
    .catch(error => {
      console.error('Push send error:', error);
    });
}

// Example subscription object and data
const subscription = {
  endpoint: 'https://fcm.googleapis.com/fcm/send/abcd...',
  keys: {
    p256dh: '...',
    auth: '...'
  }
};
const data = {
  title: 'New Payment',
  body: 'You have received a new payment. Check your app for details.'
};

sendPushNotification(subscription, data);
```

Summary
Implementing push notifications in a PWA involves registering a service worker, requesting user permission, subscribing to a push service, and handling push events in the service worker. Security considerations include using HTTPS, VAPID keys, encrypting payloads, and carefully handling sensitive data. Ensuring reliable and timely delivery of push notifications requires using a reliable push service, monitoring delivery status, implementing retry mechanisms, optimizing payload size, respecting user preferences, and regular testing across devices.

### Open Questions

### Questions:

1. Get clearer on encryption needs and the way of best practise for PWA (Richard Bell)
    1. Where should we store
        1. Auth Token
        2. Id Token
        3. Refresh Token
        4. Password
        5. Secret Key 
2. Where is (Outlook / Gmail) storing data @Anton Muntianu 

See here for Anton’s Wallet Deep Dive which includes storage and encryption implementation:

[Fourdotzero Wallet Deep Dive](https://www.notion.so/Fourdotzero-Wallet-Deep-Dive-e0ef0b25cdca431fa4bfd53844968f7f?pvs=21)

## Session 7 - Combined Session: Data Encryption, User Authentication, Communication Security, and Data Storage Overview

### Resources:

1. https://www.zaproxy.org/
2. https://www.owasp.org/

1. [https://developer.mozilla.org/en-US/docs/Learn/Server-side/First_steps/Website_security](https://developer.mozilla.org/en-US/docs/Learn/Server-side/First_steps/Website_security)
2. [crypto-js library](https://www.npmjs.com/package/crypto-js)
3. https://developer.mozilla.org/en-US/docs/Web/API/Web_Crypto_API
4. https://www.crypto101.io/

More on Storage:

1. [https://web.dev/articles/storage-for-the-web](https://web.dev/articles/storage-for-the-web)
2. [https://web.dev/articles/same-site-same-origin](https://web.dev/articles/same-site-same-origin) **`Sec-Fetch-Site`**
3. `storage.local` vs `chrome.storage.local` see https://chatgpt.com/share/f0008924-e9a6-4110-8caa-1ebb62f81098

### Top areas:

1. Use HTTPS and TLS everywhere
2. Use MFA and User granular control
3. Zeroth Key issue for Web / IndexDB Storage

Encrypt our storage - secret_key

Secret_Key  - server  side rotate the key (store old key)

PWA - offline

Server-side key →  Master_Key

Master_Key →Severside side

Derive Key from Username 

1. Web Attack Vectors [ChatGPT Session: https://chatgpt.com/share/365af24e-6d0a-410b-b68c-22ee4db82651(see end of chat for  postmessage method, see start for discussion on Content Security Policy and middle for some noddy questions on Sign in)

Summary of Origin, CORS, and XSS](https://www.notion.so/ChatGPT-Session-see-end-of-chat-for-postmessage-method-see-start-for-discussion-on-Content-Secur-a18dde6db7a94a0e91c37c7825616e3b?pvs=21) 

| Risk Name | Description | Mitigation | Implementation File/Code |
| --- | --- | --- | --- |
| CSRF (Cross-Site Request Forgery) | An attacker tricks a user's browser into performing unauthorised actions on a trusted website where the user is already logged in (e.g., transferring money).

Simple description:
Eve embeds a request to a third-party website that sends Alice a naughty request to pay her instead of Bob, which is what Alice wants to do. 

Mitigation: Ensure requests are not forged by using a token.
 | * Use CSRF tokens: Generate a unique token per user session and include it in all forms. Validate the token on the server before processing the request.  

* Implement SameSite cookie attribute: Set the SameSite attribute of cookies to Strict or Lax (depending on requirements) to prevent them from being sent in cross-site requests. | * Server-side code (e.g., PHP, Python): Implement logic to validate CSRF tokens.  * HTML forms: Include hidden fields containing the CSRF token.

Simple implementation of CSRF with AWS API Gateway: https://chatgpt.com/share/89a4de77-49d6-4cb4-985b-1c26b5884bf4 |
| XSS (Cross-Site Scripting) | An attacker injects malicious scripts into a website, which then execute in the victim's browser when they visit the website. These scripts can steal data, redirect users, or deface the website.

Simple description:
Eve give Alice some naughty code, perhaps in an e-mail /  website or other non FDZ service that runs a service against FDZ without Alice knowing. 

Mitigation: Ensure you only process scripts from white listed locations.
 | * Input validation and sanitisation: Validate and sanitise all user input (e.g., form data, URL parameters) to remove potentially harmful code.  * Use Content Security Policy (CSP): Define allowed sources for scripts, styles, images, etc. This restricts the browser from executing unauthorised code. | * Server-side code: Validate and sanitise user input before processing.  

* Client-side code (optional): Consider input validation libraries for additional protection. |
| SQL Injection (SQLi) | An attacker injects malicious SQL code into user input, which is then used to manipulate the database. This can allow attackers to steal data, modify database content, or even gain control of the database server.

Simple description:
Eve manages to give Alice some naughty SQL code that she submits to dbase without her knowing. The SQL then runs giving Eve some private information. 

Mitigation: Check all code written to db to sanitise again this. | * Use prepared statements with parameter binding: Separate SQL code from user input to prevent malicious code from being interpreted as part of the SQL query. * Validate user input to prevent unexpected data types or commands. | * Server-side code (e.g., PHP, Python): Implement prepared statements and parameter binding for database interactions. |
| CORS (Cross-Origin Resource Sharing) | A security mechanism that restricts how a web page from one origin (website) can request resources from another origin.  This prevents unauthorized access to data. (Not a vulnerability, but a security feature with configuration options)

Simple description:

https://sambell.com:80 can read [http://johndoe.co.uk:22](http://johndoe.co.uk:22) if CORS enabled, if not it cannot.  | * Configure CORS headers on the server: Specify which origins are allowed to access resources using appropriate CORS headers (e.g., Access-Control-Allow-Origin). | * Server-side code: Implement logic to set CORS headers based on requirements. |
| CSP (Content Security Policy) | A security policy that restricts the sources from which a web page can load scripts, styles, images, and other resources. This helps mitigate XSS attacks and other vulnerabilities.

 | * Implement CSP using a Content-Security-Policy header: Define allowed sources for resources using directives like script-src, style-src, etc. | * Server-side code: Configure web server to send the Content-Security-Policy header with appropriate directives. |

CSRF

![Untitled](PWA%20-%20Progressive%20Web%20App%2060978aa575fe498da4236cda6a32985a/Untitled.png)

Origin

[https://web.dev/articles/same-site-same-origin](https://web.dev/articles/same-site-same-origin)

![Untitled](PWA%20-%20Progressive%20Web%20App%2060978aa575fe498da4236cda6a32985a/Untitled%201.png)

Chrome Ext local storage. Open inspector and enter into console:

```jsx
chrome.storage.local.get(null, function(result) {
    console.log(result);
});

```

### Topics included:

1. Data Encryption
2. User Authentication
3. Communication Encryption
4. Local Data Storage vs IndexedDB Storage
5. Libraries and Tools for Inspection and Testing

| **Data Type** | **Platform** | **Caching Strategy** | **Storage Type** | **Managed By** | **Version Control Method** |
| --- | --- | --- | --- | --- | --- |
| **Application (HTML, CSS & JS)** | PWA | In-Memory Cache | IndexedDB or LocalStorage | `graphql_flutter` | Service worker with cache versioning |
| **User Config** | PWA | Persistent Cache | IndexedDB or LocalStorage | `graphql_flutter` | Service worker with cache versioning |
| Secret key  | PWA |  |  | `graphql_flutter` |  |
| Password | PWA |  |  | `graphql_flutter` |  |
| Access Token
Id Token 
Refresh Token | PWA |  |  | `graphql_flutter` |  |
| **Users Critical Data** | PWA | Persistent Cache | IndexedDB or LocalStorage | `graphql_flutter` | Service worker with cache versioning |
| **Users Non-Critical Data** | PWA | In-Memory + Lazy Load | IndexedDB or LocalStorage | `graphql_flutter` | Service worker with cache versioning |

### Data Encryption

Key Questions:
1. How can we implement encryption for data stored in Local Storage and IndexedDB?
2. What are the best practices for key management and ensuring secure encryption and decryption processes?
3. How can we use Chrome DevTools to inspect and debug encrypted data and ensure it remains secure?

### Implementation: Summary of Secure Data Handling Workflow

Workflow Steps:
1. Secure Data Transmission:
Transport Layer Security: Data sent from the middleware to the browser is encrypted using HTTPS, ensuring secure transmission over the network.

2. Key Retrieval:
Authentication: The user must be authenticated (e.g., via OAuth2 or JWT) to access the secret key.
Secure Key Delivery: Upon successful authentication, the secret key is retrieved from AWS Secrets Manager by the middleware and sent to the client over the HTTPS connection.

3. Key Storage:
Ephemeral Storage: The secret key is stored temporarily in the browser's memory (e.g., in a JavaScript variable or session storage) and not persisted to local storage or IndexedDB.
Session Management: The secret key is cleared from memory at the end of each session, requiring re-authentication to obtain a new key for subsequent sessions.

4. Data Encryption and Storage:
Encrypt Data: Sensitive data received by the browser is encrypted using the secret key before being stored in IndexedDB.
IndexedDB Storage: The encrypted data is then stored securely in IndexedDB.

5. Data Decryption:
Retrieve Encrypted Data: When accessing stored data, the encrypted data is retrieved from IndexedDB.
Decrypt Data: The secret key stored in memory is used to decrypt the data, making it accessible in plaintext.
End of Session:

6. Clear Key from Memory: 

### At the end of the user session, the secret key is cleared from memory to ensure it is not accessible.

Best Practices:

- Authentication and Authorisation:
Ensure robust authentication mechanisms (e.g., OAuth2) are in place to protect access to the secret key.
Use fine-grained authorization controls to restrict key access to authenticated users only.

- Secure Storage Solutions:
Utilize secure storage services like AWS Secrets Manager for key management.
Ensure the secret key is never hardcoded in the client-side code.

- HTTPS Encryption:
Always use HTTPS to encrypt data in transit between the middleware and the client.
Implement HSTS (HTTP Strict Transport Security) to enforce HTTPS connections.

- Ephemeral Key Storage:
Store the secret key in memory or session storage, and clear it after each session.
Avoid persisting the secret key in local storage or other long-term storage mechanisms.

- Encryption Standards:
Use strong encryption algorithms (e.g., AES-GCM) for encrypting data.
Regularly update and rotate encryption keys to maintain security.

```jsx

// Example using CryptoJS for encryption
const CryptoJS = require('crypto-js');

// Encrypt data
function encryptData(data, key) {
  return CryptoJS.AES.encrypt(data, key).toString();
}

// Decrypt data
function decryptData(ciphertext, key) {
  const bytes = CryptoJS.AES.decrypt(ciphertext, key);
  return bytes.toString(CryptoJS.enc.Utf8);
}

// Example usage
const data = 'Sensitive Data';
const key = 'secret-key';
const encryptedData = encryptData(data, key);
const decryptedData = decryptData(encryptedData, key);
```
```

Best Practices:

1. Use strong, unique keys for encryption.
2. Store keys securely and avoid hardcoding them in the source code.
3. Regularly rotate encryption keys and update encrypted data accordingly.

Chrome DevTools:

### 1. Inspect and debug encrypted data using the Application tab.
2. Use the Console to test encryption and decryption functions.

User Authentication

Key Questions:

1. How should we manage user authentication data securely?
2. What are the best practices for handling authentication tokens and session management?

Content:

1. Use secure authentication methods like OAuth2.
2. Store tokens securely using IndexedDB or Local Storage with encryption.
3. Implement secure session management practices.

### Communication Encryption

Key Questions:

1. How can we ensure secure communication between the PWA and backend services, especially for sensitive payment data?
2. What tools and practices should we use for continuous security monitoring and testing?

Content:
1. Use HTTPS to encrypt data in transit.
2. Implement TLS/SSL for secure communication.
3. Use security headers (CSP, HSTS) to enhance security.
4. Continuous security monitoring using tools like OWASP ZAP.

### Local Data Storage vs IndexedDB Storage

Key Questions:
1. What are the key differences between Local Storage and IndexedDB in terms of security?
2. How should we decide what type of data to store in Local Storage versus IndexedDB, especially for user authentication and application data?
3. What are the best practices for data management, synchronization, and security when using Local Storage and IndexedDB?

Local Storage:
1. Synchronous storage, suitable for simple key-value pairs.
2. Not designed for secure storage of sensitive data.
3. Easily accessible via browser developer tools.

IndexedDB:
1. Asynchronous, more complex storage for large datasets.
2. Supports transactions and complex queries.
3. More secure for storing sensitive data with encryption.

Security Considerations:

Local Storage:
1. Avoid storing sensitive data directly.
2. Use encryption if necessary but prefer IndexedDB for better security.

IndexedDB:
1. Use for storing authentication tokens and sensitive application data.
2. Ensure data is encrypted before storage.
3. Implement robust access controls and secure keys.

Best Practices:
1. Regularly audit stored data to ensure no sensitive data is exposed.
2. Use encryption libraries to securely store and retrieve data.
3. Synchronize data securely between client and server, handling conflicts and ensuring data integrity.

### Libraries:

1. CryptoJS: For encryption and decryption.
2. Web Crypto API: For native browser cryptography.

### Tools:

1. Chrome DevTools: Inspect storage, network, and console.
2. Application tab for inspecting Local Storage and IndexedDB.
3. Network tab for monitoring HTTPS/TLS connections.
4. OWASP ZAP: Automated security testing for web applications.
5. Lighthouse: Audits for performance, accessibility, and security.

---

### Most Secure Approach Using IndexedDB with Web Crypto API

1. **Derive the Key**:
    - Use PBKDF2 to securely derive a key from the user's password.
2. **Encrypt the Data**:
    - Encrypt data using the derived key with AES-GCM before storing it.
3. **Initialization Vector (IV)**:
    - AES-GCM requires a unique IV for each encryption operation to ensure security.
    - Generate a random IV and store it alongside the encrypted data.
4. **Store Encrypted Data and IV**:
    - Store both the encrypted data and its IV in IndexedDB.

### Example Workflow

1. **Derive the Key**:
    
    ```jsx
    javascriptCopy code
    async function deriveKey(password) {
        const enc = new TextEncoder();
        const keyMaterial = await crypto.subtle.importKey(
            "raw",
            enc.encode(password),
            { name: "PBKDF2" },
            false,
            ["deriveKey"]
        );
        return crypto.subtle.deriveKey(
            {
                name: "PBKDF2",
                salt: enc.encode("your-salt"),
                iterations: 100000,
                hash: "SHA-256"
            },
            keyMaterial,
            { name: "AES-GCM", length: 256 },
            false,
            ["encrypt", "decrypt"]
        );
    }
    
    ```
    
2. **Encrypt the Data**:
    
    ```jsx
    javascriptCopy code
    async function encryptData(data, password) {
        const key = await deriveKey(password);
        const iv = crypto.getRandomValues(new Uint8Array(12)); // Generate random IV
        const enc = new TextEncoder();
        const encryptedData = await crypto.subtle.encrypt(
            { name: "AES-GCM", iv: iv },
            key,
            enc.encode(data)
        );
        return { iv, encryptedData };
    }
    
    ```
    
3. **Store Encrypted Data and IV in IndexedDB**:
    
    ```jsx
    javascriptCopy code
    async function storeEncryptedData(encryptedData, iv) {
        const dbRequest = indexedDB.open("SecureDB", 1);
        dbRequest.onupgradeneeded = function(event) {
            const db = event.target.result;
            db.createObjectStore("data", { keyPath: "id" });
        };
        dbRequest.onsuccess = function(event) {
            const db = event.target.result;
            const transaction = db.transaction("data", "readwrite");
            const store = transaction.objectStore("data");
            store.put({ id: "encryptedData", data: encryptedData, iv: iv });
        };
    }
    
    ```
    

### Summary

- **Key Derivation**: Use PBKDF2 to derive a key from the user's password.
- **Encryption**: Encrypt data with AES-GCM using the derived key.
- **IV**: Generate a random IV for each encryption, store it with the encrypted data.
- **Storage**: Store encrypted data and IV in IndexedDB.