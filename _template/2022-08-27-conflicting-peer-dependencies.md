---
title: "Deployment: fix conflicting peer dependency on React while deploying on Netlify"
date: 2022-08-27 12:00:00 -0500
categories: [Deployment, Netlify]
tags: [React, Netlify]
---

I found one of my application failed to build on Netlify after I add new feature. And I found the problem caused by yhe conflicts in the dependencies of package in the `package.json` file.

## Solution on local development

While in the local development, we can easily fix the issue by the following command:

```bash
$ npm install <package_name> --legacy-peer-deps
```

to ignore these dependencies conflict. However, Netlify does not know that developer added a option to ignore the conflicts to the `npm install` and the build fails.

## Fix on Netlify

1. Go to the deploys section in your settings. You can make use of this link https://app.netlify.com/sites/{site-name}/settings/deploys. Replace {site-name} with the name of your site.

2. Scroll to the Environmental variables section.

3. Click on Edit variables and add the pair. `NPM_FLAGS` on the left and `--legacy-peer-deps` on the right. You could also add other flags you used while running npm install as Netlify will make use of this flag while installing the dependencies.

## References:

- [I am trying to host my react website on netlify but there is a npm install error](https://stackoverflow.com/questions/69351433/i-am-trying-to-host-my-react-website-on-netlify-but-there-is-a-npm-install-error)
