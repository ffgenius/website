---
title: Projects - FFgenius
display: Projects
description: List of projects that I am proud of
wrapperClass: 'text-center'
art: dots
projects:
  DevOps Tools:
    - name: 'Dockerfile Generator'
      link: 'https://www.dockerfile.app/'
      desc: 'Docker configuration made simple'
      icon: 'dockerfile'
    - name: 'Digital Ocean'
      link: 'https://www.digitalocean.com/community/tools/nginx'
      desc: 'Nginx configuration'
      icon: 'digital'
---

<!-- @layout-full-width -->
<ListProjects :projects="frontmatter.projects" />
