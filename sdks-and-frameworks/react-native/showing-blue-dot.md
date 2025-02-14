# Showing blue dot

Often it is necesarry to show the users position to deliver valuable context on the map. We do not offer any positioning inside the MapsIndoors SDK. But do deliver a positioning interface that allows you to hook up positioning data on the map.

Create a class that implements the `MPPositionProviderInterface`.

```tsx
import { MPPoint, MPPositionProviderInterface, MPPositionResultInterface, OnPositionUpdateListener } from "@mapsindoors/react-native-maps-indoors-mapbox";
import { MyPositionResult } from "./MyPositionResult";

export class MyPositionProvider implements MPPositionProviderInterface {
    positionUpdateListeners: OnPositionUpdateListener[] = new Array()
    latestPosition?: MyPositionResult;

    addOnPositionUpdateListener(listener: OnPositionUpdateListener) {
        this.positionUpdateListeners.push(listener);
    }

    removeOnPositionUpdateListener(listener: OnPositionUpdateListener) {
        this.positionUpdateListeners.filter(item => item != listener);
    }

    getLatestPosition(): MPPositionResultInterface | undefined {
        return this.latestPosition;
    }

    get name(): string {
        return "MyPositionProvider";
    }
}
```

Note the variable `latestPosition` we store on the position provider is a `MyPositionResult`.
Go ahead and implement that class, this class implements the `MPPositionResultInterface`, and is used to assign data like the users current floor index, the accuracy that might be known from the used position provider, user bearing etc.

```tsx
import { MPPoint, MPPositionResultInterface } from "@mapsindoors/react-native-maps-indoors-mapbox";

export class MyPositionResult implements MPPositionResultInterface {
    point: MPPoint;
    floorIndex: number;
    bearing: number;
    accuracy: number;
    positionProvider: string;

    constructor(point: MPPoint, positionProvider: string) {
        this.point = point;
        this.positionProvider = positionProvider;
        this.floorIndex = 0;
        this.bearing = 0;
        this.accuracy = 0;
    }
}
```

Inside the `MyPositionProvider` class we will now implement a `updatePosition` method that receives a `MPPoint` we can then show on our map by hooking it up with MapsIndoors.

```tsx
updatePosition(point: MPPoint) {
    this.latestPosition = new MyPositionResult(point, this.name);
    this.positionUpdateListeners.forEach((listener) => {
        listener(this.latestPosition!);
    });
}
```

We can now hook our created position provider up with `MapsIndoors`

```tsx
//Creating the position provider
let positionProvider = new MyPositionProvider();
//Assigning the position provider to MapsIndoors
await MapsIndoors.setPositionProvider(positionProvider);
//Letting our current MapControl instance know that it should show user position
mapControl.showUserPosition(true);
//Calling update position on the position provider with a random coordinate within an area.
positionProvider.updatePosition(new MPPoint(57.0582701 + (Math.random() - 0.5) / 2500, 9.9508396 + (Math.random() - 0.5) / 2500));
```

The point that we send to the update positon can be replaced and hooked into any positioning library callback, and by that you will have positioning shown inside your app on the map.