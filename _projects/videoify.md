---
layout: project
title: Videoify
date: 2022-12-07 09:00:00 +0900
---

<style type='text/css'>
[class*="project__content"] {
  margin-top: 80px;
}
[class*="content__title"] {
  display: block;
  margin-bottom: 20px;
  font-size: x-large;
}
[class*="project__technologies"] {
  display: flex;
  flex-direction: row;
  margin: 0;
  padding: 0;
  list-style-type: none;
  flex-wrap: wrap;
}
[class*="technology__item"] {
  margin: 0 8px 14px 0;
  padding: 4px 14px;
  border: 1px solid #8898AA;
  border-radius: 100px;
}
[class*="function__list"] {
  padding: 0;
  list-style-type: none;
}
[class*="function__item"] {
  margin-right: 5px;
  color: #22C55E;
}
[class*="goback"] {
  text-decoration: none !important;
}
</style>

<div class=project__content>
  <h2 class=content__title>📝 Description</h2>
  <div class=project__description>
  Reverse-engineered an online video streaming service like YouTube with SSR technique using the Pug view engine. All users can watch the uploaded videos and support more functions for authorized users. The authorized user can upload, edit, comment on the videos, and subscribe to other channels.
  </div>
</div>

<div class=project__content>
  <h2 class=content__title>🛠️ Technologies</h2>
  <ul class=project__technologies>
    <li class=technology__item>Node.js</li>
    <li class=technology__item>Express.js</li>
    <li class=technology__item>JavaScript</li>
    <li class=technology__item>ES6</li>
    <li class=technology__item>Babel</li>
    <li class=technology__item>MongoDB</li>
    <li class=technology__item>Mongoose</li>
    <li class=technology__item>Multer</li>
    <li class=technology__item>ESLint</li>
    <li class=technology__item>Pug.js</li>
    <li class=technology__item>Webpack</li>
    <li class=technology__item>SCSS</li>
    <li class=technology__item>AJAX</li>
    <li class=technology__item>REST APIs</li>
    <li class=technology__item>Mongo Atlas</li>
    <li class=technology__item>AWS S3</li>
    <li class=technology__item>Fly.io</li>
  </ul>
</div>

<div class=project__content>
  <h2 class=content__title>🪄 Visual Sample · 
    <a href='https://paolochang-nmdtube.fly.dev/' target='_blank'>DEMO</a>
  </h2>
  <h3>Home Screen</h3>
  <img src="/assets/img/project_videoify_home.png" alt="Videoify Home" width="100%">
  <h3>Play Screen</h3>
  <img src="/assets/img/project_videoify_watch.png" alt="Videoify Watch" width="100%">
  <h3>Search by Tag</h3>
  <img src="/assets/img/project_videoify_search_by_tag.png" alt="Videoify Watch" width="100%">
</div>

<div class="project__content container-fluid">
  <h2 class=content__title>💡 Functions</h2>
  <div class="row">
    <div class="col-lg-6">
      <strong>Back End</strong>
      <ul class=function__list>
        <li><i class='function__item fas fa-check'></i>MVC</li>
        <li><i class='function__item fas fa-check'></i>Routing</li>
        <li><i class='function__item fas fa-check'></i>Templates</li>
        <li><i class='function__item fas fa-check'></i>Models</li>
        <li><i class='function__item fas fa-check'></i>Relationships</li>
      </ul>
    </div>
    <div class="col-lg-6">
      <strong>Users</strong>
      <ul class=function__list>
        <li><i class='function__item fas fa-check'></i>User Authentication</li>
        <li><i class='function__item fas fa-check'></i>User Profile</li>
        <li><i class='function__item fas fa-check'></i>Log In</li>
        <li><i class='function__item fas fa-check'></i>Log Out</li>
        <li><i class='function__item fas fa-check'></i>Subscriptions</li>
      </ul>
    </div>
    <div class="col-lg-6">
      <strong>Video</strong>
      <ul class=function__list>
        <li><i class='function__item fas fa-check'></i>Video Upload</li>
        <li><i class='function__item fas fa-check'></i>Video Recording</li>
        <li><i class='function__item fas fa-check'></i>Search Video</li>
        <li><i class='function__item fas fa-check'></i>AJAX Comments</li>
        <li><i class='function__item fas fa-check'></i>View Count</li>
      </ul>
    </div>
    <div class="col-lg-6">
      <strong>Deployment</strong>
      <ul class=function__list>
        <li><i class='function__item fas fa-check'></i>Fly.io</li>
        <li><i class='function__item fas fa-check'></i>AWS S3 Upload</li>
        <li><i class='function__item fas fa-check'></i>Mongo Atlas</li>
      </ul>
    </div>
  </div>
</div>
