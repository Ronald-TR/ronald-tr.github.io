<!-- +++
title = "{{ replace .TranslationBaseName '-' ' ' | title }}"
date = "{{ .Date }}"
draft = true

+++ -->
---
title: "{{ replace .Name "-" " " | title }}"
date: {{ .Date }}
draft: true
---
