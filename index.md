---
title: "Simone's Statistics Blog"
layout: default
permalink: /
---

<nav style="background: url('/assets/images/bkg.png'); padding-bottom: 20px; font-family: sans-serif;">
  <ul class="navbar" style="list-style: none; margin: 0; padding: 0; display: flex; gap: 20px; align-items: center;">
    <li><a href="/" style="color: #63c0f5; text-decoration: none; margin-right:25px; font-size: 25px;"> Home </a></li>

    <li style="position: relative;">
      <a href="#" style="color: #63c0f5; text-decoration: none; margin-right:25px; font-size: 25px; ">Homework ▾</a>
      <ul style="
        display: none;
        position: absolute;
        background-color: #333;
        list-style: none;
        padding: 10px;
        margin: 0;
        border-radius: 6px;
        box-shadow: 0 2px 6px rgba(0,0,0,0.3);
      ">
        <li><a href="/homework1/" style="color: #63c0f5; text-decoration: none; display: block; padding: 5px 10px; font-size: 18px;">- Homework 1</a></li>
        <li><a href="/homework2/" style="color: #63c0f5; text-decoration: none; display: block; padding: 5px 10px; font-size: 18px">- Homework 2</a></li>
      </ul>
    </li>

    <li><a href="/about/" style="color: #63c0f5; text-decoration: none; margin-right:25px; font-size: 25px;">About</a></li>
  </ul>
</nav>

<script>
  document.querySelectorAll("nav li").forEach(li => {
    li.addEventListener("mouseenter", () => {
      const submenu = li.querySelector("ul");
      if (submenu) submenu.style.display = "block";
    });
    li.addEventListener("mouseleave", () => {
      const submenu = li.querySelector("ul");
      if (submenu) submenu.style.display = "none";
    });
  });
</script>

---

# Welcome to My Statistics Blog

Here you can find my work and assignments from the course.

---

## My Homework

- [Homework 1](/homework1/)

- [Homework 2](/homework2/)

---

© 2025 Simone Di Gregorio — Statistics Course Blog
