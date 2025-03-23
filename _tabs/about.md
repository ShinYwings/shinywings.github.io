---
# the default layout is 'page'
title: I'm Paolo Chang,　　　　　　　　　　<br/>a Software Developer
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
[class*="experience__container"] {
  display: flex;
  flex-direction: row;
}
[class*="experience__company"] {
  width: 50%;
}
[class*="experience__infotab"] {
  width: 50%;
  display: flex;
  flex-direction: column;
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
  <strong class=about__title>👨‍💻 Professional Experience
    <span id=workyear>Jan 2019</span>
  </strong>
  <hr/>
  <div class=experience__container>
    <strong class=experience__company>U-Rite</strong>
    <div class=experience__infotab>
      <span>Full-Stack Software Engineer</span>
      <span id=urite class=experience__workperiod>Mar 2023 - Current</span>
    </div>
  </div>
  <hr/>
  <div class=experience__container>
    <strong class=experience__company>Architech</strong>
    <div class=experience__infotab>
      <span>Front-End Software Engineer</span>
      <span class=experience__workperiod>Sept 2021 - Aug 2022 · 1 yr</span>
    </div>
  </div>
  <hr/>
  <div class=experience__container>
    <strong class=experience__company>Hive (Start-up)</strong>
    <div class=experience__infotab>
      <span>Full-Stack Software Engineer</span>
      <span class=experience__workperiod>Aug 2022 - Mar 2023 · 1 yr 4 mos</span>
      <span class=experience__workperiod>Feb 2021 - Sept 2021</span>
    </div>
  </div>
  <hr/>
  <div class=experience__container>
    <strong class=experience__company>Portfolio+</strong>
    <div class=experience__infotab>
      <span>Front-End Software Developer</span>
      <span class=experience__workperiod>Jan 2019 - Jan 2021 · 2 yrs 1 mo</span>
      <span>Junior Automation Developer (Co-op)</span>
      <span class=experience__workperiod>Sept 2018 - Dec 2019 · 4 mos</span>
    </div>
  </div>
  <hr/>
  <div class=experience__container>
    <strong class=experience__company>Woodbine Entertainment Group</strong>
    <div class=experience__infotab>
      <span>Junior Full-Stack Software Developer (Co-op)</span>
      <span class=experience__workperiod>Sept 2017 - May 2018 · 9 mos</span>
    </div>
  </div>
  <hr/>
</div>

<div class=about__content>
  <strong class=about__title>💪 Skills</strong>
  <ul class=skills__contents>
    <li class=skills__item>JavaScript</li>
    <li class=skills__item>ES6</li>
    <li class=skills__item>TypeScript</li>
    <li class=skills__item>React</li>
    <li class=skills__item>React Native</li>
    <li class=skills__item>Redux</li>
    <li class=skills__item>Webpack</li>
    <li class=skills__item>HTML</li>
    <li class=skills__item>CSS</li>
    <li class=skills__item>AJAX</li>
    <li class=skills__item>Node.js</li>
    <li class=skills__item>Express.js</li>
    <li class=skills__item>REST API</li>
    <li class=skills__item>MongoDB</li>
    <li class=skills__item>PostgreSQL</li>
    <li class=skills__item>MySQL</li>
    <li class=skills__item>C</li>
    <li class=skills__item>C++</li>
    <li class=skills__item>C#</li>
    <li class=skills__item>.NET</li>
    <li class=skills__item>Java</li>
    <li class=skills__item>Python</li>
    <li class=skills__item>Git</li>
    <li class=skills__item>SVN</li>
    <li class=skills__item>Azure</li>
    <li class=skills__item>AWS</li>
    <li class=skills__item>Docker</li>
    <li class=skills__item>Jenkins</li>
    <li class=skills__item>Jira</li>
  </ul>
</div>

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
  const uriteEl = document.getElementById("urite");

  const parsedWorkyearDate = parseDateString(workyearEl.textContent);
  const parsedUriteDate = parseDateString(uriteEl.textContent.split(" - ")[0].trim());

  const workyearStartDate = new Date(parsedWorkyearDate);
  const uriteStartDate = new Date(parsedUriteDate);

  workyearEl.innerHTML = ` ·  ${getProfessionalWorkYear(workyearStartDate)}`;
  uriteEl.append(` · ${getCurrentWorkPeriod(uriteStartDate)}`);
</script>
