---
title: "Vue.js: Environment Setup and Create a Project"
date: 2023-02-11 14:00:00 -0500
categories: [JavaScript, Vue.js]
tags: [JavaScript, Vue.js]
---

This post includes the environment setup for Vue projects and creating Vue projects using Vue CLI. You can also find out how to store the preset of the Vue project settings and change the package manager.

### Environment Setup

#### Check installation

```sh
$ vue --version
```

#### Installation

```sh
# make sure remove all vue cli
$ sudo npm uninstall --global vue-cli
$ sudo npm uninstall --global @vue/cli
# install the latest version of vue cli
$ sudo npm install --global vue
$ sudo npm install --global @vue/cli@latest
```

#### Update Vue version

```sh
$ npm update -g @vue/cli
```

#### VSCode Extension

- `Vue Language Features (Volar)`
- `Vetur`

### Create Vue Project

#### Default Setup with Vue.js 3.x

```sh
$ vue create <project_name>
Vue CLI v5.0.8
? Please pick a preset: (Use arrow keys)
❯ Default ([Vue 3] babel, eslint)
  Default ([Vue 2] babel, eslint)
  Manually select features
```

#### Manual Setup

- By using manual setup, you can save the preset for the future projects

```sh
$ vue create <project_name>
Vue CLI v5.0.8
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel, Router, Vuex, Linter
? Choose a version of Vue.js that you want to start the project with 3.x
? Use history mode for router? (Requires proper server setup for index fallback in production) Yes
? Pick a linter / formatter config: Standard
? Pick additional lint features: Lint on save
? Where do you prefer placing config for Babel, ESLint, etc.? In dedicated config files
? Save this as a preset for future projects? Yes
? Save preset as: Basic

# In creating future project

$ vue create <project_name>
Vue CLI v5.0.8
? Please pick a preset: (Use arrow keys)
❯ Basic ([Vue 3] babel, router, vuex, eslint)
  Default ([Vue 3] babel, eslint)
  Default ([Vue 2] babel, eslint)
  Manually select features
```

#### Choose Package Manager while creating project

At the very first time of creating a Vue project, Vue CLI asks which package manager will be used. This one time selection will be saved as a default setting and will create the future projects with the selected package manager. To change the package manager, there are two options.

- Add `--packageManager` option in vue `create` command for one-time

```sh
$ vue create project-name --packageManager=yarn
```

- Setup the `packageManager` in `.vuerc` for permanant change

```json
{
  "useTaobaoRegistry": false,
  "packageManager": "yarn"
}
```
{: file='.vuerc'}

### Reference

- [VSCode Extension: Vue Language Features (Volar)](https://marketplace.visualstudio.com/items?itemName=Vue.volar)
- [VSCode Extension: Vetur](https://marketplace.visualstudio.com/items?itemName=octref.vetur)
- [Can't Reselect NPM or Yarn after first project creation](https://github.com/vuejs/vue-cli/issues/1300)
