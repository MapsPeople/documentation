# Getting Started

An introduction on how to get started with the Map Template project. Read the guide or watch our web developer Andreea implement it live!

Node version

We recommend using the latest LTS version of [Node.js](https://nodejs.org/en/).



{% embed url="https://youtu.be/ILnqlCTVhr4?si=F7RnYGgtk2Tqqu5b" %}

## 1. Getting Started[​](https://docs.mapsindoors.com/web-map-template#getting-started)

Using a terminal/shell in the project folder, run the following commands:

* Clone the [repository](https://github.com/MapsPeople/web-ui) to your development environment. You can use `SSH` or `HTTPS` to clone the repository, depending on which one you are more familiar with.

```bash
git clone https://github.com/MapsPeople/web-ui.git
```

* Inside the project folder root, install all dependencies and build with Lerna:

```bash
npm install && npx lerna run build
```

* Move to the `packages/map-template` directory and run the app:

```bash
npm run start
```

Now open the app served on [http://localhost:3000/](http://localhost:3000/).

## 2. Add Google Maps API Keys or Mapbox Access Tokens[​](https://docs.mapsindoors.com/web-map-template#add-google-maps-api-keys-or-mapbox-access-tokens) <a href="#add-google-maps-api-keys-or-mapbox-access-tokens" id="add-google-maps-api-keys-or-mapbox-access-tokens"></a>

Rename `.env.example`, to `.env` and add either of the keys (or both) to that file, and the maps will load properly.

## Useful links:

[https://github.com/MapsPeople/web-ui](https://github.com/MapsPeople/web-ui)\
[https://lerna.js.org/](https://lerna.js.org/)
