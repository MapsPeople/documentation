# Project Setup

This guide is made based off a basic application already made, to keep most of the code specific to the MapsIndoors SDK. So that we don't need to spend too much time modifying or implementing UI elements. If confident, you can start off by creating your own project, and just taking the context of the different steps of the getting started guide. A short setup of a React native application with MapsIndoors is described below.

{% tabs %}
{% tab title="Basic example" %}
### Using basic example[​](https://docs.mapsindoors.com/getting-started/React%20Native/project-setup#using-basic-example) <a href="#using-basic-example" id="using-basic-example"></a>

#### Set Up Your Environment[​](https://docs.mapsindoors.com/getting-started/React%20Native/project-setup#set-up-your-environment) <a href="#set-up-your-environment" id="set-up-your-environment"></a>

Read here how to set up your [**React Native development environment**](https://reactnative.dev/docs/environment-setup)

#### Basic Example[​](https://docs.mapsindoors.com/getting-started/React%20Native/project-setup#basic-example) <a href="#basic-example" id="basic-example"></a>

You can find the basic example to start from here: [Basic example](https://github.com/MapsPeople/getting-started-react-native-basic) Follow the included README on how to get the project up and running.

#### Setup MapsIndoors[​](https://docs.mapsindoors.com/getting-started/React%20Native/project-setup#setup-mapsindoors) <a href="#setup-mapsindoors" id="setup-mapsindoors"></a>

To run MapsIndoors you need to insert a google API key inside your project.

**Android:**

Inside the project navigate to `android/app/src/main/res/values/google_maps_api.xml` and replace the `INSERT_KEY_HERE` with your google API key

**iOS:**

Inside the project navigate to `ios/GettingStartedProject/AppDelegate.mm` and replace the `/*Your Google API key here*/` inside `provideAPIKey` with your Google API key
{% endtab %}

{% tab title="Manual setup" %}
#### Manual setup[​](https://docs.mapsindoors.com/getting-started/React%20Native/project-setup#manual-setup) <a href="#manual-setup" id="manual-setup"></a>

**Project creation**[**​**](https://docs.mapsindoors.com/getting-started/React%20Native/project-setup#project-creation)

Create a new React Native project by running the following command

```shell
npx react-native@latest init GettingStartedProject
```

**Dependencies**[**​**](https://docs.mapsindoors.com/getting-started/React%20Native/project-setup#dependencies)

Start by installing the bindings for your preferred map provider. There is currently only support for using one of these packages at a time.

```bash
npm install @mapsindoors/react-native-maps-indoors-google-maps
# npm install @mapsindoors/react-native-maps-indoors-mapbox
```

The getting started project makes use of the following publicly available UI components. We recommend using them while following the getting started guide.

```bash
# Bottomsheet
npm install @gorhom/bottom-sheet@^4

# Navigation
npm install @react-navigation/native @react-navigation/native-stack
npm install react-native-screens react-native-safe-area-context
```

The above components make use of the following dependecies, if using React Native 0.59 or below you will also need to link them, you can read how to below.

```bash
# Dependencies
npm install react-native-reanimated react-native-gesture-handler
```

Please see the following links for additional steps needed for installing the dependencies:\
[https://docs.swmansion.com/react-native-gesture-handler/docs/installation/](https://docs.swmansion.com/react-native-gesture-handler/docs/installation/)\
[https://docs.swmansion.com/react-native-reanimated/docs/fundamentals/installation/](https://docs.swmansion.com/react-native-reanimated/docs/fundamentals/installation/)

**Pod install**[**​**](https://docs.mapsindoors.com/getting-started/React%20Native/project-setup#pod-install)

After making changes to dependecies, especially those involving native code, you will need to run the following command from the ios folder of your project:

```bash
pod install
```

Alternatively you can install the npm package `pod-install` and then run the following command from anywhere within the project:

```bash
npx pod-install
```
{% endtab %}
{% endtabs %}

### Code complete example[​](https://docs.mapsindoors.com/getting-started/React%20Native/project-setup#code-complete-example) <a href="#code-complete-example" id="code-complete-example"></a>

During the guide you can use this example to refer to your project, or just play around with the code inside: [Getting started](https://github.com/MapsPeople/getting-started-react-native)

### Using Mapbox[​](https://docs.mapsindoors.com/getting-started/React%20Native/project-setup#using-mapbox) <a href="#using-mapbox" id="using-mapbox"></a>

While the examples are using google maps, it is also possible to use Mapbox instead with only minor changes to the project.

To do this follow the getting started of this page [MapsIndoors Mapbox](https://www.npmjs.com/package/@mapsindoors/react-native-maps-indoors-mapbox) and replace any imports from `@mapsindoors/react-native-maps-indoors-google-maps` to `@mapsindoors/react-native-maps-indoors-mapbox` after this you is done, you can follow the getting started guide without any changes.
