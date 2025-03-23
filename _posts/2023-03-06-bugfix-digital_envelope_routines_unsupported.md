---
title: "React: fix digital envelope routines::unsupported"
date: 2023-03-06 12:00:00 -0500
categories: [JavaScript, React]
tags: [React, Bugfix]
---

<style type='text/css'>
blockquote pre {
  overflow: auto !important;
  overflow-wrap: anywhere !important;
  white-space: pre-wrap;
}
[class*="kw"] {
  color: #14A7CD;
}
[class*="cn"] {
  color: #E5E50E;
}
[class*="err_red_bold"] {
  color: #F14B4C;
  font-weight: 600;
}
[class*="err_red"] {
  color: #DA6A6A;
}
[class*="err_greyout"] {
  color: #919191;
}
[class*="err_green"] {
  color: #0CBC7A;
}
[class*="err_infoblue"] {
  color: #4E8DD4;
}
[class*="fail"] {
  color: #F0F0F0;
  font-weight: 600;
  background-color: #CD3131;
}

</style>

The "digital envelope routines::unsupported" error is a common issue that can occur in React.js projects when using outdated versions of Node.js or SSL packages. This error can be resolved by updating your Node.js version and SSL packages to the latest versions with security fixes.

## Error Message

> <pre>
> Starting the development server...
> 
> /Users/username/Documents/workspace/react-project/client/node_modules/react-scripts/scripts/start.js:19
>   throw err;
>   ^
> 
> Error: error:0308010C:digital envelope routines::unsupported
>     <span class=err_greyout>at new Hash (node:internal/crypto/hash:71:19)</span>
>     <span class=err_greyout>at Object.createHash (node:crypto:133:10)</span>
>     at module.exports <span class=err_greyout>(/Users/username/Documents/workspace/react-project/client/</span>node_modules/webpack/lib/util/createHash.js:135:53)
>     at NormalModule._initBuildHash <span class=err_greyout>(/Users/username/Documents/workspace/react-project/client/</span>node_modules/webpack/lib/NormalModule.js:417:16)
>     at <span class=err_greyout>/Users/username/Documents/workspace/react-project/client/</span>node_modules/webpack/lib/NormalModule.js:452:10
>     at <span class=err_greyout>/Users/username/Documents/workspace/react-project/client/</span>node_modules/webpack/lib/NormalModule.js:323:13
>     at <span class=err_greyout>/Users/username/Documents/workspace/react-project/client/</span>node_modules/loader-runner/lib/LoaderRunner.js:367:11
>     at <span class=err_greyout>/Users/username/Documents/workspace/react-project/client/</span>node_modules/loader-runner/lib/LoaderRunner.js:233:18
>     at context.callback <span class=err_greyout>(/Users/username/Documents/workspace/react-project/client/</span>node_modules/loader-runner/lib/LoaderRunner.js:111:13)
>     at <span class=err_greyout>/Users/username/Documents/workspace/react-project/client/</span>node_modules/babel-loader/lib/index.js:59:103 {
>   opensslErrorStack: [ <span class=err_greyout>'error:03000086:digital envelope routines::initialization error'</span> ],
>   library: <span class=err_green>'digital envelope routines'</span>,
>   reason: <span class=err_green>'unsupported'</span>,
>   code: <span class=err_green>'ERR_OSSL_EVP_UNSUPPORTED</span>'
> }
> 
> Node.js v18.8.0
> <span class=err_red>error</span> Command failed with exit code 1.
> <span class=err_infoblue>info</span> Visit <a href="https://yarnpkg.com/en/docs/cli/run" target="_blank">https://yarnpkg.com/en/docs/cli/run</a> for documentation about this command.
> </pre>

## Solution

If you encounter the "digital envelope routines::unsupported" error, you may be tempting to use the following easiest solutions:

- Downgrade Node.js to pre v17 or;
- Use the legacy SSL provider:

  ```json
  {
    "scripts": {
      "start": "react-scripts --openssl-legacy-provider start"
    }
  }
  ```

These solutions are considered hacks that leave your builds open to security threats. Downgrading Node.js or using the legacy SSL provider may temporarily resolve the issue, but they are not permanent solutions and can potentially introduce security vulnerabilities into your project.

The safest solution is to update both your Node.js version and SSL packages to the latest versions with security fixes. One way to do this is to use the following command:

```sh
npm audit fix --force
```

However, be aware that this command may not be suitable for complex builds, as it may pull in breaking security fixes that could potentially break your build.

If you use Yarn instead of NPM, the above solution may not work for you. In that case, you will need to find a similar solution specific to Yarn.

## Reference

- [Error message "error:0308010C:digital envelope routines::unsupported"](https://stackoverflow.com/a/73027407)
