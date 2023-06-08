---
title: "{{ replace .Name "-" " " | title }}"
date: {{ .Date }}
# description: Optional description
tags: [{{ range $plural, $terms := .Site.Taxonomies }}{{ range $term, $val := $terms }}"{{ printf "%s" $term }}",{{ end }}{{ end }}]
draft: true
---
