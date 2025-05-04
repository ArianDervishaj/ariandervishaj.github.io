---
layout: default
---

[{% t global.back %}]({% t global.back_link %})

# {% t systeme_solaire.title %}

{% t systeme_solaire.explanation %}

---



### {% t systeme_solaire.features_title %}

<ul>
  {% for item in site.translations[site.lang].systeme_solaire.features %}
    <li>{{ item }}</li>
  {% endfor %}
</ul>

---

### Screenshots

{% for shot in site.translations[site.lang].systeme_solaire.screenshots %}

#### {{ shot.title }}

![{{ shot.title }}]({{ shot.image }})
{% endfor %}


---

### {% t systeme_solaire.run_title %}

{% t systeme_solaire.run_intro %}

1. {{ site.translations[site.lang].systeme_solaire.run_steps[0] }}
2. {{ site.translations[site.lang].systeme_solaire.run_steps[1] }}
    ```bash
    {{ site.translations[site.lang].systeme_solaire.run_clone }}
    ```
3. {{ site.translations[site.lang].systeme_solaire.run_steps[2] }}
    ```bash
    {{ site.translations[site.lang].systeme_solaire.run_build[0] }}
    {{ site.translations[site.lang].systeme_solaire.run_build[1] }}
    {{ site.translations[site.lang].systeme_solaire.run_build[2] }}
    ```


---

### {% t systeme_solaire.more_title %}

[{% t systeme_solaire.github_link %}](https://github.com/ArianDervishaj/simulation-systeme-solaire)
