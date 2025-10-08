---
description: Turn spatial data into intelligent assistance
icon: robot
cover: ../.gitbook/assets/chatbot.jpg
coverY: 0
layout:
  width: default
  cover:
    visible: true
    size: hero
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
  metadata:
    visible: true
---

# How to Build a Location-Aware Chatbot using Spatial Data

You already have spatial data. Put it to work. Use it to build an assistant that helps people move, decide, and act inside your spaces.

MapsIndoors lets you build applications that use spatial data like locations, routes, floor plans, and POIs to create responsive indoor environments. With the addition of generative AI, this spatial data serves as a foundation for building conversational assistants that understand indoor spaces, respond to user needs, and deliver timely, contextually relevant help.

This guide demonstrates how to create a chatbot that integrates MapsIndoors with a large language model (LLM) to activate spatial data within your product. This example uses Google’s Gemini, but similar setups can be built with OpenAI, Claude, or other LLMs.

This article includes:

* What you need to build it
* How it works: 5-step guide
* Examples of spatial assistance
* Expand to other use cases
* Factors that shape chatbot quality
* Next steps

***

## What you need to build it

* A MapsIndoors API key
* An API key for Gemini (or another LLM with function-calling)
* A simple HTML-based UI (provided in the sample)

Sample code:[ https://github.com/MapsPeople/mapsindoors-gemini-chatbot-example](https://github.com/MapsPeople/mapsindoors-gemini-chatbot-example)

***

## How it works

The architecture has five core parts:

1. UI layer
2. Tools for spatial interaction
3. AI service to manage prompts and responses
4. Tool execution logic using MapsIndoors SDK
5. Conversation orchestration

Each part is explained below.

***

### 1.Set up the configuration

The chat UI is a simple HTML page. There’s a UI part in the sample code that you can use if it is helpful for you.&#x20;

To configure it: Add your MapsIndoors and Gemini API keys:

```javascript
const YOUR_GEMINI_API_KEY = "YOUR_GEMINI_API_KEY_HERE";
const MAPSINDOORS_API_KEY = "YOUR_MAPSINDOORS_API_KEY_HERE";
```

Security note: The Gemini API key is exposed in client-side code for demo purposes. In production, handle it securely on a backend server.

***

### 2. Define tools for spatial interaction

To make the LLM interact with MapsIndoors, define the tools it can use. These tools are simple JSON objects describing functions like location search and routing.

Example:

```javascript
const aiTools = [
  {
    name: "get_locations",
    description: "Search for locations using parameters like name, floor, or proximity.",
    parameters: { /* query parameters */ }
  },
  {
    name: "get_route",
    description: "Get directions between two points.",
    parameters: { /* origin, destination, travelMode */ }
  }
];
```

These tools are passed to the LLM, which can choose to call them when appropriate.

**To go further:** You can extend the data that you store within MapsIndoors and add tools for retrieving events, occupancy status, restaurant menus, and more, stored as Custom Properties  to support more advanced use cases.

***

### 3. Build the AI service

The AIService class sends prompts and receives responses from the LLM. It includes:

* **System instructions**: Define the assistant’s tone and rules.
* **Context**: Provide spatial data like venue, floor, location names, etc.
* **Prompt history**: Maintain conversation state.
* **Tool definitions**: Enable tool use in replies.



Core method:

```javascript
async sendPrompt(history, tools = []) {
  const payload = {
    system_instruction: {
      parts: [
        { text: "You are a helpful assistant for MapsIndoors..." },
        { text: `CONTEXT: ${JSON.stringify(this.context)}` }
      ]
    },
    contents: history,
    tools: tools.length ? [{ functionDeclarations: tools }] : undefined
  };

  // Send request to Gemini API and parse response...
}
```

If you use another LLM, adjust endpoint URLs and payload formats accordingly.

***

### 4. Execute tool calls

When the LLM returns a function call, your app must call the right MapsIndoors service and return a structured response.\
\
In the function example below:

* It receives the functionCall from Gemini.
* Identifies the tool name and its arguments.
* Invokes the corresponding MapsIndoors SDK service.
* Packages the SDK's output into a functionResponse object, which is the format Gemini expects for a tool's result. This result is then sent back to Gemini in the next turn of the conversation.

Example:

```javascript
async function executeToolCall(functionCall) {
  const { name, args } = functionCall;

  if (name === "get_locations") {
    const locations = await mapsindoors.services.LocationsService.getLocations(args);
    return { functionResponse: { name, response: { toolRawResult: locations } } };
  }

  if (name === "get_route") {
    const directionsService = new mapsindoors.services.DirectionsService();
    const route = await directionsService.getRoute(args);
    return { functionResponse: { name, response: { toolRawResult: route } } };
  }
}

```

Add additional logic to handle errors or extended tools.

***

### 5. Orchestrate the conversation

The **sendMessage** function ties it all together:

1. Add the user’s message to the chat history
2. Call **sendPrompt()** with history and tools
3. If the response is text, show it
4. If response is a functionCall:

* Execute tool using **executeToolCall()**
* Add tool response to history
* Call **sendPrompt()** again to get the final reply

The result is a multi-turn, context-aware conversation.

***

### Examples of spatial assistance

Powered by your spatial data, this setup allows your chatbot to:

**Answer natural language questions like:**

* “Where is the nearest restroom with no stairs from here?”
* “How do I get to meeting room B from the entrance?”

**Provide contextual suggestions:**

* “You’re near the main exhibit. A session starts in 10 minutes.”

**Adjust behavior based on the current floor or location**

**Support follow-up questions and clarifications**

***

### Expand to other use cases

Here are other ways to use this pattern:

* Accessibility navigation: Voice-guided directions with elevator-only paths
* Event support: Recommend food stands or sessions based on time and location
* Facility management: Identify usage trends from query logs
* Emergency response: Guide staff to incidents using real-time routes
* Concierge services: Combine navigation and notifications

Each use case builds on the same foundation: spatial context and generative AI. Spatial context can originate from many sources—event schedules, sensor data, room availability systems, crowd monitoring, or booking platforms. You can define tools that fetch or combine this third-party data to give the assistant even deeper insight.

If you're moving toward intelligent use cases like the examples above, consider:

* Extending your tool definitions to query external systems
* Feeding the assistant real-time or scheduled data through context or function calls
* Including dynamic triggers like time, motion, or capacity thresholds

This lets your assistant respond to the full experience of a venue, not just where things are, but what's happening and how users should act on it.

***

### Factors that shape chatbot quality

The accuracy and usefulness of the assistant depend on several factors:

* **LLM choice**\
  Different models vary in reasoning ability, response quality, speed, and support for tool use. Most support the same architecture, but performance may vary.
* **Enrichment of spatial data**\
  The more metadata and structured information added to your MapsIndoors solution names, categories, descriptions, custom properties, the more specific and helpful the assistant can be.
* **Context provided the LLM**\
  If the assistant is aware of venue, floor etc. it will make it easier for the LLM to look up the right data and be coherent in its responses. Eg. if you ask for a location on a specific floor, it doesn't look up the floor through a language-based approach, but uses the context of floors to find the corresponding floor index in the spatial data.
* **System Prompt and Tool clarity**\
  Modifying the “system\_instruction”, or system prompt that is sent to the LLM can greatly improve the usefullness and relevance of answers. Well-defined tool schemas help the LLM understand what it can do. Vague or missing tool definitions and descriptions limit its ability to act on intent.

These elements are often configured individually and depend on how each solution is set up. In many cases, it's up to the developer or customer to make sure the assistant has access to the right tools, data, and context.

***

### Next steps

* Start with the provided sample and test core functionality
* Extend the toolset to reflect real-world data
* Try the same setup with other LLMs (Claude, GPT-4) with minor changes

With spatial data activated, your assistant can do more than answer questions. It can support how people experience indoor spaces.

***

For full code:[ https://github.com/MapsPeople/mapsindoors-gemini-chatbot-example](https://github.com/MapsPeople/mapsindoors-gemini-chatbot-example)
