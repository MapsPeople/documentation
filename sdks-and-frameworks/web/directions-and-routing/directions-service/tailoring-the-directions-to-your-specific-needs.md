---
description: by leveraging the avoidHighwayTypes and excludeHighwayTypes parameters.
---

# Tailoring the directions to your specific needs

### This is a continuation of the [Directions Service](./) article

Navigating within buildings and complexes can be a challenge, especially when considering various obstacles and preferences. The new `avoidHighwayTypes` and `excludeHighwayTypes` parameters empower you to customize your indoor routes to suit your specific needs, ensuring a seamless and accessible journey.

**Supported Highway Types:**

The `avoidHighwayTypes` and `excludeHighwayTypes` parameters support a range of path types commonly found within venues, allowing you to fine-tune your route to your preferences: `'ramp'`, `'stairs'`, `'ladder'`, `'escalator'`, `'travelator'`, `'elevator'`, `'unclassified'`, `'residential'`, `'footway'`, `'wheelchairramp'`, and `'wheelchairlift'`.

**Avoiding Specific Path Types:**

The `avoidHighwayTypes` parameter allows you to specify path types that you prefer to avoid within a venue. For instance, if you're pushing a stroller or carrying heavy luggage, you might want to avoid stairs and escalators. To do so, simply add the path types you wish to avoid to the `avoidHighwayTypes` parameter when calling the `getRoute` method.

Example:

```javascript
miDirectionsServiceInstance.getRoute({
  origin: { lat: 38.897389429704695, lng: -77.03740973527613, floor: 0 },
  destination: { lat: 38.897579747054046, lng: -77.03658652944773, floor: 1 },
  avoidHighwayTypes: ['stairs', 'escalator']
});
```

**Excluding Path Types Entirely:**

The `excludeHighwayTypes` parameter takes customization a step further, allowing you to completely exclude certain path types from your route. This means that the DirectionsService will never consider these path types as part of your journey, ensuring that your route aligns with your preferences.

Example:

<pre class="language-javascript"><code class="lang-javascript"><strong>miDirectionsServiceInstance.getRoute({
</strong>  origin: { lat: 38.897389429704695, lng: -77.03740973527613, floor: 0 },
  destination: { lat: 38.897579747054046, lng: -77.03658652944773, floor: 1 },
  excludeHighwayTypes: ['stairs', 'escalator']
});
</code></pre>

**Accessibility Considerations:**

The `avoidHighwayTypes` and `excludeHighwayTypes` parameters are particularly useful for individuals with accessibility needs. For example, if you are unable to use stairs or escalators, you can add `excludeHighwayTypes: ['stairs', 'escalator']` to your `getRoute` call. This will ensure that the DirectionsService provides you with a route that doesn't include these obstacles.

#### The `avoidHighwayTypes` and `excludeHighwayTypes` parameters take priority over `avoidStairs`

The `avoidStairs` parameter will be disregarded if either `avoidHighwayTypes` or `excludeHighwayTypes` is specified. This approach stems from the fact that `avoidHighwayTypes` and `excludeHighwayTypes` provide a more granular level of control over which path types to exclude.

Example:

<pre class="language-javascript"><code class="lang-javascript"><strong>miDirectionsServiceInstance.getRoute({
</strong>  origin: { lat: 38.897389429704695, lng: -77.03740973527613, floor: 0 },
  destination: { lat: 38.897579747054046, lng: -77.03658652944773, floor: 1 },
  avoidStairs: true,
  excludeHighwayTypes: ['wheelchairramp']
});
</code></pre>

#### Unrestricted routes

To get a route that isn't restricted in any way, assign an empty array to either `avoidHighwayTypes` or `excludeHighwayTypes`

Example:

<pre class="language-javascript"><code class="lang-javascript"><strong>miDirectionsServiceInstance.getRoute({
</strong>  origin: { lat: 38.897389429704695, lng: -77.03740973527613, floor: 0 },
  destination: { lat: 38.897579747054046, lng: -77.03658652944773, floor: 1 },
  excludeHighwayTypes: []
});
</code></pre>

With these new parameters, you have the power to tailor your indoor routes to your specific needs and preferences, whether it's avoiding stairs for easier access, prioritizing accessibility, or simply navigating through a venue with ease.
