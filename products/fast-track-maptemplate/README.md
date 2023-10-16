---
layout:
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# Map Template

In under 10 minutes, you can seamlessly incorporate MapsIndoors into your system. We have developed all the required UI components and bundled them together for your convenience.

Whether your map is a stand-alone solution, or part of an existing app, the open source code repository for our Map Template is your fast track to a ready-to-use map implementation.

The app is written in React, and you will find the repository on GitHub here: [github.com/mapspeople/web-ui](https://github.com/mapspeople/web-ui).

The Map Template is built with configuration and customization in mind. Configuration options can be set as url parameters, or directly on the React Components themselves. Moreover, the whole Map Template is exported as a Web Component too, and you can set properties on that as well to control the behaviour of the app. [Read more about Configuration options](configuration/).

A common use case for the Map Template is to include the Web Component in a simple html file, set the properties for how the app should look and behave by default. Then you can use url parameters to trigger changes in the app according to other input. [Read more about the Web Component.](getting-started/web-component.md)

If you want to customize the Map Template further than what the configuration options allow you to, the repository is published under a permissive license, including for commercial purposes. We recommend forking the repository, and creating a branch on your copy of the repo. This way you can easily pull updates from the `main` branch into your fork without creating disruptive merge conflicts.

