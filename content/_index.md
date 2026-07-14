---
layout: list
title: Biomedical Data Management Systems
subtitle: Workshop @ **[VLDB 2026](https://vldb.org/2026/)**
preview_thumbnail: images/logo-type-preview.png
thumbnail: images/logo-type-vertical.png
summary: |
  <i class="fa-regular fa-calendar"></i> **September 4th**, <i class="fa-solid fa-location-dot"></i> **Boston, USA**

  Community of **biomedical informatics** and **data management** researchers and practitioners who engage in
  **collaborative efforts** to identify the most pressing **data-related challenges**
  (e.g., scalability, interoperability, quality, usability),
  and develop **novel methods and systems** to overcome them, thereby helping to accelerate the pace of innovation
  in **biomedical research and healthcare**.
sections:
    - title: Speakers
      partial: list
      content:
        page: people
        param: pages
        where:
          key: Params.groups
          operator: intersect
          match:
            - keynote
      params:
        preventTitleLinks: true
        small: false
        grouped: true
        sortBy:
          param: order
    - partial: content
      content:
        page: workshop/program.md
    - partial: content
      content:
        page: workshop/contribute.md
    - partial: content
      content:
        page: workshop/about.md
    - title: Organizers
      partial: list
      content:
        page: people
        param: pages
        where:
          key: Params.groups
          operator: intersect
          match:
            - organizer
      params:
        preventTitleLinks: true
        small: true
        sortBy:
          param: order
    - title: Program Committee
      partial: list
      content:
        page: people
        param: pages
        where:
          key: Params.groups
          operator: intersect
          match:
            - reviewer
      params:
        preventTitleLinks: true
        small: true

links:
  - icon: fa-brands fa-x-twitter
    href: https://x.com/biodatasys
    large: true
    label: Follow
    # title: Follow
  - icon: fa-brands fa-discord
    href: https://discord.gg/mR7Fmh9JtG
    large: true
    label: Chat
    # title: Chat
  # - icon: fa-solid fa-person-chalkboard
  #   href: '#invited-speakers'
  #   large: true
  #   label: Speakers
  - icon: fa-solid fa-file-circle-plus
    href: https://openreview.net/group?id=VLDB.org/2026/Workshop/BioDMS
    large: true
    label: Submit
    # title: Submit
  - icon: fa-solid fa-ticket
    href: https://vldb.org/2026/registration.html
    large: true
    label: Register
    # title: Attend
  - icon: fa-envelope
    href: "mailto:chairs@biodms.org"
    obfuscate: true
    large: true
    label: Contact
    # title: Contact
---
