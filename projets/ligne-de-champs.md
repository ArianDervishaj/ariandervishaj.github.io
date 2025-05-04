---
layout: default
---

[{% t global.back %}]({% t global.back_link %})

# {% t ligne_de_champs.title %}

{% t  ligne_de_champs.explication %}

---

### {% t ligne_de_champs.features_title %}

<ul>
  {% for item in site.translations[site.lang].ligne_de_champs.features %}
    <li>{{ item }}</li>
  {% endfor %}
</ul>
---

### Screenshots

{% for shot in site.translations[site.lang].ligne_de_champs.screenshots %}

#### {{ shot.title }}

![{{ shot.title }}]({{ shot.image }})
{% endfor %}
---

### {% t ligne_de_champs.run_title %}

{% t ligne_de_champs.run_intro %}

1. {{ site.translations[site.lang].ligne_de_champs.run_steps[0] }}
2. {{ site.translations[site.lang].ligne_de_champs.run_steps[1] }}
    ```bash
    {{ site.translations[site.lang].ligne_de_champs.run_clone }}
    ```
3. {{ site.translations[site.lang].ligne_de_champs.run_steps[2] }}
    ```bash
    {{ site.translations[site.lang].ligne_de_champs.run_build[0] }}
    {{ site.translations[site.lang].ligne_de_champs.run_build[1] }}
    {{ site.translations[site.lang].ligne_de_champs.run_build[2] }}
    ```

---

### {% t ligne_de_champs.more_title %}

[{% t ligne_de_champs.github_link %}](https://github.com/ArianDervishaj/simulation-lignes-de-champs)
