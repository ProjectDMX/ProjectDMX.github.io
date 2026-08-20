---
layout: about
title: DMXLab
permalink: /
subtitle:

profile:

selected_papers: false # includes a list of papers marked as "selected={true}"
social: false # includes social icons at the bottom of the page

announcements:
  enabled: true # includes a list of news items
  scrollable: true # adds a vertical scroll bar if there are more than 3 news items
  limit: 5 # leave blank to include all the news in the `_news` folder

latest_posts:
  enabled: false
  scrollable: true # adds a vertical scroll bar if there are more than 3 new posts items
  limit: 3 # leave blank to include all the blog posts
---

## Vision
Our vision is to make frontier AI systems observable, steerable, and scientifically legible while they run at production speed. 

Today's large language models are increasingly deployed as opaque, high-throughput systems: they generate, retrieve, plan, refuse, hallucinate, and adapt in real time, but their internal computation is often hidden from researchers once models leave offline analysis settings. 

DMXLab aims to close this gap by building the systems frameworks, measurement science, and downstream applications needed to inspect deep models during execution.

## Open Source Releases

<a class="home-release-card" href="https://github.com/ProjectDMX/DMI" aria-label="Explore DMI on GitHub">
  <span class="home-release-card__logo">
    <img
      src="{{ '/assets/img/dmi-logo.png' | relative_url }}"
      alt="DMI — Deep Model Inspector"
      width="2218"
      height="779"
      loading="lazy"
    >
  </span>
  <span class="home-release-card__copy">
    <strong>DMI</strong>
    <span>A decoupled, asynchronous observation system for high-speed LLM inference.</span>
    <span class="home-release-card__cta">Explore DMI on GitHub <span aria-hidden="true">→</span></span>
  </span>
</a>
