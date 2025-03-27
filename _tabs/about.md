---
# the default layout is 'page'
title: Hi, I'm Shin Gyeongik (신경익)
icon: fas fa-info-circle
order: 5
---


<style type='text/css'>
[class*="about__content"] {
  margin-top: 80px;
}
[class*="about__title"] {
  display: block;
  margin-bottom: 20px;
  font-size: x-large;
}
[class*="about__subtitle"] {
  display: block;
  margin-bottom: 15px;
  font-size: large;
}
[class*="experience__container"] {
  display: flex;
  flex-direction: row;
}
[class*="experience__company"] {
  width: 50%;
}
[class*="experience__infotab"] {
  width: 70%;
  display: flex;
  flex-direction: column;
}
[class*="experience__stack"] {
  font-size: smaller;
  color: #AA9888;
}
[class*="tech__stack"] {
  font-size: smaller;
  color:rgb(160, 138, 221);
}
[class*="experience__workperiod"] {
  font-size: smaller;
  color: #8898AA;
}
[class*="skills__contents"] {
  display: flex;
  flex-direction: row;
  margin: 0;
  padding: 0;
  list-style-type: none;
  flex-wrap: wrap;
}
[class*="skills__item"] {
  margin: 0 8px 14px 0;
  padding: 4px 14px;
  border: 1px solid #8898AA;
  border-radius: 100px;
}
</style>

<div class=about__content>
  <strong class=about__title>👨‍💻 Work Experiences
    <span id=workyear>Sep 2020</span>
  </strong>
  <hr/>
  <div class=experience__container>
    <strong class=experience__company>TU Korea</strong>
    <div class=experience__infotab>
      <span>Visiting Research Fellow</span>
      <span id=tukor_desc class=experience__stack> Developing a 3D reconstruction model based on gaussian splatting </span>
      <span id=tukor_stack class=tech__stack> PyTorch, OpenCV, Python, CUDA </span>
      <span id=tukor_period class=experience__workperiod> Mar 2025 - </span>
    </div>
  </div>
  <hr/>
  <div class=experience__container>
    <strong class=experience__company>MORAI</strong>
    <div class=experience__infotab>
      <span>Software Engineer, Virtual Sensor Team</span>
      <span id=morai_desc class=experience__stack>Developed sensor simulations, including Camera and LiDAR</span>
      <span id=morai_desc class=tech__stack>Unreal Engine, C++ </span>
      <span id=morai_period class=experience__workperiod>May 2023 - Mar 2025</span>
    </div>
  </div>
  <hr/>
  <div class=experience__container>
    <strong class=experience__company>Chung-Ang Univ.</strong>
    <div class=experience__infotab>
      <span>Research Assistant, CV/AR Lab</span>
      <span id=cvar_desc class=experience__stack>Developed an HDR reconstruction model for realistic textures in reconstructed 3D models </span>
      <span id=cvar_desc class=tech__stack>Tensorflow, OpenCV, Python, CUDA </span>
      <span id=cvar_period class=experience__workperiod>Sep 2020 - Dec 2022</span>
    </div>
  </div>
  <hr/>
</div>

<div class=about__content>
  <strong class=about__title>💪 Skills</strong>
  <hr/>
  
  <div class=skills__container>
    <strong class=about__subtitle>Programming Languages</strong>
    <div class=skills__infotab>
      <ul class=PL__skills__contents>
        <li class=skills__item>C++</li>
        <li class=skills__item>Python</li>
      </ul>
    </div>
  </div>
  <hr/>

  <div class=skills__container>
    <strong class=about__subtitle>Frameworks</strong>
    <div class=skills__infotab>
      <ul class=FW__skills__contents>
          <li class=skills__item>Unreal Engine</li>
          <li class=skills__item>Tensorflow</li>
          <li class=skills__item>OpenCV</li>
      </ul>
    </div>
  </div>
  <hr/>

<script>
  function parseDateString(dateString) {
    const [month, year] = dateString.split(" ");
    const monthMap = {
      Jan: 0,
      Feb: 1,
      Mar: 2,
      Apr: 3,
      May: 4,
      Jun: 5,
      Jul: 6,
      Aug: 7,
      Sep: 8,
      Oct: 9,
      Nov: 10,
      Dec: 11,
    };
    const monthNumber = monthMap[month];
    const dateObject = new Date(year, monthNumber, 1);
    return dateObject;
  }

  function getProfessionalWorkYear(startDate) {
    const currentDate = new Date();
    const diffYears = currentDate.getFullYear() - startDate.getFullYear();
    return `${diffYears}+ yrs`;
  }

  function getCurrentWorkPeriod(startDate) {
    /* Check if the startDate argument is a valid Date object */
    if (!(startDate instanceof Date) || isNaN(startDate)) {
      throw new Error("Invalid date object");
    }

    const currentDate = new Date();

    const diffYears = currentDate.getFullYear() - startDate.getFullYear();
    const diffMonths = (currentDate.getMonth() - startDate.getMonth()) + (diffYears * 12) + 1;

    if (diffYears === 0) {
      return `${diffMonths} mos`;
    } else {
      return `${diffYears} ${diffYears > 1 ? 'yrs' : 'yr'}, ${diffMonths % 12} mos`;
    }
  }

  const workyearEl = document.getElementById("workyear");
  const uriteEl = document.getElementById("tukor_period");

  const parsedWorkyearDate = parseDateString(workyearEl.textContent);
  const parsedUriteDate = parseDateString(uriteEl.textContent.split(" - ")[0].trim());

  const workyearStartDate = new Date(parsedWorkyearDate);
  const uriteStartDate = new Date(parsedUriteDate);

  workyearEl.innerHTML = ` ·  ${getProfessionalWorkYear(workyearStartDate)}`;
  uriteEl.append(` · ${getCurrentWorkPeriod(uriteStartDate)}`);
</script>
