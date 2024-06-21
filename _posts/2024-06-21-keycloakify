---
layout: post
title: Keycloakify
categories: keycloakify
---

### Testing your theme

link: https://docs.keycloakify.dev/testing-your-theme/in-storybook

### In Storybook

- TLDR:

```java
npx keycloakify add-story
npm run storybook
```

Storybook is a tool that enables to test UI component in isolation. For reference, (*de tham khao*) the component showcase Keycloakify website is a Storybook instrance:
https://storybook.keycloakify.dev/?path=/story/introduction--page

The starter template does not initially contain any story files, instead there's a keycloakify CLI command that let's you import specifically the stories for the pages you want to test into your project

So, just run this command in the root of your Keycloakify project and select the pages you want.  

- *npx keycloakify add-stories*

It will enables you to select the pages you want to add stories for. 

You can run this command multiple times to add stories for multiple pages.

![_config.yml]({{ site.baseurl }}/images/kcf-01.png)

Selecting login -> register.ftl will result in this file to be created in your project:

![_config.yml]({{ site.baseurl }}/images/kcf-02.png)

You can run the above command multiple times to add stories for the different pages you want to develop.

Once your added a few stories you can start Storybook locally with:

![_config.yml]({{ site.baseurl }}/images/kcf-03.png)

You can see the changes you make in you code in realtime in your Storybook.  

The idea of Storybook is to easyly let you see the pages in different configuration without having to reproduce the full login/register cinematic in a real Keycloak.  

Keycloakify provide a default mock context for every pages, the stories let you partially (*mot phan*) overrride some specific (*cu the*) part of this default mock to reflect (*phan anh*) pages in different configurations.  

For example, if you want to create a story that show the register page in chinese you would add this:

![_config.yml]({{ site.baseurl }}/images/kcf-04.png)

![_config.yml]({{ site.baseurl }}/images/kcf-05.png)

That's really nice, however this approach has it's limits. At some point you'll want to test in a real Keycloak to make sure everything works. Also you don't nessesary know the kcContext values to provides in order to replicate a desired configuration.  

Don't worry! Keycloakify got you covered by letting you test in a local Keycloak Docker container.