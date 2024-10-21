# Vue.js Project with Nuxt.js, Vite, and AWS Integration
This project is a Vue.js application template built with Nuxt.js and Vite, utilizing AWS services for backend functionality.

## Table of Contents
1. [Introduction](#introduction)
2. [Features](#features)
3. [Prerequisites](#prerequisites)
4. [Installation](#installation)
5. [Project Structure](#project-structure)
6. [Configuration](#configuration)
7. [Authentication](#authentication)
8. [Notification Utility](#notification-utility)
9. [Data Management GQL/API](#data-management)
10. [User Interface](#user-interface)
11. [Streaming and Caching](#streaming-and-caching-capabilities)
12. [State Management](#state-management)
13. [Testing](#testing)
14. [Deployment](#deployment)
15. [Offline Capabilities](#offline-capabilities)
16. [Internationalization](#internationalization)
17. [Performance Optimization](#performance-optimization)
18. [Accessibility](#accessibility)
19. [Contributing](#contributing)
20. [License](#license)

## Introduction

This project is a Vue.js application template built with Nuxt.js and Vite, utilizing AWS services for backend functionality. It's set up as a Progressive Web App (PWA) with GraphQL and OpenAPI integration, designed for high performance and offline capabilities.

#### Documentation 
1. [Vue Docs](https://vuejs.org/guide/introduction.html)
2. [Nuxt](https://nuxt.com/)
3. [Vite](https://vite.dev/)
4. [pnpm](https://pnpm.io/)
5. [Tailwind CSS](https://tailwindcss.com/)
6. [Serverless](https://www.serverless.com/)

#### Resources
1. [Vue PWA Library](https://cli.vuejs.org/core-plugins/pwa.html)
2. [VSC Vue Plug In ](https://marketplace.visualstudio.com/items?itemName=Vue.volar)
3. [Google PWA Checklist](https://developers.google.com/web/progressive-web-apps/checklist)


## Features

- Vue.js with Nuxt.js and Vite for fast development
- AWS Cognito for user authentication
- AWS AppSync for GraphQL API
- DynamoDB for data storage
- SNS for notifications
- PWA capabilities with offline support
- Multi-language support
- Tailwind CSS for styling
- Pinia for state management
- Vitest for testing

## Prerequisites

- Node.js (v14 or later)
- pnpm
- AWS account
- Serverless.com account

[Go to Table of Contents](#table-of-contents)

## Installation

```bash
# Clone the repository
git clone https://github.com/your-username/your-project-name.git
cd your-project-name

# Install dependencies
pnpm install

# Set up environment variables
cp .env.example .env.local

# Start the development server
pnpm run dev
```
[Go to Table of Contents](#table-of-contents)

## Project Structure

```
.
├── src/
│   ├── components/
│   ├── layouts/
│   ├── pages/
│   ├── plugins/
│   ├── store/
│   ├── utils/
│   └── locales/
├── tests/
├── .env.debug
├── .env.development
├── .env.production
├── nuxt.config.js
├── tailwind.config.js
├── serverless.yml
└── package.json
```
[Go to Table of Contents](#table-of-contents)
## Configuration

### Environment Variables

Create `.env.debug`, `.env.development`, and `.env.production` files in the root directory with the following variables:

```bash
echo "VITE_APP_API_URL=your_api_url
VITE_APP_COGNITO_USER_POOL_ID=your_user_pool_id
VITE_APP_COGNITO_CLIENT_ID=your_client_id
VITE_APP_COGNITO_REGION=your_region" > .env.debug

echo "VITE_APP_API_URL=your_api_url
VITE_APP_COGNITO_USER_POOL_ID=your_user_pool_id
VITE_APP_COGNITO_CLIENT_ID=your_client_id
VITE_APP_COGNITO_REGION=your_region" > .env.development

echo "VITE_APP_API_URL=your_api_url
VITE_APP_COGNITO_USER_POOL_ID=your_user_pool_id
VITE_APP_COGNITO_CLIENT_ID=your_client_id
VITE_APP_COGNITO_REGION=your_region" > .env.production
```
[Go to Table of Contents](#table-of-contents)
### AWS Setup

1. Configure AWS CLI with your credentials.
2. Set up AWS services (Cognito, AppSync, DynamoDB, SNS) using the AWS Management Console or CLI.

## Authentication

Authentication is handled using AWS Cognito. Here's an example of the authentication utility:

All the core authentication functions: `signUp`, `signIn`, `signOut`, and `getCurrentUser`.
The app lock functionality with `startInactivityTimer`, `stopInactivityTimer`, `lockApp`, and `resetInactivityTimer`.

Event listeners for user activity to reset the inactivity timer.
Additional utility functions: `forgotPassword`, `confirmPassword`, `changePassword`, and `refreshSession`.

```javascript
// src/utils/auth.js
import { CognitoUserPool, CognitoUser, AuthenticationDetails } from 'amazon-cognito-identity-js';

const userPool = new CognitoUserPool({
  UserPoolId: import.meta.env.VITE_APP_COGNITO_USER_POOL_ID,
  ClientId: import.meta.env.VITE_APP_COGNITO_CLIENT_ID
});

let inactivityTimer;
const INACTIVITY_TIMEOUT = 5 * 60 * 1000; // 5 minutes in milliseconds

export const signUp = (username, password, email) => {
  return new Promise((resolve, reject) => {
    userPool.signUp(
      username,
      password,
      [{ Name: 'email', Value: email }],
      null,
      (err, result) => {
        if (err) {
          reject(err);
          return;
        }
        resolve(result.user);
      }
    );
  });
};

export const signIn = (username, password) => {
  return new Promise((resolve, reject) => {
    const authenticationDetails = new AuthenticationDetails({
      Username: username,
      Password: password,
    });

    const cognitoUser = new CognitoUser({
      Username: username,
      Pool: userPool
    });

    cognitoUser.authenticateUser(authenticationDetails, {
      onSuccess: (result) => {
        startInactivityTimer(); // Start the inactivity timer after successful login
        resolve(result);
      },
      onFailure: (err) => {
        reject(err);
      },
    });
  });
};

export const signOut = () => {
  return new Promise((resolve) => {
    const cognitoUser = userPool.getCurrentUser();
    if (cognitoUser) {
      cognitoUser.signOut();
    }
    stopInactivityTimer(); // Stop the inactivity timer on sign out
    resolve();
  });
};

export const getCurrentUser = () => {
  return new Promise((resolve, reject) => {
    const cognitoUser = userPool.getCurrentUser();

    if (!cognitoUser) {
      reject(new Error('No user found'));
      return;
    }

    cognitoUser.getSession((err, session) => {
      if (err) {
        reject(err);
        return;
      }

      cognitoUser.getUserAttributes((err, attributes) => {
        if (err) {
          reject(err);
          return;
        }

        const userData = attributes.reduce((acc, attribute) => {
          acc[attribute.Name] = attribute.Value;
          return acc;
        }, {});

        resolve({ ...userData, username: cognitoUser.getUsername() });
      });
    });
  });
};

// App lock functionality
const startInactivityTimer = () => {
  stopInactivityTimer(); // Clear any existing timer
  inactivityTimer = setTimeout(lockApp, INACTIVITY_TIMEOUT);
};

const stopInactivityTimer = () => {
  if (inactivityTimer) {
    clearTimeout(inactivityTimer);
  }
};

const lockApp = () => {
  console.log('App locked due to inactivity');
  signOut();
  // You might want to redirect to a lock screen or trigger a re-authentication
  // For example:
  // window.location.href = '/lock-screen';
};

export const resetInactivityTimer = () => {
  startInactivityTimer();
};

const resetTimerOnActivity = () => {
  resetInactivityTimer();
};

if (typeof window !== 'undefined') {
  window.addEventListener('mousemove', resetTimerOnActivity);
  window.addEventListener('keydown', resetTimerOnActivity);
  window.addEventListener('click', resetTimerOnActivity);
  window.addEventListener('scroll', resetTimerOnActivity);
  window.addEventListener('touchstart', resetTimerOnActivity);
}

// Additional utility functions

export const forgotPassword = (username) => {
  return new Promise((resolve, reject) => {
    const cognitoUser = new CognitoUser({
      Username: username,
      Pool: userPool
    });

    cognitoUser.forgotPassword({
      onSuccess: () => {
        resolve();
      },
      onFailure: (err) => {
        reject(err);
      }
    });
  });
};

export const confirmPassword = (username, verificationCode, newPassword) => {
  return new Promise((resolve, reject) => {
    const cognitoUser = new CognitoUser({
      Username: username,
      Pool: userPool
    });

    cognitoUser.confirmPassword(verificationCode, newPassword, {
      onSuccess: () => {
        resolve();
      },
      onFailure: (err) => {
        reject(err);
      }
    });
  });
};

export const changePassword = (oldPassword, newPassword) => {
  return new Promise((resolve, reject) => {
    const cognitoUser = userPool.getCurrentUser();

    if (!cognitoUser) {
      reject(new Error('No user found'));
      return;
    }

    cognitoUser.getSession((err, session) => {
      if (err) {
        reject(err);
        return;
      }

      cognitoUser.changePassword(oldPassword, newPassword, (err, result) => {
        if (err) {
          reject(err);
          return;
        }
        resolve(result);
      });
    });
  });
};

export const refreshSession = () => {
  return new Promise((resolve, reject) => {
    const cognitoUser = userPool.getCurrentUser();

    if (!cognitoUser) {
      reject(new Error('No user found'));
      return;
    }

    cognitoUser.getSession((err, session) => {
      if (err) {
        reject(err);
        return;
      }

      cognitoUser.refreshSession(session.getRefreshToken(), (err, session) => {
        if (err) {
          reject(err);
          return;
        }
        resolve(session);
      });
    });
  });
};
```

### Example Javascipt Auth Usage
```javascript
import { signUp, signIn, signOut, getCurrentUser } from '@/utils/auth';

// Example usage
try {
  await signUp('newuser', 'password123', 'newuser@example.com');
  console.log('User signed up successfully');
} catch (error) {
  console.error('Sign up failed:', error);
}

try {
  const result = await signIn('username', 'password');
  console.log('User signed in successfully', result);
} catch (error) {
  console.error('Sign in failed:', error);
}

try {
  await signOut();
  console.log('User signed out successfully');
} catch (error) {
  console.error('Sign out failed:', error);
}

try {
  const currentUser = await getCurrentUser();
  console.log('Current user:', currentUser);
} catch (error) {
  console.error('Failed to get current user:', error);
}
```
[Go to Table of Contents](#table-of-contents)
## Push Notification Utility

This module provides a set of utility functions for managing notifications
using Amazon Simple Notification Service (SNS). It allows for creating and
managing topics, subscribing and unsubscribing endpoints, and publishing
notifications.

These functions can be used to implement various notification features in 
your application, such as:
- Sending welcome emails when a user signs up
- Notifying users of important account activities
- Sending system-wide announcements
- Implementing in-app notifications

To use these functions, ensure that you have set up the necessary AWS 
credentials and permissions, and that you have the following environment 
variables configured:
- VITE_APP_AWS_REGION: Your AWS region
- VITE_APP_COGNITO_IDENTITY_POOL_ID: Your Cognito Identity Pool ID

```javascript
// src/utils/notifications.js
import AWS from 'aws-sdk';

// Configure AWS
AWS.config.update({
  region: import.meta.env.VITE_APP_AWS_REGION,
  credentials: new AWS.CognitoIdentityCredentials({
    IdentityPoolId: import.meta.env.VITE_APP_COGNITO_IDENTITY_POOL_ID
  })
});

const sns = new AWS.SNS();

export const subscribeToTopic = (topicArn, protocol, endpoint) => {
  return new Promise((resolve, reject) => {
    const params = {
      Protocol: protocol,
      TopicArn: topicArn,
      Endpoint: endpoint
    };

    sns.subscribe(params, (err, data) => {
      if (err) {
        reject(err);
      } else {
        resolve(data.SubscriptionArn);
      }
    });
  });
};

export const unsubscribeFromTopic = (subscriptionArn) => {
  return new Promise((resolve, reject) => {
    const params = {
      SubscriptionArn: subscriptionArn
    };

    sns.unsubscribe(params, (err) => {
      if (err) {
        reject(err);
      } else {
        resolve();
      }
    });
  });
};

export const publishNotification = (topicArn, message, subject) => {
  return new Promise((resolve, reject) => {
    const params = {
      Message: message,
      TopicArn: topicArn,
      Subject: subject
    };

    sns.publish(params, (err, data) => {
      if (err) {
        reject(err);
      } else {
        resolve(data.MessageId);
      }
    });
  });
};

export const createTopic = (topicName) => {
  return new Promise((resolve, reject) => {
    const params = {
      Name: topicName
    };

    sns.createTopic(params, (err, data) => {
      if (err) {
        reject(err);
      } else {
        resolve(data.TopicArn);
      }
    });
  });
};

export const deleteTopic = (topicArn) => {
  return new Promise((resolve, reject) => {
    const params = {
      TopicArn: topicArn
    };

    sns.deleteTopic(params, (err) => {
      if (err) {
        reject(err);
      } else {
        resolve();
      }
    });
  });
};

export const listSubscriptions = (topicArn) => {
  return new Promise((resolve, reject) => {
    const params = {
      TopicArn: topicArn
    };

    sns.listSubscriptionsByTopic(params, (err, data) => {
      if (err) {
        reject(err);
      } else {
        resolve(data.Subscriptions);
      }
    });
  });
};
```

### Example Notification Code 
```javascript
import { subscribeToTopic, publishNotification } from '@/utils/notifications';

// Use notification functions
await subscribeToTopic(topicArn, 'email', userEmail);
await publishNotification(topicArn, 'Welcome to our app!', 'Welcome');
```
[Go to Table of Contents](#table-of-contents)
## Data Management

### GQL - Data management is handled through AWS AppSync (GraphQL) and DynamoDB. Here's an example of a GraphQL query:

```javascript
// src/graphql/queries.js
import gql from 'graphql-tag';

export const GET_ITEMS = gql`
  query GetItems {
    listItems {
      items {
        id
        name
        description
      }
    }
  }
`;
```
[Go to Table of Contents](#table-of-contents)

### API Integration

API calls are made using Axios. Here's an example:

```javascript
// src/utils/api.js

import axios from 'axios';
import { getCurrentUser } from './auth'; // Assuming you have an auth utility

const api = axios.create({
  baseURL: import.meta.env.VITE_APP_API_URL
});

// Add a request interceptor to automatically add the Authorization header
api.interceptors.request.use(async (config) => {
  try {
    const user = await getCurrentUser();
    const token = user.getSignInUserSession().getIdToken().getJwtToken();
    config.headers.Authorization = `Bearer ${token}`;
  } catch (error) {
    console.error('Error getting auth token', error);
  }
  return config;
}, (error) => {
  return Promise.reject(error);
});

export const fetchItems = async () => {
  const response = await api.get('/items');
  return response.data;
};

// Add other API calls as needed
export const createItem = async (item) => {
  const response = await api.post('/items', item);
  return response.data;
};

export const updateItem = async (id, item) => {
  const response = await api.put(`/items/${id}`, item);
  return response.data;
};

export const deleteItem = async (id) => {
  const response = await api.delete(`/items/${id}`);
  return response.data;
};
```
[Go to Table of Contents](#table-of-contents)



## Streaming and Caching Capabilities

### Streaming
While not natively supported like in Next.js, we achieve similar functionality through:

- Server-Sent Events (SSE) or WebSockets for real-time data streaming
- Vue 3's Suspense component for managing asynchronous dependencies
- Lazy loading of components for improved initial load times

### Caching
Our application implements multiple layers of caching:

1. Service Workers: For offline functionality and caching of assets and API responses
2. Client-side caching: Using [vue-request](https://github.com/AttoJS/vue-request) for data fetching with built-in caching
3. API-level caching: Leveraging AWS AppSync caching capabilities
4. Browser caching: Properly configured cache headers for static assets

These strategies ensure optimal performance and user experience, even in low-connectivity situations.

[Go to Table of Contents](#table-of-contents)
## User Interface

The user interface is built using Vue.js components and Tailwind CSS for styling. Here's an example of a data table component:

```html
<!-- src/components/DataTable.vue -->
<template>
  <table class="min-w-full divide-y divide-gray-200">
    <thead class="bg-gray-50">
      <tr>
        <th v-for="key in Object.keys(items[0])" :key="key" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
          {{ key }}
        </th>
      </tr>
    </thead>
    <tbody class="bg-white divide-y divide-gray-200">
      <tr v-for="item in items" :key="item.id">
        <td v-for="key in Object.keys(item)" :key="key" class="px-6 py-4 whitespace-nowrap">
          {{ item[key] }}
        </td>
      </tr>
    </tbody>
  </table>
</template>

<script setup>
defineProps({
  items: {
    type: Array,
    required: true
  }
});
</script>
```
[Go to Table of Contents](#table-of-contents)
## State Management

State management is handled using Pinia. Here's an example store:

```
// src/stores/items.js

import { defineStore } from 'pinia';
import { fetchItems, createItem } from '@/services/api';
import { GET_ITEMS } from '@/graphql/queries';
import { useQuery } from '@vue/apollo-composable';

export const useItemsStore = defineStore('items', {
  state: () => ({
    items: [],
    isLoading: false,
    error: null
  }),

  actions: {
    // Fetch items using REST API
    async fetchItemsREST() {
      this.isLoading = true;
      this.error = null;
      try {
        this.items = await fetchItems();
      } catch (error) {
        this.error = error.message || 'An error occurred while fetching items';
        console.error('Error fetching items:', error);
      } finally {
        this.isLoading = false;
      }
    },

    // Fetch items using GraphQL
    async fetchItemsGraphQL() {
      this.isLoading = true;
      this.error = null;
      try {
        const { result, loading, error } = useQuery(GET_ITEMS);
        this.isLoading = loading.value;
        if (error.value) {
          throw new Error(error.value.message);
        }
        this.items = result.value.listItems.items;
      } catch (error) {
        this.error = error.message || 'An error occurred while fetching items';
        console.error('Error fetching items:', error);
      } finally {
        this.isLoading = false;
      }
    },

    // Add item using REST API
    async addItem(newItem) {
      this.isLoading = true;
      this.error = null;
      try {
        const createdItem = await createItem(newItem);
        this.items.push(createdItem);
      } catch (error) {
        this.error = error.message || 'An error occurred while adding the item';
        console.error('Error adding item:', error);
      } finally {
        this.isLoading = false;
      }
    }

    // You can add a GraphQL mutation for adding items here if needed
  },

  getters: {
    getItemById: (state) => (id) => state.items.find(item => item.id === id)
  }
});
```
[Go to Table of Contents](#table-of-contents)

## Testing

Testing is done using Vitest. Here's an example test:

```javascript
// tests/components/DataTable.test.js
import { mount } from '@vue/test-utils';
import { describe, it, expect } from 'vitest';
import DataTable from '@/components/DataTable.vue';

describe('DataTable', () => {
  it('renders correctly', () => {
    const wrapper = mount(DataTable, {
      props: {
        items: [{ id: 1, name: 'Item 1' }]
      }
    });
    expect(wrapper.text()).toContain('Item 1');
  });
});
```
[Go to Table of Contents](#table-of-contents)
## Deployment

This project uses Serverless.com for deployment. Configure your `serverless.yml` file and deploy using:

```bash
serverless deploy --stage production
```

## Offline Capabilities

Offline capabilities are implemented using service workers and IndexedDB. This allows for reading transaction data and syncing when back online.

## Internationalization

Multi-language support is implemented using vue-i18n. Locale files are stored in the `src/locales` directory.

## Performance Optimization

Performance is optimized through:
- Code splitting
- Lazy loading of components
- Service worker caching
- AWS AppSync caching

## Accessibility

Accessibility features are implemented following WCAG 2.1 guidelines. We use Vue Axe for accessibility auditing during development.

## Contributing

Please read [CONTRIBUTING.md](CONTRIBUTING.md) for details on our code of conduct and the process for submitting pull requests.

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details.

[Go to Table of Contents](#table-of-contents)
