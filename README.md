fork from https://github.com/dgrubelic/vue-authenticate

# vue-authenticate

modify oauth2 providers config to support [laravel](https://laravel.com) passport

## Supported OAuth providers and configurations

1. Facebook (https://github.com/dgrubelic/vue-authenticate/blob/master/src/options.js#L21)
2. Google (https://github.com/dgrubelic/vue-authenticate/blob/master/src/options.js#L34)
3. Github (https://github.com/dgrubelic/vue-authenticate/blob/master/src/options.js#L49)
4. Instagram (https://github.com/dgrubelic/vue-authenticate/blob/master/src/options.js#L61)
5. Twitter (https://github.com/dgrubelic/vue-authenticate/blob/master/src/options.js#L72)
6. Bitbucket (https://github.com/dgrubelic/vue-authenticate/blob/master/src/options.js#L81)
7. LinkedIn (https://github.com/dgrubelic/vue-authenticate/blob/master/src/options.js#L93)
8. Microsoft Live (https://github.com/dgrubelic/vue-authenticate/blob/master/src/options.js#L106)

## Installation
```bash
npm install vue-auth-client
```

## Usage
```javascript
import Vue from 'vue'
import VueAxios from 'vue-axios'
import VueAuthenticate from 'vue-auth-client'
import axios from 'axios';

Vue.use(VueAxios, axios)
Vue.use(VueAuthenticate, {
  baseUrl: 'http://localhost:3000', // Your API domain
  
  providers: {
    github: {
      clientId: '',
      redirectUri: 'http://localhost:3000/auth/callback' // Your client app URL
    },

    oauth2: {
      url: '/oauth/token',
      authorizationEndpoint: 'https://laravel.test/oauth/authorize',
      clientId: '4',
      responseParams: {
        code: 'code',
        grant_type: 'authorization_code',
        client_secret: 'WOfm88oUTYQgYdCB0RhndBY7tgUYh4LGg3vP4nof',
        clientId: 'client_id',
        redirectUri: 'redirect_uri'
      }
    },
  }
})
```

### Email & password login and registration
```javascript
new Vue({
  methods: {
    login: function () {
      this.$auth.login({ email, password }).then(function () {
        // Execute application logic after successful login
      })
    },

    register: function () {
      this.$auth.register({ name, email, password }).then(function () {
        // Execute application logic after successful registration
      })
    }
  }
})
```

```html
<button @click="login()">Login</button>
<button @click="register()">Register</button>
```

### Social account authentication

```javascript
new Vue({
  methods: {
    authenticate: function (provider) {
      this.$auth.authenticate(provider).then(function () {
        // Execute application logic after successful social authentication
      })
    }
  }
})
```

```html
<button @click="authenticate('github')">auth Github</button>
<button @click="authenticate('facebook')">auth Facebook</button>
<button @click="authenticate('google')">auth Google</button>
<button @click="authenticate('twitter')">auth Twitter</button>
```

### Vuex authentication

#### Import and initialize all required libraries

```javascript
// ES6 example
import Vue from 'vue'
import Vuex from 'vuex'
import VueAxios from 'vue-axios'
import { VueAuthenticate } from 'vue-auth-client'
import axios from 'axios';

Vue.use(Vuex)
Vue.use(VueAxios, axios)

const vueAuth = new VueAuthenticate(Vue.prototype.$http, {
  baseUrl: 'http://localhost:3000'
})
```

```javascript
// CommonJS example
var Vue = require('vue')
var Vuex = require('vuex')
var VueAxios = require('vue-axios')
var VueAuthenticate = require('vue-authenticate')
var axios = require('axios');

Vue.use(Vuex)
Vue.use(VueAxios, axios)

// ES5, CommonJS example
var vueAuth = VueAuthenticate.factory(Vue.prototype.$http, {
  baseUrl: 'http://localhost:3000'
})
```

Once you have created VueAuthenticate instance, you can use it in Vuex store like this:

```javascript
export default new Vuex.Store({
  
  // You can use it as state property
  state: {
    isAuthenticated: false
  },

  // You can use it as a state getter function (probably the best solution)
  getters: {
    isAuthenticated () {
      return vueAuth.isAuthenticated()
    }
  },

  // Mutation for when you use it as state property
  mutations: {
    isAuthenticated (state, payload) {
      state.isAuthenticated = payload.isAuthenticated
    }
  },

  actions: {

    // Perform VueAuthenticate login using Vuex actions
    login (context, payload) {

      vueAuth.login(payload.user, payload.requestOptions).then((response) => {
        context.commit('isAuthenticated', {
          isAuthenticated: vueAuth.isAuthenticated()
        })
      })

    }
  }
})
```

Later in Vue component, you can dispatch Vuex state action like this

```javascript
// You define your store logic here
import store from './store.js'

new Vue({
  store,

  computed: {
    isAuthenticated: function () {
      return this.$store.getters.isAuthenticated()
    }
  },

  methods: {
    login () {
      this.$store.dispatch('login', { user, requestOptions })
    }
  }
})
```

### Custom request and response interceptors

You can easily setup custom request and response interceptors if you use different request handling library.

**Important**: You must set both `request` and `response` interceptors at all times.

```javascript

/**
* This is example for request and response interceptors for axios library
*/

Vue.use(VueAuthenticate, {
  bindRequestInterceptor: function () {
    this.$http.interceptors.request.use((config) => {
      if (this.isAuthenticated()) {
        config.headers['Authorization'] = [
          this.options.tokenType, this.getToken()
        ].join(' ')
      } else {
        delete config.headers['Authorization']
      }
      return config
    })
  },

  bindResponseInterceptor: function () {
    this.$http.interceptors.response.use((response) => {
      this.setToken(response)
      return response
    })
  }
})

```

## License

The MIT License (MIT)

Copyright (c) 2017 Davor Grubelić

Permission is hereby granted, free of charge, to any person obtaining a copy of
this software and associated documentation files (the "Software"), to deal in
the Software without restriction, including without limitation the rights to
use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of
the Software, and to permit persons to whom the Software is furnished to do so,
subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS
FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR
COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER
IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN
CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
